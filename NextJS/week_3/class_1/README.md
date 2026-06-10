# ⚙️ Server Components vs Client Components

## 📚 Topics Covered
- What are React Server Components (RSC)
- Server Components — default in Next.js App Router
- Client Components — `"use client"` directive
- When to use Server vs Client components
- How to compose them together
- Passing Server Component data to Client Components as props
- Boundaries — where the client-server split happens
- Common mistakes to avoid

---

## 1️⃣ The Two Types of Components

In Next.js App Router, every component is either:

| | Server Component | Client Component |
|--|-----------------|-----------------|
| **Default** | ✅ Yes | ❌ Must opt-in with `"use client"` |
| **Runs on** | Server only | Browser (and server during SSR) |
| **Can use** | `async/await`, direct DB access | `useState`, `useEffect`, event handlers |
| **JavaScript sent to browser** | ❌ None | ✅ Yes |
| **Access to browser APIs** | ❌ No | ✅ Yes |

---

## 2️⃣ Server Components — The Default

All components in Next.js are Server Components **by default**. They run only on the server.

```jsx
// app/page.jsx — Server Component (no "use client" = server by default)
async function getUser() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users/1");
  return res.json();
}

export default async function HomePage() {
  const user = await getUser(); // ✅ async/await directly in component

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <p>Email: {user.email}</p>
      <p>City: {user.address.city}</p>
    </div>
  );
}
```

**What you CAN do in Server Components:**
- `async/await` — fetch data directly
- Access databases, filesystems, environment variables
- Import server-side libraries
- Keep sensitive logic off the browser

**What you CANNOT do:**
- `useState`, `useReducer`, `useContext`
- `useEffect`, `useLayoutEffect`
- Event handlers (`onClick`, `onChange`, etc.)
- Browser-only APIs (`window`, `localStorage`, `document`)

---

## 3️⃣ Client Components — `"use client"`

Add `"use client"` at the top of a file to make it a Client Component.

```jsx
// app/components/Counter.jsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

**Use Client Components when you need:**
- `useState`, `useReducer`, `useEffect`
- Event handlers (`onClick`, `onSubmit`, etc.)
- Browser APIs (`localStorage`, `window`, `navigator`)
- Third-party libraries that use client hooks

---

## 4️⃣ The `"use client"` Boundary

`"use client"` creates a **boundary**. Everything in the file and everything it imports becomes a Client Component.

```
app/
├── page.jsx            ← Server Component
└── components/
    ├── ProductList.jsx ← Server Component (fetches data)
    └── AddToCart.jsx   ← "use client" (needs onClick)
```

```jsx
// app/components/ProductList.jsx — Server Component
import AddToCart from "./AddToCart";

async function getProducts() {
  const res = await fetch("https://fakestoreapi.com/products");
  return res.json();
}

export default async function ProductList() {
  const products = await getProducts();

  return (
    <div>
      {products.map((p) => (
        <div key={p.id} style={{ border: "1px solid #eee", padding: 16, marginBottom: 8 }}>
          <h3>{p.title}</h3>
          <p>${p.price}</p>
          {/* Client Component inside Server Component ✅ */}
          <AddToCart product={p} />
        </div>
      ))}
    </div>
  );
}
```

```jsx
// app/components/AddToCart.jsx — Client Component
"use client";

import { useState } from "react";

export default function AddToCart({ product }) {
  const [added, setAdded] = useState(false);

  return (
    <button
      onClick={() => setAdded(true)}
      style={{ background: added ? "#4caf50" : "#2196f3", color: "#fff", border: "none", padding: "8px 16px", borderRadius: 4 }}
    >
      {added ? "✓ Added to Cart" : "Add to Cart"}
    </button>
  );
}
```

---

## 5️⃣ Composition Patterns

### ✅ Pattern 1: Server Component renders Client Component

```jsx
// Server Component
import ClientButton from "./ClientButton"; // Client Component

export default async function Page() {
  const data = await fetch("...").then(r => r.json());
  return <ClientButton label={data.title} />; // pass data as props ✅
}
```

### ✅ Pattern 2: Pass Server Component as `children` to Client Component

```jsx
// ClientWrapper.jsx
"use client";
import { useState } from "react";

export default function ClientWrapper({ children }) {
  const [open, setOpen] = useState(false);
  return (
    <div>
      <button onClick={() => setOpen(!open)}>Toggle</button>
      {open && children}
    </div>
  );
}
```

```jsx
// app/page.jsx — Server Component
import ClientWrapper from "./ClientWrapper";
import ServerContent from "./ServerContent"; // async Server Component

export default function Page() {
  return (
    <ClientWrapper>
      <ServerContent /> {/* ✅ Server Component passed as children */}
    </ClientWrapper>
  );
}
```

### ❌ Pattern to AVOID — importing Server Component inside Client Component

```jsx
// ClientComponent.jsx
"use client";
import ServerComponent from "./ServerComponent"; // ❌ Won't work!
// Anything imported by a Client Component becomes a Client Component too
```

---

## 6️⃣ Practical Decision Guide

```
Does the component need:
  - onClick, onChange, onSubmit?   → Client Component
  - useState or useEffect?         → Client Component
  - localStorage / window?         → Client Component
  - fetch data from API / DB?      → Server Component
  - Display-only (no interactivity)? → Server Component
  - Both fetch + interactivity?    → Split into two components
```

---

## 7️⃣ Full Example — Blog Page

```jsx
// app/blog/page.jsx — Server Component (fetches posts)
import LikeButton from "@/components/LikeButton";

async function getPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=5");
  return res.json();
}

export default async function BlogPage() {
  const posts = await getPosts();

  return (
    <div style={{ maxWidth: 700, margin: "0 auto", padding: 24 }}>
      <h1>Blog</h1>
      {posts.map((post) => (
        <article key={post.id} style={{ borderBottom: "1px solid #eee", paddingBottom: 20, marginBottom: 20 }}>
          <h2>{post.title}</h2>
          <p style={{ color: "#666" }}>{post.body}</p>
          <LikeButton postId={post.id} />
        </article>
      ))}
    </div>
  );
}
```

```jsx
// components/LikeButton.jsx — Client Component
"use client";
import { useState } from "react";

export default function LikeButton({ postId }) {
  const [liked, setLiked] = useState(false);
  const [count, setCount] = useState(0);

  return (
    <button
      onClick={() => {
        setLiked(!liked);
        setCount(liked ? count - 1 : count + 1);
      }}
      style={{ background: "none", border: "1px solid #ddd", padding: "6px 12px", borderRadius: 4, cursor: "pointer" }}
    >
      {liked ? "❤️" : "🤍"} {count} Likes
    </button>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What is the difference between Server and Client Components?**

> Server Components run only on the server — no JavaScript is sent to the browser. They can use async/await, access databases, and import server-only packages. Client Components run in the browser and can use React hooks, event handlers, and browser APIs. Client Components are created by adding `"use client"` at the top of the file.

**Q2: Why is `"use client"` a directive, not a runtime check?**

> It's a compile-time directive that tells the Next.js bundler to split the module graph at this boundary. Everything in the file and below becomes part of the client bundle. It doesn't check conditions at runtime.

**Q3: Can you use a Server Component inside a Client Component?**

> Not by direct import — anything imported by a Client Component becomes a Client Component too. But you CAN pass a Server Component as `children` or another prop to a Client Component. The Server Component renders on the server and its output (HTML) is passed through.

**Q4: Why are Server Components better for performance?**

> Server Components send zero JavaScript to the browser — only HTML. This reduces bundle size, speeds up page load, and improves Time-to-Interactive. They also have direct access to backend resources so no extra API round-trips are needed.

---

## 🏠 Home Task

Build a **Blog App** with proper Server/Client split:
1. `app/blog/page.jsx` — Server Component that fetches 10 posts from JSONPlaceholder
2. `components/PostCard.jsx` — Server Component that displays post title and body
3. `components/LikeButton.jsx` — Client Component with `useState` for like/unlike
4. `components/SearchBar.jsx` — Client Component with `useState` for filtering posts
5. Pass posts from Server Component to SearchBar as `initialPosts` prop
6. In the browser DevTools, check the Network tab — notice that Server Component code is NOT in the JS bundle
