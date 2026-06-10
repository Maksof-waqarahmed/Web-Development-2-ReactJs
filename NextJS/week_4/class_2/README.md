# 🛣️ Route Handlers (API Routes)

## 📚 Topics Covered
- What are Route Handlers
- `route.js` — creating API endpoints
- `GET`, `POST`, `PUT`, `PATCH`, `DELETE` methods
- `NextRequest` and `NextResponse`
- Reading URL params, search params, headers, cookies
- Dynamic Route Handlers — `[id]`
- Returning JSON responses
- CORS headers
- Building a REST API inside Next.js

---

## 1️⃣ What are Route Handlers?

Route Handlers are **API endpoints** inside your Next.js app. Instead of a separate Express/Node server, you can define `GET`, `POST`, `PUT`, `DELETE` handlers directly in the `app/` directory.

```
app/
└── api/
    ├── users/
    │   ├── route.js          →  GET /api/users, POST /api/users
    │   └── [id]/
    │       └── route.js      →  GET /api/users/1, PUT /api/users/1, DELETE /api/users/1
    └── posts/
        └── route.js          →  GET /api/posts, POST /api/posts
```

> Route Handlers live in a `route.js` file. They **cannot** coexist with `page.jsx` in the same folder.

---

## 2️⃣ Basic GET Handler

```js
// app/api/users/route.js
import { NextResponse } from "next/server";

const users = [
  { id: 1, name: "Ali Hassan", email: "ali@example.com", role: "admin" },
  { id: 2, name: "Sara Khan", email: "sara@example.com", role: "user" },
  { id: 3, name: "Ahmed Raza", email: "ahmed@example.com", role: "user" },
];

// GET /api/users
export async function GET(request) {
  const { searchParams } = new URL(request.url);
  const role = searchParams.get("role"); // /api/users?role=admin

  const filtered = role ? users.filter((u) => u.role === role) : users;

  return NextResponse.json(filtered);
}
```

Test it: `http://localhost:3000/api/users` or `http://localhost:3000/api/users?role=admin`

---

## 3️⃣ POST Handler — Create

```js
// app/api/users/route.js
import { NextResponse } from "next/server";

let users = [
  { id: 1, name: "Ali Hassan", email: "ali@example.com" },
];

export async function GET() {
  return NextResponse.json(users);
}

// POST /api/users
export async function POST(request) {
  const body = await request.json();

  // Validate
  if (!body.name || !body.email) {
    return NextResponse.json(
      { error: "Name and email are required" },
      { status: 400 }
    );
  }

  const newUser = {
    id: users.length + 1,
    name: body.name,
    email: body.email,
  };

  users.push(newUser);

  return NextResponse.json(newUser, { status: 201 });
}
```

---

## 4️⃣ Dynamic Route Handler — GET, PUT, DELETE by ID

```js
// app/api/users/[id]/route.js
import { NextResponse } from "next/server";

let users = [
  { id: 1, name: "Ali Hassan", email: "ali@example.com" },
  { id: 2, name: "Sara Khan", email: "sara@example.com" },
];

// GET /api/users/1
export async function GET(request, { params }) {
  const id = Number(params.id);
  const user = users.find((u) => u.id === id);

  if (!user) {
    return NextResponse.json({ error: "User not found" }, { status: 404 });
  }

  return NextResponse.json(user);
}

// PUT /api/users/1
export async function PUT(request, { params }) {
  const id = Number(params.id);
  const body = await request.json();
  const index = users.findIndex((u) => u.id === id);

  if (index === -1) {
    return NextResponse.json({ error: "User not found" }, { status: 404 });
  }

  users[index] = { ...users[index], ...body };
  return NextResponse.json(users[index]);
}

// DELETE /api/users/1
export async function DELETE(request, { params }) {
  const id = Number(params.id);
  const index = users.findIndex((u) => u.id === id);

  if (index === -1) {
    return NextResponse.json({ error: "User not found" }, { status: 404 });
  }

  const deleted = users[index];
  users = users.filter((u) => u.id !== id);

  return NextResponse.json({ message: "User deleted", user: deleted });
}
```

---

## 5️⃣ Reading Headers and Cookies

```js
// app/api/profile/route.js
import { NextRequest, NextResponse } from "next/server";
import { cookies, headers } from "next/headers";

export async function GET(request) {
  // Method 1: from request object
  const authHeader = request.headers.get("Authorization");

  // Method 2: from next/headers (server-side)
  const headersList = headers();
  const userAgent = headersList.get("user-agent");

  const cookieStore = cookies();
  const token = cookieStore.get("token");

  if (!token) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  return NextResponse.json({
    message: "Profile data",
    token: token.value,
    userAgent,
  });
}
```

---

## 6️⃣ Setting Cookies in Response

```js
// app/api/login/route.js
import { NextResponse } from "next/server";

export async function POST(request) {
  const { email, password } = await request.json();

  // Fake auth check
  if (email !== "admin@test.com" || password !== "123456") {
    return NextResponse.json({ error: "Invalid credentials" }, { status: 401 });
  }

  const response = NextResponse.json({ message: "Logged in successfully" });

  // Set a cookie
  response.cookies.set("token", "fake-jwt-token-here", {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    maxAge: 60 * 60 * 24 * 7, // 7 days
    path: "/",
  });

  return response;
}
```

---

## 7️⃣ CORS Headers

```js
// app/api/public/route.js
import { NextResponse } from "next/server";

export async function GET() {
  const data = { message: "Public API data" };

  return NextResponse.json(data, {
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Authorization",
    },
  });
}

// Handle OPTIONS preflight
export async function OPTIONS() {
  return new NextResponse(null, {
    status: 200,
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Authorization",
    },
  });
}
```

---

## 8️⃣ Complete REST API Example — Posts

```js
// app/api/posts/route.js
import { NextResponse } from "next/server";

let posts = [
  { id: 1, title: "First Post", body: "Hello World", userId: 1 },
  { id: 2, title: "Second Post", body: "Next.js is awesome", userId: 1 },
];

export async function GET(request) {
  const { searchParams } = new URL(request.url);
  const userId = searchParams.get("userId");

  const result = userId
    ? posts.filter((p) => p.userId === Number(userId))
    : posts;

  return NextResponse.json(result);
}

export async function POST(request) {
  const body = await request.json();

  if (!body.title || !body.body) {
    return NextResponse.json({ error: "Title and body required" }, { status: 400 });
  }

  const newPost = { id: Date.now(), ...body };
  posts.push(newPost);

  return NextResponse.json(newPost, { status: 201 });
}
```

```js
// app/api/posts/[id]/route.js
import { NextResponse } from "next/server";

export async function GET(request, { params }) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${params.id}`);
  if (!res.ok) return NextResponse.json({ error: "Not found" }, { status: 404 });
  const post = await res.json();
  return NextResponse.json(post);
}
```

---

## 🎯 Interview Questions

**Q1: What is the difference between a Server Action and a Route Handler?**

> Server Actions are for form submissions and mutations from the UI — they work with `<form action={...}>` and don't need a separate HTTP call. Route Handlers are traditional API endpoints accessible via `fetch("/api/...")` — used for external consumers, REST APIs, or when you need more HTTP control (custom headers, CORS, cookies).

**Q2: How do you access URL parameters in a Route Handler?**

> Dynamic params come via the second argument: `function GET(request, { params })` where `params.id` is the dynamic segment. Query params come via `new URL(request.url).searchParams.get("key")`.

**Q3: What is `NextResponse` and why use it over `Response`?**

> `NextResponse` extends the standard `Response` with Next.js-specific helpers like `.cookies.set()`, `.redirect()`, and `.rewrite()`. For simple JSON responses, both work, but `NextResponse.json(data, { status })` is more convenient than manually setting headers.

**Q4: Can a `route.js` and `page.jsx` exist in the same folder?**

> No. A `route.js` and `page.jsx` cannot be in the same directory because they both respond to requests at the same URL — Next.js would not know which to use. Typically, API routes live under `app/api/` to avoid this conflict.

---

## 🏠 Home Task

Build a complete **REST API** inside Next.js:
1. `GET /api/products` — return all products
2. `POST /api/products` — create a new product (validate: name, price required)
3. `GET /api/products/[id]` — return single product (return 404 if not found)
4. `PUT /api/products/[id]` — update a product
5. `DELETE /api/products/[id]` — delete a product
6. Test all endpoints using a browser fetch or Postman
7. Add search: `GET /api/products?category=electronics` filters by category
