# ⚡ What is Next.js & Project Setup

## 📚 Topics Covered
- What is Next.js and why use it over plain React
- Next.js vs React — key differences
- Pages Router vs App Router (Next.js 13+)
- Creating a project with `create-next-app`
- Project folder structure — `app/`, `public/`, `components/`
- Special files — `page.jsx`, `layout.jsx`, `loading.jsx`, `error.jsx`
- Running dev server — `npm run dev`
- Understanding the `app/` directory

---

## 1️⃣ What is Next.js?

**Next.js** is a **React framework** built on top of React that adds:

- **File-based routing** — no need for React Router
- **Server-Side Rendering (SSR)** — HTML generated on the server
- **Static Site Generation (SSG)** — HTML generated at build time
- **API Routes** — backend endpoints inside your frontend project
- **Image & Font optimization** — built-in performance tools
- **SEO-friendly** — server-rendered HTML is indexable by search engines

> Think of it this way: **React is a library**, **Next.js is a full framework** built on React.

---

## 2️⃣ Why Next.js Over Plain React?

| Problem with React (Vite/CRA) | How Next.js Solves It |
| ----------------------------- | --------------------- |
| Routing needs React Router DOM | File-based routing built-in |
| All rendering is client-side (bad SEO) | SSR / SSG out of the box |
| No backend — need a separate server | Route Handlers (API) built-in |
| Images not optimized by default | `<Image>` component auto-optimizes |
| Fonts cause layout shift | `next/font` eliminates layout shift |
| Environment variables need manual config | Built-in `.env` with `NEXT_PUBLIC_` |

---

## 3️⃣ Pages Router vs App Router

Next.js has two routing systems:

| | Pages Router (`pages/`) | App Router (`app/`) |
|--|------------------------|---------------------|
| Introduced | Next.js v1 | Next.js v13 |
| Default in | Old projects | New projects (v13+) |
| Server Components | ❌ | ✅ |
| Layouts | Manual `_app.js` | `layout.jsx` |
| Streaming | ❌ | ✅ |
| Data fetching | `getServerSideProps`, `getStaticProps` | `async` components + `fetch()` |
| Recommended | Legacy projects | ✅ All new projects |

> We will use the **App Router** throughout this course.

---

## 4️⃣ Installation & Project Setup

### Step 1: Create a new Next.js project

```bash
npx create-next-app@latest my-next-app
```

You will be asked:

```
✔ Would you like to use TypeScript? → No
✔ Would you like to use ESLint? → Yes
✔ Would you like to use Tailwind CSS? → Yes (optional)
✔ Would you like to use `src/` directory? → No
✔ Would you like to use App Router? → Yes ✅
✔ Would you like to customize the default import alias? → No
```

### Step 2: Move into the project and start the dev server

```bash
cd my-next-app
npm run dev
```

Open `http://localhost:3000` in your browser.

---

## 5️⃣ Project Folder Structure

```
my-next-app/
├── app/                   ← App Router lives here
│   ├── layout.jsx         ← Root layout (wraps all pages)
│   ├── page.jsx           ← Home page (route: /)
│   ├── globals.css        ← Global styles
│   └── favicon.ico
├── public/                ← Static files (images, fonts)
│   └── next.svg
├── components/            ← Reusable components (you create this)
├── next.config.mjs        ← Next.js configuration
├── package.json
└── .env.local             ← Environment variables
```

---

## 6️⃣ Special Files in the `app/` Directory

| File | Purpose |
| ---- | ------- |
| `page.jsx` | The route UI — what users see at that URL |
| `layout.jsx` | Shared wrapper that persists across navigation |
| `loading.jsx` | Shown automatically while page is loading |
| `error.jsx` | Shown automatically when an error occurs |
| `not-found.jsx` | Shown when route is not found (404) |
| `route.js` | API endpoint (Route Handler) |

---

## 7️⃣ Your First Page

```jsx
// app/page.jsx
export default function HomePage() {
  return (
    <div>
      <h1>Welcome to Next.js!</h1>
      <p>This is the home page at /</p>
    </div>
  );
}
```

### Root Layout

```jsx
// app/layout.jsx
export const metadata = {
  title: "My Next.js App",
  description: "Built with Next.js",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

🧠 **Key Point:** `layout.jsx` wraps every page. It does NOT re-render on navigation — it persists. This is how shared navbars and sidebars work.

---

## 8️⃣ Next.js vs React — Quick Comparison

```
React (Vite):                     Next.js:
src/
├── App.jsx                       app/
├── main.jsx                      ├── layout.jsx   (root layout)
├── pages/                        ├── page.jsx     (home route /)
│   ├── Home.jsx                  ├── about/
│   ├── About.jsx                 │   └── page.jsx (route /about)
│   └── Contact.jsx               └── contact/
└── router setup in main.jsx          └── page.jsx (route /contact)
```

In Next.js, **creating a file creates a route** — no router configuration needed.

---

## 🎯 Interview Questions

**Q1: What is Next.js and why would you use it over React?**

> Next.js is a React framework that adds server-side rendering, static site generation, file-based routing, API routes, and built-in optimizations. You'd use it when you need SEO, better performance, or a full-stack solution without a separate backend.

**Q2: What is the difference between the Pages Router and the App Router?**

> The Pages Router (pre-v13) uses a `pages/` directory with `getServerSideProps`/`getStaticProps` for data fetching. The App Router (v13+) uses the `app/` directory with React Server Components, async components, and `layout.jsx` files for shared UI. The App Router is the modern, recommended approach.

**Q3: What does `layout.jsx` do in Next.js?**

> It wraps all pages in the same directory and its subdirectories. Unlike `page.jsx`, the layout does NOT re-render when navigating between pages — it persists. This makes it perfect for navbars, sidebars, and global providers.

**Q4: What is the `public/` folder used for?**

> It stores static assets like images, fonts, and SVGs that are served at the root URL. A file at `public/logo.png` is accessible at `/logo.png`. These files are not processed by webpack.

---

## 🏠 Home Task

Set up your first Next.js project:
1. Create a new Next.js app with `create-next-app`
2. Explore the folder structure — understand each file's purpose
3. Modify `app/page.jsx` to show your name and a welcome message
4. Modify `app/layout.jsx` to update the page title in metadata
5. Create a new folder `app/about/` with a `page.jsx` file — visit `/about` in the browser
6. Add a simple `<nav>` in `layout.jsx` with links to Home and About
