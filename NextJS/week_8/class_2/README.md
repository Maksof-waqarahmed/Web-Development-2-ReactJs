# 🌊 Streaming & Suspense in Next.js

## 📚 Topics Covered
- What is Streaming — progressive rendering
- `Suspense` — granular loading states
- `loading.jsx` — route-level streaming
- Streaming specific components while rest loads
- `Suspense` with async Server Components
- Skeleton loaders
- Error boundaries with `error.jsx`
- `React.lazy` and dynamic imports

---

## 1️⃣ What is Streaming?

Traditional SSR waits for **all data** to be fetched before sending **any HTML** to the browser:

```
Without Streaming:
  Server: [wait for all data... 3 seconds] → send full HTML
  Browser: [blank page for 3 seconds] → page appears all at once

With Streaming:
  Server: [immediately] → send HTML shell (navbar, layout)
  Server: [data ready] → stream each section as it finishes
  Browser: [page appears immediately] → sections fill in progressively
```

This massively improves **Time to First Byte (TTFB)** and **First Contentful Paint (FCP)**.

---

## 2️⃣ `loading.jsx` — Route-Level Streaming

The simplest way to stream — create a `loading.jsx` file:

```jsx
// app/products/loading.jsx
export default function Loading() {
  return (
    <div style={{ padding: 24 }}>
      <h1>Products</h1>
      {/* Skeleton cards */}
      <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
        {Array.from({ length: 6 }).map((_, i) => (
          <div
            key={i}
            style={{
              border: "1px solid #eee",
              borderRadius: 8,
              overflow: "hidden",
              animation: "pulse 1.5s ease-in-out infinite",
            }}
          >
            <div style={{ height: 200, background: "#f0f0f0" }} />
            <div style={{ padding: 12 }}>
              <div style={{ height: 14, background: "#f0f0f0", borderRadius: 4, marginBottom: 8 }} />
              <div style={{ height: 14, background: "#f0f0f0", borderRadius: 4, width: "60%" }} />
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

```jsx
// app/products/page.jsx (async — loads data)
export default async function ProductsPage() {
  const products = await fetch("https://fakestoreapi.com/products").then(r => r.json());
  // loading.jsx shows while this awaits ↑
  return <div>{/* products */}</div>;
}
```

---

## 3️⃣ `<Suspense>` — Component-Level Streaming

`<Suspense>` gives you control over WHICH parts stream independently:

```jsx
// app/dashboard/page.jsx
import { Suspense } from "react";
import RecentOrders from "./components/RecentOrders";
import UserStats from "./components/UserStats";
import TopProducts from "./components/TopProducts";

export default function DashboardPage() {
  return (
    <div style={{ padding: 24 }}>
      <h1>Dashboard</h1>

      {/* Each section streams independently */}
      <Suspense fallback={<StatsSkeleton />}>
        <UserStats />
      </Suspense>

      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20, marginTop: 20 }}>
        <Suspense fallback={<CardSkeleton />}>
          <RecentOrders />
        </Suspense>

        <Suspense fallback={<CardSkeleton />}>
          <TopProducts />
        </Suspense>
      </div>
    </div>
  );
}

function StatsSkeleton() {
  return <div style={{ height: 100, background: "#f0f0f0", borderRadius: 8, marginBottom: 20 }} />;
}

function CardSkeleton() {
  return <div style={{ height: 300, background: "#f0f0f0", borderRadius: 8 }} />;
}
```

```jsx
// components/RecentOrders.jsx — async Server Component
export default async function RecentOrders() {
  // Simulated slow fetch
  await new Promise(r => setTimeout(r, 2000));
  const orders = await fetch("https://jsonplaceholder.typicode.com/todos?_limit=5").then(r => r.json());

  return (
    <div style={{ border: "1px solid #eee", borderRadius: 8, padding: 16 }}>
      <h3>Recent Orders</h3>
      {orders.map(o => (
        <div key={o.id} style={{ padding: "8px 0", borderBottom: "1px solid #f5f5f5" }}>
          <span>{o.title.slice(0, 40)}...</span>
          <span style={{ color: o.completed ? "green" : "orange", marginLeft: 8 }}>
            {o.completed ? "✓ Done" : "Pending"}
          </span>
        </div>
      ))}
    </div>
  );
}
```

---

## 4️⃣ Parallel Streaming — Multiple Suspense Boundaries

```jsx
// app/profile/page.jsx
import { Suspense } from "react";

export default function ProfilePage({ params }) {
  return (
    <div style={{ padding: 24 }}>
      {/* This renders instantly — no async */}
      <h1>User Profile</h1>

      {/* These stream in parallel — don't block each other */}
      <div style={{ display: "grid", gridTemplateColumns: "300px 1fr", gap: 20, marginTop: 20 }}>
        <div>
          <Suspense fallback={<ProfileCardSkeleton />}>
            <ProfileCard userId={params.id} />
          </Suspense>
        </div>
        <div>
          <Suspense fallback={<PostsListSkeleton />}>
            <UserPosts userId={params.id} />
          </Suspense>
        </div>
      </div>
    </div>
  );
}
```

```jsx
// Slow component (2s)
async function ProfileCard({ userId }) {
  await new Promise(r => setTimeout(r, 2000));
  const user = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then(r => r.json());
  return (
    <div style={{ border: "1px solid #eee", borderRadius: 8, padding: 16, textAlign: "center" }}>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}

// Fast component (0.5s)
async function UserPosts({ userId }) {
  const posts = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}&_limit=5`).then(r => r.json());
  return (
    <div>
      <h3>Posts</h3>
      {posts.map(p => <div key={p.id} style={{ padding: "8px 0", borderBottom: "1px solid #eee" }}>{p.title}</div>)}
    </div>
  );
}
```

✅ `UserPosts` (0.5s) appears first. `ProfileCard` (2s) shows skeleton while loading, then appears. They don't block each other.

---

## 5️⃣ Dynamic Imports — Lazy Loading Client Components

```jsx
// app/page.jsx
import dynamic from "next/dynamic";

// Lazy load — only load when needed (reduces initial bundle)
const HeavyChart = dynamic(() => import("@/components/HeavyChart"), {
  loading: () => <div>Loading chart...</div>,
  ssr: false, // don't render on server (for browser-only libraries)
});

const VideoPlayer = dynamic(() => import("@/components/VideoPlayer"), {
  ssr: false, // video player needs browser APIs
});

export default function Page() {
  return (
    <div>
      <h1>Analytics</h1>
      <HeavyChart />
      <VideoPlayer />
    </div>
  );
}
```

---

## 6️⃣ Error Handling with Streaming

```jsx
// app/dashboard/error.jsx
"use client";

export default function DashboardError({ error, reset }) {
  return (
    <div style={{ padding: 24, background: "#fff3f3", borderRadius: 8, border: "1px solid #f44336" }}>
      <h3 style={{ color: "#c62828" }}>❌ Failed to load dashboard</h3>
      <p style={{ color: "#666" }}>{error.message}</p>
      <button
        onClick={reset}
        style={{ marginTop: 12, padding: "8px 16px", background: "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}
      >
        Try Again
      </button>
    </div>
  );
}
```

For Suspense-level error boundaries:

```jsx
import { Suspense } from "react";
import { ErrorBoundary } from "react-error-boundary";

function ComponentErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div style={{ padding: 16, background: "#fff3f3", borderRadius: 8 }}>
      <p>Failed to load: {error.message}</p>
      <button onClick={resetErrorBoundary}>Retry</button>
    </div>
  );
}

export default function Page() {
  return (
    <ErrorBoundary FallbackComponent={ComponentErrorFallback}>
      <Suspense fallback={<Loading />}>
        <SlowComponent />
      </Suspense>
    </ErrorBoundary>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What is streaming in Next.js and why is it better than traditional SSR?**

> Traditional SSR sends the full HTML only after ALL data is fetched — causing a blank page while waiting. Streaming sends the HTML shell immediately, then progressively streams each section as its data becomes ready. Users see content faster (lower FCP) and individual slow components don't block the rest of the page.

**Q2: What is the difference between `loading.jsx` and `<Suspense>`?**

> `loading.jsx` is an automatic route-level Suspense boundary — it wraps the entire `page.jsx` component. `<Suspense>` is more granular — you wrap individual async components within a page, so different sections can stream independently. Use `loading.jsx` for the whole page, `<Suspense>` for specific sections.

**Q3: How do parallel Suspense boundaries work?**

> When multiple `<Suspense>` boundaries are siblings, their async components load independently and in parallel. A slow component's skeleton doesn't block a faster component from appearing. They race to completion — each resolves and replaces its skeleton when ready.

**Q4: When should you use `dynamic()` with `ssr: false`?**

> When a component uses browser-only APIs (`window`, `document`, `navigator`) or libraries that don't support SSR (like some chart or map libraries). Setting `ssr: false` ensures the component only renders on the client. The `loading` option provides a server-rendered placeholder.

---

## 🏠 Home Task

Build a **Streaming Dashboard** app:
1. Dashboard page at `/dashboard` with 4 sections: Stats, Orders, Products, Activity
2. Each section is a separate async Server Component with different simulated delays (0.5s, 1s, 2s, 3s)
3. Wrap each section in `<Suspense>` with a skeleton fallback
4. Verify in the browser that sections appear one by one as they load (not all at once)
5. Add `loading.jsx` to the dashboard route as a page-level fallback
6. Add an `error.jsx` with a "Try Again" button
7. Lazy-load a chart component using `dynamic()` with `ssr: false`
