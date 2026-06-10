# 🗺️ File-Based Routing & Pages

## 📚 Topics Covered
- How file-based routing works in Next.js App Router
- Creating routes by creating folders and `page.jsx` files
- Nested routes — folders inside folders
- `Link` component — client-side navigation
- `useRouter`, `usePathname`, `useParams` hooks
- Active link styling
- `redirect()` and `notFound()` functions
- 404 page with `not-found.jsx`

---

## 1️⃣ How File-Based Routing Works

In Next.js, **the folder structure = the URL structure**.

```
app/
├── page.jsx           →  /
├── about/
│   └── page.jsx       →  /about
├── contact/
│   └── page.jsx       →  /contact
├── blog/
│   ├── page.jsx       →  /blog
│   └── [slug]/
│       └── page.jsx   →  /blog/any-post-here
```

> You don't write any router configuration — just create a folder and add `page.jsx`.

---

## 2️⃣ Creating Pages

### Home Page

```jsx
// app/page.jsx
export default function HomePage() {
  return (
    <main>
      <h1>Home Page</h1>
      <p>Welcome to my Next.js site!</p>
    </main>
  );
}
```

### About Page

```jsx
// app/about/page.jsx
export default function AboutPage() {
  return (
    <main>
      <h1>About Us</h1>
      <p>We build awesome web apps.</p>
    </main>
  );
}
```

### Contact Page

```jsx
// app/contact/page.jsx
export default function ContactPage() {
  return (
    <main>
      <h1>Contact</h1>
      <p>Email us at hello@example.com</p>
    </main>
  );
}
```

---

## 3️⃣ Nested Routes

Folders inside folders create nested routes:

```
app/
├── dashboard/
│   ├── page.jsx          →  /dashboard
│   ├── settings/
│   │   └── page.jsx      →  /dashboard/settings
│   └── profile/
│       └── page.jsx      →  /dashboard/profile
```

```jsx
// app/dashboard/page.jsx
export default function DashboardPage() {
  return <h1>Dashboard</h1>;
}

// app/dashboard/settings/page.jsx
export default function SettingsPage() {
  return <h1>Dashboard Settings</h1>;
}
```

---

## 4️⃣ `Link` Component — Client-Side Navigation

Use `<Link>` from `next/link` instead of `<a>` for internal navigation. It doesn't reload the page.

```jsx
// app/components/Navbar.jsx
"use client"; // needed for usePathname

import Link from "next/link";
import { usePathname } from "next/navigation";

export default function Navbar() {
  const pathname = usePathname();

  const links = [
    { href: "/", label: "Home" },
    { href: "/about", label: "About" },
    { href: "/contact", label: "Contact" },
  ];

  return (
    <nav style={{ display: "flex", gap: 16, padding: 16, background: "#1a1a2e" }}>
      {links.map((link) => (
        <Link
          key={link.href}
          href={link.href}
          style={{
            color: pathname === link.href ? "#2196f3" : "#fff",
            textDecoration: "none",
            fontWeight: pathname === link.href ? "bold" : "normal",
          }}
        >
          {link.label}
        </Link>
      ))}
    </nav>
  );
}
```

Add the Navbar to the root layout:

```jsx
// app/layout.jsx
import Navbar from "../components/Navbar";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <Navbar />
        <main>{children}</main>
      </body>
    </html>
  );
}
```

---

## 5️⃣ `usePathname` — Current Route

```jsx
"use client";
import { usePathname } from "next/navigation";

function ActiveExample() {
  const pathname = usePathname();
  // pathname = "/about" when on the About page
  return <p>Current path: {pathname}</p>;
}
```

---

## 6️⃣ `useRouter` — Programmatic Navigation

```jsx
"use client";
import { useRouter } from "next/navigation";

function GoBackButton() {
  const router = useRouter();

  return (
    <div>
      <button onClick={() => router.push("/")}>Go Home</button>
      <button onClick={() => router.back()}>Go Back</button>
      <button onClick={() => router.replace("/login")}>Replace with Login</button>
    </div>
  );
}
```

| Method | What it does |
| ------ | ------------ |
| `router.push("/path")` | Navigate to path (adds to history) |
| `router.replace("/path")` | Navigate, replacing current history entry |
| `router.back()` | Go back one page |
| `router.forward()` | Go forward one page |
| `router.refresh()` | Refresh current page data |

---

## 7️⃣ `redirect()` — Server-Side Redirect

```jsx
// app/dashboard/page.jsx
import { redirect } from "next/navigation";

export default function DashboardPage() {
  const isLoggedIn = false; // check auth here

  if (!isLoggedIn) {
    redirect("/login"); // redirects on the server
  }

  return <h1>Dashboard</h1>;
}
```

---

## 8️⃣ `notFound()` — Trigger 404

```jsx
// app/products/[id]/page.jsx
import { notFound } from "next/navigation";

async function getProduct(id) {
  const res = await fetch(`https://fakestoreapi.com/products/${id}`);
  if (!res.ok) return null;
  return res.json();
}

export default async function ProductPage({ params }) {
  const product = await getProduct(params.id);

  if (!product) {
    notFound(); // renders not-found.jsx
  }

  return <h1>{product.title}</h1>;
}
```

### Custom 404 Page

```jsx
// app/not-found.jsx
import Link from "next/link";

export default function NotFound() {
  return (
    <div style={{ textAlign: "center", padding: "60px 20px" }}>
      <h1 style={{ fontSize: 72 }}>404</h1>
      <h2>Page Not Found</h2>
      <p>The page you are looking for does not exist.</p>
      <Link href="/" style={{ color: "#2196f3" }}>Go back home</Link>
    </div>
  );
}
```

---

## 9️⃣ Full Example — Multi-Page App

```
app/
├── layout.jsx       ← root layout with Navbar
├── page.jsx         ← /
├── about/
│   └── page.jsx     ← /about
├── blog/
│   └── page.jsx     ← /blog
└── not-found.jsx    ← 404 page
```

```jsx
// app/layout.jsx
import Link from "next/link";

export const metadata = { title: "My Next.js App" };

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <nav style={{ padding: "12px 24px", background: "#111", display: "flex", gap: 20 }}>
          <Link href="/" style={{ color: "#fff", textDecoration: "none" }}>Home</Link>
          <Link href="/about" style={{ color: "#fff", textDecoration: "none" }}>About</Link>
          <Link href="/blog" style={{ color: "#fff", textDecoration: "none" }}>Blog</Link>
        </nav>
        {children}
      </body>
    </html>
  );
}
```

```jsx
// app/page.jsx
export default function Home() {
  return <h1 style={{ padding: 24 }}>🏠 Home Page</h1>;
}

// app/about/page.jsx
export default function About() {
  return <h1 style={{ padding: 24 }}>ℹ️ About Page</h1>;
}

// app/blog/page.jsx
export default function Blog() {
  return <h1 style={{ padding: 24 }}>📝 Blog Page</h1>;
}
```

---

## 🎯 Interview Questions

**Q1: How does file-based routing work in Next.js?**

> Every folder inside `app/` becomes a URL segment. A `page.jsx` file inside that folder is the component rendered at that URL. Nesting folders creates nested routes — no router configuration is needed.

**Q2: What is the difference between `<Link>` and `<a>` in Next.js?**

> `<a>` causes a full page reload. `<Link>` from `next/link` performs client-side navigation — only the changed component re-renders, keeping the layout mounted. It also prefetches the linked page in the background for faster navigation.

**Q3: When would you use `redirect()` vs `router.push()`?**

> `redirect()` runs on the server (in Server Components or Route Handlers) — the browser never sees the original page. `router.push()` runs client-side and is used in Client Components to navigate after user interaction like a button click.

**Q4: What does `notFound()` do?**

> It triggers the nearest `not-found.jsx` file to render. Used when a requested resource (like a product or user by ID) doesn't exist. Without calling `notFound()`, you'd need to manually return a 404 response.

---

## 🏠 Home Task

Build a **Multi-Page Website** with Next.js:
1. Create 4 pages: `/`, `/about`, `/services`, `/contact`
2. Add a `Navbar` component in `components/Navbar.jsx` with `<Link>` for all pages
3. Import the Navbar into `layout.jsx` so it shows on all pages
4. Use `usePathname` to highlight the active link in the Navbar
5. Create a `not-found.jsx` with a custom 404 message and a link back to Home
6. Add a "Go to Dashboard" button using `useRouter` — it should redirect to `/dashboard` (which shows 404 since it doesn't exist)
