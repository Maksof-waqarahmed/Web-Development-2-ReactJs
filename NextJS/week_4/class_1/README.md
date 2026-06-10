# 🔧 Server Actions & Form Handling

## 📚 Topics Covered
- What are Server Actions
- `"use server"` directive
- Form submission with Server Actions — no API endpoint needed
- `useFormStatus` — pending state for submit buttons
- `useActionState` (formerly `useFormState`) — form state with server actions
- Mutations — create, update, delete via Server Actions
- `revalidatePath()` — clearing cache after mutation
- Server Action in a Client Component
- Progressive enhancement — works without JavaScript

---

## 1️⃣ What are Server Actions?

**Server Actions** are async functions that run on the server but can be called directly from forms or Client Components. No need to create a separate API endpoint.

```
Traditional approach:
  Form → POST /api/submit → Route Handler → Database
  (need to write a separate API route)

With Server Actions:
  Form → Server Action → Database
  (one function, no API route needed)
```

---

## 2️⃣ Basic Server Action — Inline in Server Component

```jsx
// app/contact/page.jsx
export default function ContactPage() {
  async function submitForm(formData) {
    "use server"; // ← this function runs on the server

    const name = formData.get("name");
    const email = formData.get("email");
    const message = formData.get("message");

    // Save to database, send email, etc.
    console.log("Server received:", { name, email, message });

    // In real app: await db.messages.create({ name, email, message });
  }

  return (
    <div style={{ maxWidth: 480, margin: "40px auto", padding: 24 }}>
      <h1>Contact Us</h1>
      <form action={submitForm}>
        <div style={{ marginBottom: 12 }}>
          <label>Name</label>
          <input name="name" style={{ display: "block", width: "100%", padding: 8, marginTop: 4 }} required />
        </div>
        <div style={{ marginBottom: 12 }}>
          <label>Email</label>
          <input name="email" type="email" style={{ display: "block", width: "100%", padding: 8, marginTop: 4 }} required />
        </div>
        <div style={{ marginBottom: 12 }}>
          <label>Message</label>
          <textarea name="message" rows={4} style={{ display: "block", width: "100%", padding: 8, marginTop: 4 }} required />
        </div>
        <button type="submit" style={{ padding: "10px 24px", background: "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}>
          Send Message
        </button>
      </form>
    </div>
  );
}
```

---

## 3️⃣ Separate Actions File — `actions.js`

For reusability, put Server Actions in a dedicated file:

```js
// app/actions.js
"use server"; // ← marks the WHOLE file as server-only

import { revalidatePath } from "next/cache";

export async function createPost(formData) {
  const title = formData.get("title");
  const body = formData.get("body");

  // Validate
  if (!title || title.length < 3) {
    return { error: "Title must be at least 3 characters" };
  }

  // Save to DB (example with fetch — in real app use Prisma)
  const res = await fetch("https://jsonplaceholder.typicode.com/posts", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ title, body, userId: 1 }),
  });

  if (!res.ok) return { error: "Failed to create post" };

  // Revalidate the posts page so it shows the new post
  revalidatePath("/posts");

  return { success: true };
}

export async function deletePost(id) {
  await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
    method: "DELETE",
  });
  revalidatePath("/posts");
}
```

---

## 4️⃣ `useActionState` — Form State + Errors

```jsx
// app/posts/new/page.jsx
"use client";

import { useActionState } from "react";
import { createPost } from "@/app/actions";

const initialState = { error: null, success: false };

export default function NewPostPage() {
  const [state, formAction, isPending] = useActionState(createPost, initialState);

  return (
    <div style={{ maxWidth: 480, margin: "40px auto", padding: 24 }}>
      <h1>Create New Post</h1>

      {state.error && (
        <p style={{ color: "red", background: "#fff3f3", padding: 12, borderRadius: 4 }}>
          ❌ {state.error}
        </p>
      )}

      {state.success && (
        <p style={{ color: "green", background: "#f3fff3", padding: 12, borderRadius: 4 }}>
          ✅ Post created successfully!
        </p>
      )}

      <form action={formAction}>
        <div style={{ marginBottom: 12 }}>
          <label>Title</label>
          <input name="title" style={{ display: "block", width: "100%", padding: 8, marginTop: 4 }} />
        </div>
        <div style={{ marginBottom: 12 }}>
          <label>Body</label>
          <textarea name="body" rows={4} style={{ display: "block", width: "100%", padding: 8, marginTop: 4 }} />
        </div>
        <button
          type="submit"
          disabled={isPending}
          style={{ padding: "10px 24px", background: isPending ? "#ccc" : "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}
        >
          {isPending ? "Creating..." : "Create Post"}
        </button>
      </form>
    </div>
  );
}
```

---

## 5️⃣ `useFormStatus` — Submit Button Pending State

```jsx
// components/SubmitButton.jsx
"use client";
import { useFormStatus } from "react-dom";

export default function SubmitButton({ label = "Submit" }) {
  const { pending } = useFormStatus();

  return (
    <button
      type="submit"
      disabled={pending}
      style={{
        padding: "10px 24px",
        background: pending ? "#ccc" : "#2196f3",
        color: "#fff",
        border: "none",
        borderRadius: 4,
        cursor: pending ? "not-allowed" : "pointer",
      }}
    >
      {pending ? "⏳ Loading..." : label}
    </button>
  );
}
```

```jsx
// Use it in any form
import SubmitButton from "@/components/SubmitButton";

export default function MyForm() {
  async function handleSubmit(formData) {
    "use server";
    // ...
  }

  return (
    <form action={handleSubmit}>
      <input name="title" />
      <SubmitButton label="Save Post" />
    </form>
  );
}
```

---

## 6️⃣ Delete Action with Button

```jsx
// app/posts/page.jsx
import { deletePost } from "@/app/actions";

async function getPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=5");
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div style={{ padding: 24 }}>
      <h1>Posts</h1>
      {posts.map((post) => (
        <div key={post.id} style={{ display: "flex", justifyContent: "space-between", borderBottom: "1px solid #eee", padding: "12px 0" }}>
          <div>
            <h3>{post.title}</h3>
            <p style={{ color: "#666", fontSize: 14 }}>{post.body.slice(0, 80)}...</p>
          </div>
          <form action={deletePost.bind(null, post.id)}>
            <button type="submit" style={{ color: "red", background: "none", border: "1px solid red", borderRadius: 4, padding: "4px 10px", cursor: "pointer" }}>
              Delete
            </button>
          </form>
        </div>
      ))}
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What is a Server Action?**

> A Server Action is an async function marked with `"use server"` that runs exclusively on the server. It can be called from a form's `action` attribute or from a Client Component. No API endpoint is needed — the framework handles the secure server call automatically.

**Q2: What is the difference between `useActionState` and `useFormStatus`?**

> `useActionState` wraps a Server Action and gives you the action's return value (like error messages or success state) and an `isPending` boolean. `useFormStatus` reads the status of the nearest parent `<form>` — useful in separate submit button components to access the `pending` state without prop drilling.

**Q3: Why use Server Actions instead of Route Handlers for form submission?**

> Server Actions are simpler — no `fetch("/api/...")` call needed, they work with plain `<form action={...}>` (progressive enhancement — even without JS), and they integrate directly with React state via `useActionState`. Route Handlers are better for external API consumers or REST-style endpoints.

**Q4: What does `revalidatePath` do after a mutation?**

> It purges the cached version of a specific page, forcing Next.js to regenerate it on the next request. Without calling `revalidatePath`, the page would show stale cached data even after a database change.

---

## 🏠 Home Task

Build a **Todo App** using Server Actions:
1. Display list of todos (fetch from JSONPlaceholder or a static array)
2. A form to create new todos using a Server Action (`createTodo`)
3. A delete button on each todo using a Server Action (`deleteTodo`)
4. Show pending state with `useFormStatus` on the submit button
5. Show success/error messages using `useActionState`
6. Call `revalidatePath("/todos")` after each mutation
