# ⚡ Caching Strategies in Next.js

## 📚 Topics Covered
- Next.js 4-layer caching system overview
- Request Memoization — same fetch deduplicated in one render
- Data Cache — persistent fetch cache across requests
- Full Route Cache — cached HTML + RSC payload
- Router Cache — client-side navigation cache
- `revalidatePath()` and `revalidateTag()` — on-demand invalidation
- `unstable_cache()` — caching non-fetch functions (DB queries)
- `cache: "no-store"` vs `revalidate: 0` vs `revalidate: N`

---

## 1️⃣ The 4 Caching Layers

```
Request
    ↓
1. Request Memoization   (same-request deduplication — RAM)
    ↓
2. Data Cache            (persistent fetch cache — disk/CDN)
    ↓
3. Full Route Cache      (static HTML pages — CDN)
    ↓
4. Router Cache          (client-side prefetch cache — browser)
```

Each layer serves a different purpose and operates independently.

---

## 2️⃣ Request Memoization

**What:** Deduplicates identical `fetch()` calls made during a single render/request.

**Why:** Multiple components on the same page might need the same data — without memoization, each component would make a separate HTTP request.

```jsx
// Both components call the same URL — only ONE HTTP request is made
async function getUser(id) {
  const res = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`);
  return res.json();
}

// Component A
async function UserName({ id }) {
  const user = await getUser(id); // request #1 — actual fetch
  return <span>{user.name}</span>;
}

// Component B (on the same page)
async function UserEmail({ id }) {
  const user = await getUser(id); // request #2 — returns CACHED result (no HTTP)
  return <span>{user.email}</span>;
}
```

- **Scope:** Single render cycle only
- **Storage:** In-memory (RAM)
- **Duration:** Until request ends

---

## 3️⃣ Data Cache

**What:** Persists `fetch()` results across requests (and across server restarts in some cases).

**Why:** Avoid hitting external APIs on every page load — serve from cache.

```jsx
// SSG — cached forever (or until revalidated)
fetch(url, { cache: "force-cache" });

// SSR — never cache
fetch(url, { cache: "no-store" });

// ISR — revalidate after N seconds
fetch(url, { next: { revalidate: 60 } });

// Tag-based revalidation
fetch(url, { next: { tags: ["products"] } });
```

### On-Demand Revalidation

```js
import { revalidateTag, revalidatePath } from "next/cache";

// Invalidate all fetches tagged "products"
revalidateTag("products");

// Invalidate a specific page
revalidatePath("/products");
revalidatePath("/products/[id]", "page"); // all dynamic pages
```

---

## 4️⃣ `unstable_cache()` — Cache Non-Fetch Functions

Prisma queries and other non-fetch functions are NOT automatically cached. Use `unstable_cache` to add caching:

```js
// lib/cached-queries.js
import { unstable_cache } from "next/cache";
import prisma from "./db";

// Cache the DB result for 60 seconds, tagged "posts"
export const getCachedPosts = unstable_cache(
  async () => {
    return prisma.post.findMany({
      include: { author: true },
      orderBy: { createdAt: "desc" },
    });
  },
  ["all-posts"],          // cache key
  {
    revalidate: 60,       // revalidate every 60 seconds
    tags: ["posts"],      // tag for on-demand invalidation
  }
);
```

```jsx
// app/posts/page.jsx
import { getCachedPosts } from "@/lib/cached-queries";

export default async function PostsPage() {
  const posts = await getCachedPosts(); // cached!
  return (
    <div>
      {posts.map(p => <div key={p.id}>{p.title}</div>)}
    </div>
  );
}
```

After creating/updating a post, invalidate:

```js
// In a Server Action
import { revalidateTag } from "next/cache";
revalidateTag("posts"); // getCachedPosts will refetch on next call
```

---

## 5️⃣ Full Route Cache

Next.js pre-renders and caches entire pages at build time for static routes. The cached HTML + React Server Component payload is served directly from CDN.

```jsx
// This page is cached as static HTML at build time
export default async function StaticPage() {
  const data = await fetch("https://api.example.com/config", {
    cache: "force-cache",
  }).then(r => r.json());

  return <div>{data.title}</div>;
}
```

Opt out of Full Route Cache (make it dynamic):

```jsx
// Option 1: use no-store fetch
const data = await fetch(url, { cache: "no-store" });

// Option 2: export dynamic route config
export const dynamic = "force-dynamic"; // always SSR this page
export const revalidate = 0;            // same as no-store
```

---

## 6️⃣ Router Cache — Client-Side

The browser caches visited pages and prefetched links for instant back/forward navigation.

```jsx
// Prefetches /about when link is in viewport
<Link href="/about">About</Link>

// Disable prefetching
<Link href="/about" prefetch={false}>About</Link>
```

**Duration:**
- Static pages: 5 minutes
- Dynamic pages: 30 seconds

---

## 7️⃣ Caching Cheatsheet

| Scenario | Config | Behavior |
| -------- | ------ | -------- |
| Blog post (rarely changes) | `cache: "force-cache"` or `revalidate: 3600` | SSG/ISR |
| Product prices (change hourly) | `revalidate: 3600` | ISR every hour |
| News feed (change frequently) | `revalidate: 60` | ISR every minute |
| User dashboard (personal data) | `cache: "no-store"` | SSR always |
| Admin panel | `export const dynamic = "force-dynamic"` | SSR always |
| After mutation | `revalidateTag("posts")` | Purge cache on demand |

---

## 8️⃣ Complete Example — Cached Product Page

```jsx
// lib/cached-queries.js
import { unstable_cache } from "next/cache";

export const getProducts = unstable_cache(
  async () => {
    const res = await fetch("https://fakestoreapi.com/products");
    return res.json();
  },
  ["products-list"],
  { revalidate: 300, tags: ["products"] } // 5 minutes
);

export const getProduct = unstable_cache(
  async (id) => {
    const res = await fetch(`https://fakestoreapi.com/products/${id}`);
    return res.json();
  },
  ["product"],
  { revalidate: 300, tags: ["products"] }
);
```

```jsx
// app/products/page.jsx
import { getProducts } from "@/lib/cached-queries";

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div style={{ padding: 24 }}>
      <h1>Products</h1>
      <p style={{ color: "#999", fontSize: 13 }}>Cached for 5 minutes</p>
      {products.map(p => (
        <div key={p.id}>{p.title} — ${p.price}</div>
      ))}
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What are the 4 caching layers in Next.js?**

> (1) **Request Memoization** — deduplicates identical fetches in a single render. (2) **Data Cache** — persists fetch results across requests using `cache: "force-cache"` or `revalidate`. (3) **Full Route Cache** — caches the entire rendered HTML of static pages. (4) **Router Cache** — client-side cache for visited/prefetched routes.

**Q2: What is the difference between `revalidatePath` and `revalidateTag`?**

> `revalidatePath("/products")` purges the cache for a specific URL. `revalidateTag("products")` purges all data cache entries tagged with "products" — including fetches from multiple different pages that use that tag. Tags are more flexible for cache management.

**Q3: Why use `unstable_cache` for Prisma queries?**

> The Data Cache only wraps the `fetch()` function. Direct database queries (Prisma, raw SQL) bypass the Data Cache entirely. `unstable_cache` wraps any async function with the same caching semantics — `revalidate` time and `tags` for on-demand invalidation.

**Q4: How do you make a page always render fresh (SSR)?**

> Either use `fetch` with `cache: "no-store"`, or export `export const dynamic = "force-dynamic"` from the page file. The latter is cleaner when you want the whole page to be dynamic without changing individual fetch calls.

---

## 🏠 Home Task

Implement caching in a **Blog App**:
1. Use `unstable_cache` to cache `getAllPosts()` Prisma query with tag `"posts"` and `revalidate: 60`
2. Use `unstable_cache` to cache `getPost(id)` with tag `"post-{id}"` and `revalidate: 300`
3. After creating a new post (Server Action), call `revalidateTag("posts")`
4. After updating a post, call `revalidateTag("posts")` AND `revalidateTag("post-{id}")`
5. Mark `/admin` pages with `export const dynamic = "force-dynamic"` (always SSR)
6. Use `<Link prefetch={false}>` on the admin navigation
7. In the browser DevTools → Network tab, verify cached pages load from cache on re-visit
