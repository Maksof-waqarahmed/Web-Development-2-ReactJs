# 🏗️ Full CRUD App with Prisma + Next.js

## 📚 Topics Covered
- Complete CRUD application architecture
- Server Actions with Prisma — create, update, delete
- Form handling with validation
- Optimistic UI with `useOptimistic`
- Relations — fetching posts with author
- Seeding the database
- Error handling in Server Actions
- Full project: Blog with CRUD

---

## 1️⃣ Architecture Overview

```
app/
├── api/posts/route.js          ← REST API (GET, POST)
├── api/posts/[id]/route.js     ← REST API (GET, PUT, DELETE)
├── posts/
│   ├── page.jsx                ← List all posts (Server Component)
│   ├── new/page.jsx            ← Create post form
│   └── [id]/
│       ├── page.jsx            ← View single post
│       └── edit/page.jsx       ← Edit post form
├── actions/post-actions.js     ← Server Actions
└── lib/db.js                   ← Prisma client
```

---

## 2️⃣ Prisma Schema — Blog Models

```prisma
// prisma/schema.prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  posts     Post[]
}

model Post {
  id          Int       @id @default(autoincrement())
  title       String
  body        String
  published   Boolean   @default(false)
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt
  authorId    Int
  author      User      @relation(fields: [authorId], references: [id])
  comments    Comment[]
}

model Comment {
  id        Int      @id @default(autoincrement())
  text      String
  createdAt DateTime @default(now())
  postId    Int
  post      Post     @relation(fields: [postId], references: [id], onDelete: Cascade)
}
```

---

## 3️⃣ Server Actions

```js
// app/actions/post-actions.js
"use server";

import { revalidatePath } from "next/cache";
import { redirect } from "next/navigation";
import prisma from "@/lib/db";

export async function createPost(prevState, formData) {
  const title = formData.get("title")?.trim();
  const body = formData.get("body")?.trim();

  // Validation
  if (!title || title.length < 3) {
    return { error: "Title must be at least 3 characters" };
  }
  if (!body || body.length < 10) {
    return { error: "Body must be at least 10 characters" };
  }

  try {
    await prisma.post.create({
      data: {
        title,
        body,
        authorId: 1, // in real app: get from session
      },
    });
  } catch (err) {
    return { error: "Failed to create post. Please try again." };
  }

  revalidatePath("/posts");
  redirect("/posts");
}

export async function updatePost(id, prevState, formData) {
  const title = formData.get("title")?.trim();
  const body = formData.get("body")?.trim();

  if (!title || !body) {
    return { error: "Title and body are required" };
  }

  try {
    await prisma.post.update({
      where: { id: Number(id) },
      data: { title, body },
    });
  } catch {
    return { error: "Failed to update post" };
  }

  revalidatePath(`/posts/${id}`);
  revalidatePath("/posts");
  redirect(`/posts/${id}`);
}

export async function deletePost(id) {
  await prisma.post.delete({ where: { id: Number(id) } });
  revalidatePath("/posts");
  redirect("/posts");
}

export async function togglePublish(id, published) {
  await prisma.post.update({
    where: { id: Number(id) },
    data: { published: !published },
  });
  revalidatePath("/posts");
}
```

---

## 4️⃣ Posts List Page

```jsx
// app/posts/page.jsx
import Link from "next/link";
import prisma from "@/lib/db";
import { deletePost } from "@/app/actions/post-actions";

export default async function PostsPage() {
  const posts = await prisma.post.findMany({
    include: { author: true },
    orderBy: { createdAt: "desc" },
  });

  return (
    <div style={{ maxWidth: 800, margin: "0 auto", padding: 24 }}>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 24 }}>
        <h1>Blog Posts ({posts.length})</h1>
        <Link
          href="/posts/new"
          style={{ padding: "10px 20px", background: "#2196f3", color: "#fff", textDecoration: "none", borderRadius: 6 }}
        >
          + New Post
        </Link>
      </div>

      {posts.length === 0 && (
        <div style={{ textAlign: "center", padding: 60, color: "#999" }}>
          <p>No posts yet.</p>
          <Link href="/posts/new">Create your first post</Link>
        </div>
      )}

      <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
        {posts.map((post) => (
          <div key={post.id} style={{ border: "1px solid #eee", borderRadius: 8, padding: 20 }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
              <div>
                <Link href={`/posts/${post.id}`} style={{ textDecoration: "none", color: "inherit" }}>
                  <h2 style={{ margin: 0, marginBottom: 8 }}>{post.title}</h2>
                </Link>
                <p style={{ color: "#666", margin: 0 }}>{post.body.slice(0, 120)}...</p>
                <p style={{ color: "#999", fontSize: 13, marginTop: 8 }}>
                  By {post.author.name} · {new Date(post.createdAt).toLocaleDateString()}
                </p>
              </div>

              <div style={{ display: "flex", gap: 8, flexShrink: 0, marginLeft: 16 }}>
                <Link
                  href={`/posts/${post.id}/edit`}
                  style={{ padding: "6px 12px", background: "#f5f5f5", textDecoration: "none", color: "#333", borderRadius: 4, fontSize: 14 }}
                >
                  Edit
                </Link>
                <form action={deletePost.bind(null, post.id)}>
                  <button
                    type="submit"
                    style={{ padding: "6px 12px", background: "#fff", color: "#f44336", border: "1px solid #f44336", borderRadius: 4, cursor: "pointer", fontSize: 14 }}
                  >
                    Delete
                  </button>
                </form>
              </div>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 5️⃣ Create Post Form

```jsx
// app/posts/new/page.jsx
"use client";
import { useActionState } from "react";
import { createPost } from "@/app/actions/post-actions";
import Link from "next/link";

export default function NewPostPage() {
  const [state, formAction, isPending] = useActionState(createPost, { error: null });

  return (
    <div style={{ maxWidth: 600, margin: "40px auto", padding: 24 }}>
      <h1>Create New Post</h1>

      {state?.error && (
        <div style={{ background: "#fff3f3", color: "#c62828", padding: 12, borderRadius: 6, marginBottom: 16 }}>
          ❌ {state.error}
        </div>
      )}

      <form action={formAction}>
        <div style={{ marginBottom: 16 }}>
          <label style={{ display: "block", marginBottom: 6, fontWeight: 600 }}>Title</label>
          <input
            name="title"
            placeholder="Post title..."
            style={{ width: "100%", padding: 10, borderRadius: 6, border: "1px solid #ddd", fontSize: 16 }}
          />
        </div>

        <div style={{ marginBottom: 16 }}>
          <label style={{ display: "block", marginBottom: 6, fontWeight: 600 }}>Body</label>
          <textarea
            name="body"
            rows={8}
            placeholder="Write your post..."
            style={{ width: "100%", padding: 10, borderRadius: 6, border: "1px solid #ddd", fontSize: 15, resize: "vertical" }}
          />
        </div>

        <div style={{ display: "flex", gap: 12 }}>
          <button
            type="submit"
            disabled={isPending}
            style={{ padding: "10px 24px", background: isPending ? "#ccc" : "#2196f3", color: "#fff", border: "none", borderRadius: 6, cursor: isPending ? "not-allowed" : "pointer" }}
          >
            {isPending ? "Creating..." : "Create Post"}
          </button>
          <Link href="/posts" style={{ padding: "10px 24px", color: "#666", textDecoration: "none" }}>
            Cancel
          </Link>
        </div>
      </form>
    </div>
  );
}
```

---

## 6️⃣ Database Seeding

```js
// prisma/seed.js
const { PrismaClient } = require("@prisma/client");
const prisma = new PrismaClient();

async function main() {
  // Create users
  const user1 = await prisma.user.create({
    data: { name: "Ali Hassan", email: "ali@example.com" },
  });

  // Create posts
  await prisma.post.createMany({
    data: [
      { title: "Getting Started with Next.js", body: "Next.js is a React framework...", authorId: user1.id, published: true },
      { title: "Understanding Server Components", body: "Server Components run only on the server...", authorId: user1.id, published: true },
      { title: "Prisma ORM Guide", body: "Prisma is a modern ORM...", authorId: user1.id, published: false },
    ],
  });

  console.log("Database seeded!");
}

main()
  .catch(console.error)
  .finally(() => prisma.$disconnect());
```

Add to `package.json`:

```json
{
  "prisma": {
    "seed": "node prisma/seed.js"
  }
}
```

Run: `npx prisma db seed`

---

## 🎯 Interview Questions

**Q1: How do you use Prisma relations to fetch related data?**

> Use the `include` option: `prisma.post.findMany({ include: { author: true } })` fetches each post with its author. Use `select` for specific fields: `include: { author: { select: { name: true } } }`. For nested includes: `include: { author: true, comments: { include: { user: true } } }`.

**Q2: What is the `onDelete: Cascade` option in Prisma?**

> It tells the database to automatically delete related records when the parent is deleted. For example, `Post @relation(..., onDelete: Cascade)` on `Comment` means deleting a post also deletes all its comments. Without this, attempting to delete a post with comments would throw a foreign key constraint error.

**Q3: How do you handle errors in Server Actions?**

> Return an error object from the action (`return { error: "message" }`) instead of throwing. The calling component (using `useActionState`) receives this return value as `state`. Never throw unhandled errors in Server Actions — they'll cause a 500 error page.

**Q4: What does `revalidatePath` do in a Server Action?**

> It marks the cached version of a specific URL as stale, so Next.js regenerates it on the next request. Without this, the page would still show old cached data even after a database mutation. Always call `revalidatePath` after creating, updating, or deleting records.

---

## 🏠 Home Task

Build a complete **Blog CRUD App**:
1. Prisma schema: `User`, `Post`, `Comment` models with relations
2. Seed the DB with 1 user and 5 posts
3. Posts list page — all posts with author name, edit and delete buttons
4. Create post page — form with Server Action, validation, error messages
5. Edit post page — pre-filled form using existing data, update Server Action
6. Delete — Server Action with `deletePost.bind(null, post.id)`
7. Single post page — shows full post with comments section
8. Add comment form on single post page using a Server Action
