# 🏗️ App Router — Layouts, Loading & Error UI

## 📚 Topics Covered
- `layout.jsx` — shared UI that persists across navigation
- Nested layouts — layouts inside layouts
- `template.jsx` — re-mounts on every navigation
- `loading.jsx` — automatic Suspense fallback
- `error.jsx` — automatic Error Boundary
- `global-error.jsx` — root-level error boundary
- Using `metadata` export for SEO
- `generateMetadata()` — dynamic page titles

---

## 1️⃣ `layout.jsx` — Persistent Shared UI

A `layout.jsx` wraps all pages in its directory and **does not re-mount** on navigation. The layout stays, only the page content changes.

```jsx
// app/layout.jsx  (Root Layout — required)
import "./globals.css";

export const metadata = {
  title: "My App",
  description: "Built with Next.js",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <header style={{ padding: "12px 24px", background: "#1a1a2e", color: "#fff" }}>
          <h2>My App</h2>
        </header>
        <main style={{ padding: 24 }}>
          {children}
        </main>
        <footer style={{ textAlign: "center", padding: 16, color: "#999" }}>
          © 2024 My App
        </footer>
      </body>
    </html>
  );
}
```

🧠 **Key rule:** The root `app/layout.jsx` must contain `<html>` and `<body>` tags — it is required.

---

## 2️⃣ Nested Layouts

You can have a layout for a section (like `/dashboard`) that adds extra UI only for those pages.

```
app/
├── layout.jsx           ← Root layout (Navbar + Footer for ALL pages)
├── page.jsx             ← /
└── dashboard/
    ├── layout.jsx       ← Dashboard layout (Sidebar for /dashboard/* only)
    ├── page.jsx         ← /dashboard
    ├── settings/
    │   └── page.jsx     ← /dashboard/settings
    └── profile/
        └── page.jsx     ← /dashboard/profile
```

```jsx
// app/dashboard/layout.jsx
export default function DashboardLayout({ children }) {
  return (
    <div style={{ display: "flex", minHeight: "100vh" }}>
      {/* Sidebar — only on dashboard routes */}
      <aside style={{ width: 220, background: "#f5f5f5", padding: 16 }}>
        <h3>Dashboard</h3>
        <nav style={{ display: "flex", flexDirection: "column", gap: 8 }}>
          <a href="/dashboard">Overview</a>
          <a href="/dashboard/profile">Profile</a>
          <a href="/dashboard/settings">Settings</a>
        </nav>
      </aside>

      {/* Page content */}
      <div style={{ flex: 1, padding: 24 }}>
        {children}
      </div>
    </div>
  );
}
```

```jsx
// app/dashboard/page.jsx
export default function DashboardPage() {
  return <h1>Dashboard Overview</h1>;
}

// app/dashboard/settings/page.jsx
export default function SettingsPage() {
  return <h1>Settings</h1>;
}
```

✅ The Root layout (Navbar/Footer) + Dashboard layout (Sidebar) both wrap dashboard pages.

---

## 3️⃣ `loading.jsx` — Automatic Loading UI

Create a `loading.jsx` file next to a `page.jsx` — Next.js shows it automatically as a Suspense fallback while the page is loading.

```jsx
// app/products/loading.jsx
export default function Loading() {
  return (
    <div style={{ display: "flex", justifyContent: "center", padding: 60 }}>
      <div style={{
        width: 48,
        height: 48,
        border: "4px solid #eee",
        borderTop: "4px solid #2196f3",
        borderRadius: "50%",
        animation: "spin 0.8s linear infinite",
      }} />
    </div>
  );
}
```

```jsx
// app/products/page.jsx
async function getProducts() {
  await new Promise((r) => setTimeout(r, 2000)); // simulate delay
  const res = await fetch("https://fakestoreapi.com/products");
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div>
      <h1>Products</h1>
      {products.map((p) => (
        <div key={p.id}>
          <h3>{p.title}</h3>
          <p>${p.price}</p>
        </div>
      ))}
    </div>
  );
}
```

🧠 While `getProducts()` is fetching, the `loading.jsx` spinner is shown automatically.

---

## 4️⃣ `error.jsx` — Automatic Error Boundary

Create an `error.jsx` file to catch errors thrown by pages in the same directory.

```jsx
// app/products/error.jsx
"use client"; // error.jsx must be a Client Component

export default function Error({ error, reset }) {
  return (
    <div style={{ textAlign: "center", padding: 60 }}>
      <h2>❌ Something went wrong!</h2>
      <p style={{ color: "#666" }}>{error.message}</p>
      <button
        onClick={reset}
        style={{ marginTop: 16, padding: "8px 20px", background: "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}
      >
        Try Again
      </button>
    </div>
  );
}
```

| Prop | Description |
| ---- | ----------- |
| `error` | The Error object that was thrown |
| `reset` | Function to re-render the page and retry |

---

## 5️⃣ `global-error.jsx` — Root Error Boundary

```jsx
// app/global-error.jsx
"use client";

export default function GlobalError({ error, reset }) {
  return (
    <html>
      <body>
        <h2>Something went wrong globally!</h2>
        <button onClick={reset}>Try again</button>
      </body>
    </html>
  );
}
```

> `global-error.jsx` replaces the root layout when it activates — so it needs its own `<html>` and `<body>` tags.

---

## 6️⃣ Metadata — Page Titles & SEO

### Static Metadata

```jsx
// app/about/page.jsx
export const metadata = {
  title: "About Us — My App",
  description: "Learn more about our team and mission.",
  openGraph: {
    title: "About Us",
    description: "Learn more about our team",
    images: ["/og-about.png"],
  },
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

### Dynamic Metadata

```jsx
// app/products/[id]/page.jsx
export async function generateMetadata({ params }) {
  const product = await fetch(`https://fakestoreapi.com/products/${params.id}`)
    .then((r) => r.json());

  return {
    title: `${product.title} — My Shop`,
    description: product.description,
  };
}

export default async function ProductPage({ params }) {
  const product = await fetch(`https://fakestoreapi.com/products/${params.id}`)
    .then((r) => r.json());

  return <h1>{product.title}</h1>;
}
```

---

## 7️⃣ File Hierarchy Summary

```
app/
├── layout.jsx        ← Root layout (required, wraps everything)
├── page.jsx          ← /
├── loading.jsx       ← Loading UI for /
├── error.jsx         ← Error boundary for /
├── not-found.jsx     ← 404 for /
├── global-error.jsx  ← Root-level error boundary
└── dashboard/
    ├── layout.jsx    ← Nested layout for /dashboard/*
    ├── page.jsx      ← /dashboard
    ├── loading.jsx   ← Loading UI for /dashboard
    └── error.jsx     ← Error boundary for /dashboard
```

---

## 🎯 Interview Questions

**Q1: What is the difference between `layout.jsx` and `page.jsx`?**

> `page.jsx` is the unique UI for a route — it re-renders on every navigation. `layout.jsx` wraps pages and **persists** across navigations within its scope. The DOM nodes in a layout are not destroyed when navigating to a child route, making it ideal for navbars and sidebars.

**Q2: How does `loading.jsx` work?**

> Next.js automatically wraps the `page.jsx` in a `<Suspense>` boundary with `loading.jsx` as the fallback. While the async page component is fetching data, the loading UI is shown instantly without any extra code.

**Q3: Why must `error.jsx` be a Client Component?**

> Error boundaries in React must be class components or use the `error` and `reset` props pattern, which requires interactivity (the reset button). Next.js requires `"use client"` on `error.jsx` because error boundaries need to be client-side to handle runtime errors and provide the retry callback.

**Q4: What is the difference between `layout.jsx` and `template.jsx`?**

> `layout.jsx` persists — its state and DOM are kept when navigating between child pages. `template.jsx` re-mounts on every navigation, resetting all state. Use `template.jsx` when you need animations between routes or want to reset state on each visit.

---

## 🏠 Home Task

Build a **Dashboard App** with proper layout structure:
1. Root layout with a Navbar showing app name and navigation links
2. A `/dashboard` section with its own nested layout showing a Sidebar
3. Dashboard sub-pages: `/dashboard`, `/dashboard/profile`, `/dashboard/settings`
4. Add `loading.jsx` to `/dashboard` — simulate a 1-second delay with `setTimeout` in the page
5. Add `error.jsx` to `/dashboard` with a "Try Again" button
6. Add proper `metadata` export to each page with a unique `title`
