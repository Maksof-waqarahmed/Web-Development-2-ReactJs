# 📡 Data Fetching — SSG, SSR & ISR

## 📚 Topics Covered
- How Next.js extends the `fetch()` API
- **SSG** (Static Site Generation) — `cache: 'force-cache'`
- **SSR** (Server-Side Rendering) — `cache: 'no-store'`
- **ISR** (Incremental Static Regeneration) — `revalidate`
- `generateStaticParams()` — static dynamic routes
- Parallel data fetching with `Promise.all()`
- Sequential vs parallel fetching (waterfall problem)
- `fetch` with custom headers and options

---

## 1️⃣ How Next.js Extends `fetch()`

Next.js adds **caching options** to the native `fetch()`:

```jsx
// SSG — cache forever (build time)
fetch(url, { cache: "force-cache" });

// SSR — never cache, always fresh
fetch(url, { cache: "no-store" });

// ISR — revalidate every N seconds
fetch(url, { next: { revalidate: 60 } });
```

---

## 2️⃣ SSG — Static Site Generation

Data is fetched **once at build time**. The HTML is pre-generated and served instantly to every user.

```jsx
// app/posts/page.jsx
async function getPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=10", {
    cache: "force-cache",  // ← SSG: cached forever (or until revalidated)
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div style={{ padding: 24 }}>
      <h1>Blog Posts (Static)</h1>
      <p style={{ color: "#999" }}>Built once at build time</p>
      {posts.map((post) => (
        <div key={post.id} style={{ borderBottom: "1px solid #eee", padding: "12px 0" }}>
          <h3>{post.title}</h3>
          <p>{post.body.slice(0, 100)}...</p>
        </div>
      ))}
    </div>
  );
}
```

**When to use SSG:**
- Blog posts, documentation, landing pages
- Data that rarely or never changes
- Maximum performance — served from CDN

---

## 3️⃣ SSR — Server-Side Rendering

Data is fetched **on every request**. Fresh data every time.

```jsx
// app/dashboard/page.jsx
async function getUserData() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users/1", {
    cache: "no-store",  // ← SSR: never cache, always fetch fresh
  });
  return res.json();
}

export default async function DashboardPage() {
  const user = await getUserData();

  return (
    <div style={{ padding: 24 }}>
      <h1>Dashboard</h1>
      <p style={{ color: "#999" }}>Data fetched fresh on every request</p>
      <div style={{ background: "#f5f5f5", padding: 16, borderRadius: 8 }}>
        <h2>{user.name}</h2>
        <p>Email: {user.email}</p>
        <p>Phone: {user.phone}</p>
        <p>Company: {user.company.name}</p>
      </div>
    </div>
  );
}
```

**When to use SSR:**
- User-specific data (dashboards, profiles)
- Real-time prices, stock, scores
- Data that changes frequently
- Pages that require authentication checks

---

## 4️⃣ ISR — Incremental Static Regeneration

Best of both worlds — static pages that **automatically revalidate** after a set time.

```jsx
// app/products/page.jsx
async function getProducts() {
  const res = await fetch("https://fakestoreapi.com/products", {
    next: { revalidate: 60 },  // ← ISR: rebuild this page every 60 seconds
  });
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div style={{ padding: 24 }}>
      <h1>Products</h1>
      <p style={{ color: "#999" }}>Revalidates every 60 seconds</p>
      <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
        {products.map((p) => (
          <div key={p.id} style={{ border: "1px solid #eee", padding: 16, borderRadius: 8 }}>
            <img src={p.image} alt={p.title} style={{ width: "100%", height: 120, objectFit: "contain" }} />
            <h3 style={{ fontSize: 14 }}>{p.title}</h3>
            <p style={{ color: "#2196f3" }}>${p.price}</p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Route-Level Revalidation

You can also set `revalidate` at the route level:

```jsx
// app/products/page.jsx
export const revalidate = 60; // revalidate this page every 60 seconds

export default async function ProductsPage() {
  const products = await fetch("https://fakestoreapi.com/products").then(r => r.json());
  // ...
}
```

---

## 5️⃣ Comparison Table

| Feature | SSG | SSR | ISR |
| ------- | --- | --- | --- |
| When fetched | Build time | Every request | Build time + revalidate |
| Speed | Fastest (CDN) | Slower (server every time) | Fast (CDN + background refresh) |
| Data freshness | Stale until rebuild | Always fresh | Fresh within revalidate window |
| Use case | Blogs, docs, marketing | Dashboards, user pages | Products, news, prices |
| `fetch` option | `cache: "force-cache"` | `cache: "no-store"` | `next: { revalidate: N }` |

---

## 6️⃣ `generateStaticParams` — Static Dynamic Routes

Pre-build all dynamic route pages at build time:

```jsx
// app/products/[id]/page.jsx

export async function generateStaticParams() {
  const products = await fetch("https://fakestoreapi.com/products").then(r => r.json());

  return products.map((p) => ({
    id: String(p.id),
  }));
  // Returns: [{ id: "1" }, { id: "2" }, ..., { id: "20" }]
  // Next.js pre-builds all 20 product pages at build time ✅
}

export default async function ProductDetailPage({ params }) {
  const product = await fetch(`https://fakestoreapi.com/products/${params.id}`, {
    cache: "force-cache",
  }).then((r) => r.json());

  return (
    <div style={{ maxWidth: 600, margin: "0 auto", padding: 24 }}>
      <img src={product.image} alt={product.title} style={{ width: 200 }} />
      <h1>{product.title}</h1>
      <p>{product.description}</p>
      <h2 style={{ color: "#2196f3" }}>${product.price}</h2>
    </div>
  );
}
```

---

## 7️⃣ Parallel Data Fetching — Avoiding Waterfalls

### ❌ Waterfall (Sequential — Slow)

```jsx
// Each fetch waits for the previous one to finish
export default async function Page() {
  const user = await fetch(".../user").then(r => r.json());      // 200ms
  const posts = await fetch(".../posts").then(r => r.json());    // +300ms
  const comments = await fetch(".../comments").then(r => r.json()); // +250ms
  // Total: 750ms
}
```

### ✅ Parallel (Promise.all — Fast)

```jsx
// All fetches run at the same time
export default async function Page() {
  const [user, posts, comments] = await Promise.all([
    fetch("https://jsonplaceholder.typicode.com/users/1").then(r => r.json()),
    fetch("https://jsonplaceholder.typicode.com/posts?userId=1&_limit=5").then(r => r.json()),
    fetch("https://jsonplaceholder.typicode.com/comments?postId=1&_limit=3").then(r => r.json()),
  ]);
  // Total: ~300ms (longest individual fetch)

  return (
    <div style={{ padding: 24 }}>
      <h1>{user.name}</h1>
      <section>
        <h2>Recent Posts</h2>
        {posts.map((p) => <p key={p.id}>{p.title}</p>)}
      </section>
      <section>
        <h2>Comments</h2>
        {comments.map((c) => <p key={c.id}>{c.body.slice(0, 60)}...</p>)}
      </section>
    </div>
  );
}
```

---

## 8️⃣ On-Demand Revalidation

Manually revalidate cached data after a mutation (e.g., after a form submit):

```jsx
// app/api/revalidate/route.js
import { revalidatePath, revalidateTag } from "next/cache";
import { NextResponse } from "next/server";

export async function POST(request) {
  const { path } = await request.json();
  revalidatePath(path);        // revalidate a specific path
  // revalidateTag("products"); // or revalidate by tag
  return NextResponse.json({ revalidated: true });
}
```

Tag-based revalidation:

```jsx
// When fetching, add a tag
const res = await fetch("https://fakestoreapi.com/products", {
  next: { tags: ["products"] },
});

// To revalidate:
import { revalidateTag } from "next/cache";
revalidateTag("products"); // all fetches tagged "products" will refetch
```

---

## 🎯 Interview Questions

**Q1: What is the difference between SSG, SSR, and ISR?**

> SSG generates HTML at build time — fastest, served from CDN, but data can be stale. SSR generates HTML on every request — always fresh but slower. ISR is a hybrid — generates at build time but automatically rebuilds after a time interval. ISR combines SSG speed with SSR freshness.

**Q2: How does Next.js know whether to SSR or SSG a page?**

> Through the `fetch` cache options. `cache: "force-cache"` = SSG (or `export const revalidate = false`). `cache: "no-store"` = SSR. `next: { revalidate: N }` = ISR. If no option is specified, Next.js defaults to caching (SSG behavior).

**Q3: What is a waterfall in data fetching and how do you fix it?**

> A waterfall is when multiple fetch calls run sequentially — each waiting for the previous. Fix it with `Promise.all()` to run all fetches in parallel, reducing total time from the sum of all durations to the maximum single duration.

**Q4: What is `revalidatePath` and when would you use it?**

> `revalidatePath("/products")` purges the cached version of a specific route, forcing Next.js to regenerate it on the next request. Used in Server Actions or Route Handlers after data mutations (create, update, delete) to ensure users see fresh data.

---

## 🏠 Home Task

Build a **News App** with all three rendering strategies:
1. `/news` — SSG page fetching headlines (refresh only on rebuild)
2. `/news/live` — SSR page showing "live" news (always fresh, `cache: "no-store"`)
3. `/news/trending` — ISR page revalidating every 30 seconds
4. `/news/[id]` — SSG dynamic routes using `generateStaticParams`
5. Use `Promise.all` to fetch both the article AND related articles in parallel
6. Add a loading spinner with `loading.jsx` for each news section
