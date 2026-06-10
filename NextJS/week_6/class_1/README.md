# 🗄️ Database Integration with Prisma

## 📚 Topics Covered
- What is Prisma ORM
- Installation and setup
- `schema.prisma` — defining data models
- Prisma migrations — `prisma migrate dev`
- Prisma Client — `findMany`, `findUnique`, `create`, `update`, `delete`
- Connecting to SQLite (local) and PostgreSQL
- Using Prisma in Server Components and Route Handlers
- Relations — one-to-many

---

## 1️⃣ What is Prisma?

**Prisma** is a modern **ORM (Object-Relational Mapper)** that lets you interact with your database using TypeScript/JavaScript instead of raw SQL.

```
Without Prisma:
  const users = await db.query("SELECT * FROM users WHERE id = $1", [id]);

With Prisma:
  const user = await prisma.user.findUnique({ where: { id } });
```

Prisma supports: **PostgreSQL, MySQL, SQLite, MongoDB, SQL Server**

---

## 2️⃣ Installation

```bash
npm install prisma @prisma/client
npx prisma init --datasource-provider sqlite  # or postgresql
```

This creates:
- `prisma/schema.prisma` — your database schema
- `.env` — with `DATABASE_URL`

---

## 3️⃣ `schema.prisma` — Defining Models

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"  // or "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  password  String
  role      String   @default("user")
  createdAt DateTime @default(now())
  posts     Post[]   // one user has many posts
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  body      String
  published Boolean  @default(false)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id])
}
```

**.env file:**

```bash
DATABASE_URL="file:./dev.db"  # SQLite
# DATABASE_URL="postgresql://user:password@localhost:5432/mydb"  # PostgreSQL
```

---

## 4️⃣ Prisma Migrations

```bash
# Create migration + update database
npx prisma migrate dev --name init

# View your database in browser
npx prisma studio

# Reset database
npx prisma migrate reset

# Generate Prisma Client (after schema changes)
npx prisma generate
```

---

## 5️⃣ Prisma Client Setup

```js
// lib/db.js
import { PrismaClient } from "@prisma/client";

// Prevent multiple instances in development (hot reload issue)
const globalForPrisma = global;
const prisma = globalForPrisma.prisma ?? new PrismaClient();

if (process.env.NODE_ENV !== "production") {
  globalForPrisma.prisma = prisma;
}

export default prisma;
```

---

## 6️⃣ CRUD Operations with Prisma

### Read — Find All

```js
import prisma from "@/lib/db";

// Get all users
const users = await prisma.user.findMany();

// Get with sorting and filtering
const activeUsers = await prisma.user.findMany({
  where: { role: "user" },
  orderBy: { createdAt: "desc" },
  take: 10,      // limit
  skip: 0,       // offset
});
```

### Read — Find One

```js
// Find by unique field
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: { posts: true }, // include related posts
});

// Find first match
const admin = await prisma.user.findFirst({
  where: { role: "admin" },
});
```

### Create

```js
const newUser = await prisma.user.create({
  data: {
    name: "Ali Hassan",
    email: "ali@example.com",
    password: hashedPassword,
    role: "user",
  },
});
```

### Update

```js
const updatedUser = await prisma.user.update({
  where: { id: 1 },
  data: {
    name: "Ali Hassan Updated",
    role: "admin",
  },
});
```

### Delete

```js
const deletedUser = await prisma.user.delete({
  where: { id: 1 },
});
```

---

## 7️⃣ Prisma in Server Components

```jsx
// app/users/page.jsx — Server Component
import prisma from "@/lib/db";

export default async function UsersPage() {
  const users = await prisma.user.findMany({
    orderBy: { createdAt: "desc" },
    select: {
      id: true,
      name: true,
      email: true,
      role: true,
      createdAt: true,
    },
  });

  return (
    <div style={{ padding: 24 }}>
      <h1>Users ({users.length})</h1>
      <table style={{ width: "100%", borderCollapse: "collapse" }}>
        <thead>
          <tr>
            <th style={{ textAlign: "left", padding: "8px 12px", borderBottom: "2px solid #eee" }}>Name</th>
            <th style={{ textAlign: "left", padding: "8px 12px", borderBottom: "2px solid #eee" }}>Email</th>
            <th style={{ textAlign: "left", padding: "8px 12px", borderBottom: "2px solid #eee" }}>Role</th>
          </tr>
        </thead>
        <tbody>
          {users.map((user) => (
            <tr key={user.id}>
              <td style={{ padding: "8px 12px", borderBottom: "1px solid #eee" }}>{user.name}</td>
              <td style={{ padding: "8px 12px", borderBottom: "1px solid #eee" }}>{user.email}</td>
              <td style={{ padding: "8px 12px", borderBottom: "1px solid #eee" }}>
                <span style={{ padding: "2px 8px", background: user.role === "admin" ? "#e3f2fd" : "#f5f5f5", borderRadius: 4 }}>
                  {user.role}
                </span>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

---

## 8️⃣ Prisma in Route Handlers

```js
// app/api/users/route.js
import { NextResponse } from "next/server";
import prisma from "@/lib/db";
import bcrypt from "bcryptjs";

// GET /api/users
export async function GET() {
  const users = await prisma.user.findMany({
    select: { id: true, name: true, email: true, role: true },
  });
  return NextResponse.json(users);
}

// POST /api/users
export async function POST(request) {
  const { name, email, password } = await request.json();

  if (!name || !email || !password) {
    return NextResponse.json({ error: "All fields required" }, { status: 400 });
  }

  // Check if email exists
  const existing = await prisma.user.findUnique({ where: { email } });
  if (existing) {
    return NextResponse.json({ error: "Email already in use" }, { status: 409 });
  }

  const hashedPassword = await bcrypt.hash(password, 10);

  const user = await prisma.user.create({
    data: { name, email, password: hashedPassword },
    select: { id: true, name: true, email: true, role: true },
  });

  return NextResponse.json(user, { status: 201 });
}
```

---

## 🎯 Interview Questions

**Q1: What is Prisma and why use it?**

> Prisma is a type-safe ORM for Node.js. It generates a database client based on your schema, providing autocomplete and compile-time type checking. It's easier to write than raw SQL, avoids SQL injection by design, and supports migrations to keep your database schema in sync with code.

**Q2: What is `prisma migrate dev` and what does it do?**

> It reads your `schema.prisma`, generates a SQL migration file based on the diff from the previous schema, applies it to the database, and regenerates the Prisma Client. The `--name` flag adds a human-readable description to the migration file.

**Q3: What does `include` vs `select` do in Prisma?**

> `select` returns only the specified fields (data minimization). `include` fetches all fields of the model PLUS the specified relations. They cannot be used together in the same query.

**Q4: How do you prevent multiple Prisma Client instances in development?**

> Next.js hot-reloads modules in development, which would create a new `PrismaClient` instance on every reload, eventually exhausting the database connection pool. The fix is to cache the instance on the global object and reuse it across hot reloads.

---

## 🏠 Home Task

Set up Prisma with SQLite and build a **User Management App**:
1. Initialize Prisma with SQLite
2. Create a `User` model with: `id`, `name`, `email`, `password`, `role`, `createdAt`
3. Run `prisma migrate dev --name init`
4. Create a seed script `prisma/seed.js` that creates 5 sample users
5. Build a `/users` page (Server Component) showing all users in a table
6. Build `GET /api/users` and `POST /api/users` Route Handlers using Prisma
7. Add a simple form on the page that creates a new user via the POST endpoint
