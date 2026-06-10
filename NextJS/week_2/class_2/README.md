# 🔀 Dynamic Routes, Route Groups & Parallel Routes

## 📚 Topics Covered
- Dynamic routes — `[id]`, `[slug]`
- Catch-all routes — `[...slug]`
- Optional catch-all — `[[...slug]]`
- `generateStaticParams()` — pre-generating dynamic routes
- Route Groups — `(group)` folders for organization
- `useParams` — reading dynamic segment values
- `useSearchParams` — reading query parameters
- Parallel Routes — `@slot` folders

---

## 1️⃣ Dynamic Routes

When a URL segment changes (like `/products/1`, `/products/2`), use a **dynamic segment** — a folder wrapped in square brackets.

```
app/
└── products/
    ├── page.jsx           →  /products
    └── [id]/
        └── page.jsx       →  /products/1, /products/42, etc.
```

```jsx
// app/products/[id]/page.jsx
async function getProduct(id) {
  const res = await fetch(`https://fakestoreapi.com/products/${id}`);
  if (!res.ok) return null;
  return res.json();
}

export default async function ProductPage({ params }) {
  const product = await getProduct(params.id);

  if (!product) return <h2>Product not found</h2>;

  return (
    <div style={{ maxWidth: 600, margin: "0 auto", padding: 24 }}>
      <img src={product.image} alt={product.title} style={{ width: 200 }} />
      <h1>{product.title}</h1>
      <p style={{ color: "#666" }}>{product.description}</p>
      <h2>${product.price}</h2>
    </div>
  );
}
```

```jsx
// app/products/page.jsx
import Link from "next/link";

async function getProducts() {
  const res = await fetch("https://fakestoreapi.com/products");
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div style={{ padding: 24 }}>
      <h1>All Products</h1>
      <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
        {products.map((p) => (
          <Link key={p.id} href={`/products/${p.id}`} style={{ textDecoration: "none", color: "inherit" }}>
            <div style={{ border: "1px solid #eee", padding: 16, borderRadius: 8 }}>
              <img src={p.image} alt={p.title} style={{ width: "100%", height: 120, objectFit: "contain" }} />
              <h3 style={{ fontSize: 14 }}>{p.title}</h3>
              <p style={{ color: "#2196f3" }}>${p.price}</p>
            </div>
          </Link>
        ))}
      </div>
    </div>
  );
}
```

---

## 2️⃣ `useParams` — Client-Side Access to Params

If you need dynamic params in a **Client Component**:

```jsx
"use client";
import { useParams } from "next/navigation";

function ProductBreadcrumb() {
  const params = useParams();
  return <p>Viewing product #{params.id}</p>;
}
```

---

## 3️⃣ `useSearchParams` — Query Parameters

Access query string values like `/search?q=laptop&page=2`:

```jsx
"use client";
import { useSearchParams } from "next/navigation";

function SearchResults() {
  const searchParams = useSearchParams();
  const query = searchParams.get("q");    // "laptop"
  const page = searchParams.get("page");  // "2"

  return (
    <div>
      <p>Searching for: <strong>{query}</strong></p>
      <p>Page: {page}</p>
    </div>
  );
}
```

---

## 4️⃣ `generateStaticParams()` — Pre-render Dynamic Routes

For SSG (Static Site Generation), tell Next.js which dynamic params to pre-render at build time:

```jsx
// app/products/[id]/page.jsx

// Pre-generate pages for products 1–20 at build time
export async function generateStaticParams() {
  const products = await fetch("https://fakestoreapi.com/products")
    .then((r) => r.json());

  return products.map((product) => ({
    id: String(product.id),
  }));
}

export default async function ProductPage({ params }) {
  const product = await fetch(`https://fakestoreapi.com/products/${params.id}`)
    .then((r) => r.json());

  return <h1>{product.title}</h1>;
}
```

🧠 This creates static HTML files at build time for each product — ultra-fast page loads!

---

## 5️⃣ Catch-All Routes

Capture multiple URL segments with `[...slug]`:

```
app/
└── docs/
    └── [...slug]/
        └── page.jsx   → /docs/intro, /docs/react/hooks, /docs/a/b/c
```

```jsx
// app/docs/[...slug]/page.jsx
export default function DocsPage({ params }) {
  // params.slug = ["react", "hooks"] for /docs/react/hooks
  const breadcrumb = params.slug.join(" / ");

  return (
    <div>
      <p>Path: {breadcrumb}</p>
      <h1>Docs: {params.slug[params.slug.length - 1]}</h1>
    </div>
  );
}
```

### Optional Catch-All `[[...slug]]`

Also matches the base path (`/docs` itself):

```
app/docs/[[...slug]]/page.jsx  →  /docs, /docs/intro, /docs/a/b/c
```

---

## 6️⃣ Route Groups — `(group)` Folders

Route Groups organize files **without affecting the URL**. The folder name in `()` is ignored in the URL.

```
app/
├── (marketing)/         ← group — NOT in URL
│   ├── layout.jsx       ← layout for marketing pages only
│   ├── page.jsx         → /
│   ├── about/
│   │   └── page.jsx     → /about
│   └── pricing/
│       └── page.jsx     → /pricing
│
└── (dashboard)/         ← group — NOT in URL
    ├── layout.jsx       ← layout for dashboard pages only
    └── dashboard/
        └── page.jsx     → /dashboard
```

### Why Use Route Groups?

1. **Different layouts for different sections** — without nesting URLs
2. **Organize routes** without creating URL segments
3. **Multiple root layouts** — each group can have its own `layout.jsx`

```jsx
// app/(marketing)/layout.jsx
export default function MarketingLayout({ children }) {
  return (
    <div>
      <header>Marketing Navbar</header>
      {children}
      <footer>Marketing Footer</footer>
    </div>
  );
}

// app/(dashboard)/layout.jsx
export default function DashboardLayout({ children }) {
  return (
    <div style={{ display: "flex" }}>
      <aside>Dashboard Sidebar</aside>
      <main>{children}</main>
    </div>
  );
}
```

---

## 7️⃣ Parallel Routes — `@slot` Folders

Parallel Routes allow you to render **multiple pages in the same layout** simultaneously. Useful for dashboards with side panels.

```
app/
└── dashboard/
    ├── layout.jsx
    ├── page.jsx
    ├── @analytics/
    │   └── page.jsx    ← analytics panel
    └── @team/
        └── page.jsx    ← team panel
```

```jsx
// app/dashboard/layout.jsx
export default function DashboardLayout({ children, analytics, team }) {
  return (
    <div>
      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
        <div>{analytics}</div>
        <div>{team}</div>
      </div>
      {children}
    </div>
  );
}
```

```jsx
// app/dashboard/@analytics/page.jsx
export default function Analytics() {
  return <div style={{ padding: 16, border: "1px solid #ddd", borderRadius: 8 }}>📊 Analytics Panel</div>;
}

// app/dashboard/@team/page.jsx
export default function Team() {
  return <div style={{ padding: 16, border: "1px solid #ddd", borderRadius: 8 }}>👥 Team Panel</div>;
}
```

---

## 🎯 Interview Questions

**Q1: What is a dynamic route in Next.js?**

> A dynamic route uses a folder with square brackets like `[id]`. Any value in that URL position maps to `params.id` inside the page component. For example, `app/users/[id]/page.jsx` handles `/users/1`, `/users/42`, etc.

**Q2: What is `generateStaticParams` used for?**

> It tells Next.js which param values to pre-render as static HTML at build time. Without it, dynamic routes are rendered on demand (SSR). With it, pages are generated ahead of time (SSG) — much faster for users.

**Q3: What are Route Groups and why are they useful?**

> Route Groups use `(name)` folder syntax — the name doesn't appear in the URL. They're used to organize code (e.g., group all marketing pages together) and to apply different layouts to different sections without affecting the URL structure.

**Q4: What is the difference between `[id]`, `[...slug]`, and `[[...slug]]`?**

> `[id]` matches exactly one segment. `[...slug]` matches one or more segments (required). `[[...slug]]` matches zero or more segments — the route also matches the base path (e.g., `/docs` without any slug).

---

## 🏠 Home Task

Build a **Products Catalog** app:
1. Products list page at `/products` — fetch from `https://fakestoreapi.com/products`
2. Product detail page at `/products/[id]` — show full product details
3. Use `generateStaticParams` to pre-render all product detail pages
4. Add dynamic `generateMetadata` that sets the page title to the product name
5. Create a `/docs/[[...slug]]` route that shows the current path
6. Use Route Groups to have a `(shop)` layout (with cart icon) separate from the main layout
