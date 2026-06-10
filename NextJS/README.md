# ⚡ Next.js — Complete Course Outline

> **Next.js** is a React framework that adds Server-Side Rendering (SSR), Static Site Generation (SSG), API Routes, and much more on top of React. It is the most popular way to build production-ready React applications.

---

## 🗺️ Course Structure — 10 Weeks

```
NextJS/
├── week_1/
│   ├── class_1/    → What is Next.js & Project Setup
│   └── class_2/    → File-Based Routing & Pages
├── week_2/
│   ├── class_1/    → App Router — Layouts, Loading, Error UI
│   └── class_2/    → Dynamic Routes, Route Groups, Parallel Routes
├── week_3/
│   ├── class_1/    → Server Components vs Client Components
│   └── class_2/    → Data Fetching — SSG, SSR, ISR
├── week_4/
│   ├── class_1/    → Server Actions & Form Handling
│   └── class_2/    → Route Handlers (API Routes)
├── week_5/
│   ├── class_1/    → Middleware & Authentication (NextAuth.js)
│   └── class_2/    → Protected Routes & Session Management
├── week_6/
│   ├── class_1/    → Database Integration (Prisma + PostgreSQL)
│   └── class_2/    → Full CRUD App with Database
├── week_7/
│   ├── class_1/    → next/image, next/font — Optimization
│   └── class_2/    → Metadata API & SEO
├── week_8/
│   ├── class_1/    → Caching Strategies in Next.js
│   └── class_2/    → Streaming & Suspense
├── week_9/
│   ├── class_1/    → State Management in Next.js (Zustand/Context)
│   └── class_2/    → TanStack Query with Next.js
└── week_10/
    ├── class_1/    → Environment Variables & Deployment
    └── class_2/    → Full Stack Project — E-Commerce / Blog
```

---

## 📚 All Topics — Complete List

### Week 1 — Introduction & Project Setup
- What is Next.js and why use it over plain React
- Next.js vs React — key differences
- Pages Router vs App Router (Next.js 13+)
- Creating a project with `create-next-app`
- Project folder structure — `app/`, `public/`, `components/`
- `page.jsx`, `layout.jsx`, `loading.jsx`, `error.jsx`, `not-found.jsx`
- Running dev server — `npm run dev`
- File-based routing — creating pages by creating files

---

### Week 2 — Routing (App Router)
- How App Router routing works
- `page.jsx` — the route component
- `layout.jsx` — shared UI wrapper (persists across routes)
- `template.jsx` — re-mounts on navigation
- `loading.jsx` — automatic Suspense fallback
- `error.jsx` — automatic Error Boundary
- `not-found.jsx` — 404 page
- Dynamic routes — `[id]`, `[slug]`
- Catch-all routes — `[...slug]`
- Optional catch-all — `[[...slug]]`
- Route Groups — `(group)` folders for organization without URL
- Parallel Routes — `@slot` folders
- `useRouter`, `usePathname`, `useParams`, `useSearchParams`
- `Link` component and programmatic navigation
- `redirect()` and `notFound()` functions

---

### Week 3 — Server Components vs Client Components
- What are React Server Components (RSC)
- Server Components — no JavaScript sent to browser
- Client Components — `"use client"` directive
- When to use Server vs Client components
- How they can be composed together
- Data fetching directly in Server Components (async components)
- `fetch()` in Next.js — extended with caching options
- **Static Site Generation (SSG)** — `cache: 'force-cache'`
- **Server-Side Rendering (SSR)** — `cache: 'no-store'`
- **Incremental Static Regeneration (ISR)** — `revalidate: 60`
- `generateStaticParams()` — pre-generating dynamic routes
- Waterfall vs parallel data fetching
- `Promise.all()` for parallel fetching

---

### Week 4 — Server Actions & Route Handlers
- What are Server Actions
- `"use server"` directive
- Form submission with Server Actions — no API needed
- `useFormState` — form state with server action
- `useFormStatus` — pending state for form buttons
- Mutations — create, update, delete via Server Actions
- `revalidatePath()` and `revalidateTag()` — clearing cache after mutation
- Route Handlers — `route.js` file
- `GET`, `POST`, `PUT`, `PATCH`, `DELETE` handlers
- `Request` and `Response` objects
- `NextRequest` and `NextResponse`
- Reading search params, headers, cookies in handlers
- Building a REST API inside Next.js

---

### Week 5 — Middleware & Authentication
- What is Middleware in Next.js
- `middleware.js` — runs before every request
- `matcher` config — apply middleware to specific routes
- Redirecting and rewriting requests in middleware
- **NextAuth.js (Auth.js)** — authentication library
- Setting up NextAuth with providers (Google, GitHub, Credentials)
- `SessionProvider` and `useSession`
- `getServerSession()` — reading session on server
- JWT vs Database sessions
- Protected routes — redirecting unauthenticated users
- Role-based access control
- `signIn()`, `signOut()`, `getCsrfToken()`

---

### Week 6 — Database Integration
- **Prisma ORM** — setup and configuration
- `schema.prisma` — defining data models
- Prisma migrations — `prisma migrate dev`
- Prisma Client — `findMany`, `findUnique`, `create`, `update`, `delete`
- Connecting Prisma to PostgreSQL / MySQL / SQLite
- Using Prisma inside Server Components and Route Handlers
- **MongoDB with Mongoose** — alternative approach
- Database seeding
- Relations — one-to-many, many-to-many
- Full CRUD with Next.js + Prisma + PostgreSQL

---

### Week 7 — Image, Font & Metadata Optimization
- **`next/image`** — `<Image>` component
  - Automatic WebP conversion
  - Lazy loading by default
  - `width`, `height`, `fill`, `priority` props
  - Remote image domains configuration
- **`next/font`** — `<Font>` optimization
  - Google Fonts with zero layout shift
  - Local fonts
  - `variable` fonts
- **Metadata API**
  - Static metadata — `export const metadata`
  - Dynamic metadata — `generateMetadata()` function
  - `title`, `description`, `openGraph`, `twitter` cards
  - `robots`, `canonical`, `icons`
  - Metadata inheritance through layouts
- Favicon and app icons

---

### Week 8 — Caching & Streaming
- Next.js 4-layer caching system overview
- **Request Memoization** — same fetch deduplicated in one render
- **Data Cache** — persistent fetch cache across requests
- **Full Route Cache** — cached HTML + RSC payload
- **Router Cache** — client-side navigation cache
- `revalidatePath()` — invalidate a specific path
- `revalidateTag()` — tag-based cache invalidation
- `unstable_cache()` — cache non-fetch functions
- **Streaming** — progressive UI rendering
- `Suspense` with async Server Components
- `loading.js` — automatic route-level streaming
- Streaming specific components while rest of page loads

---

### Week 9 — State Management & Data Fetching Libraries
- State management patterns in Next.js
- Where to put client state vs server state
- **Zustand** — lightweight global state
  - Setup, stores, actions
  - Using Zustand with Server Components
- **Context API** in Next.js — client component provider pattern
- **TanStack Query** with Next.js
  - `QueryClientProvider` in a client component
  - Hydration — prefetching on server, using on client
  - `dehydrate` and `HydrationBoundary`
  - `prefetchQuery` in Server Components
  - Combining server fetching with client caching

---

### Week 10 — Deployment & Final Project
- **Environment Variables** in Next.js
  - `NEXT_PUBLIC_` prefix for browser-exposed vars
  - Server-only variables (no prefix)
  - `.env.local`, `.env.production`
- **Deploying to Vercel** — zero-config deployment
- Deploying to a custom server (Docker, VPS)
- `next build` and `next start`
- Analyzing bundle size — `@next/bundle-analyzer`
- Performance checklist before production
- **Final Project: Full Stack App** — choose one:
  - E-Commerce Store (products, cart, checkout, auth)
  - Blog Platform (posts, comments, auth, admin panel)
  - Task Management App (teams, tasks, real-time updates)

---

## 🔁 React → Next.js Comparison

| Feature | React (Vite) | Next.js |
|---------|-------------|---------|
| Routing | React Router DOM | File-based (built-in) |
| Rendering | Client-side only | SSR + SSG + ISR + CSR |
| Data Fetching | useEffect + fetch | async Server Components |
| API | Separate backend needed | Route Handlers built-in |
| SEO | Poor (CSR) | Excellent (SSR/SSG) |
| Performance | Good | Better (server rendering) |
| Bundle Size | Larger (JS-heavy) | Smaller (server components) |
| Deployment | Any static host | Vercel (best), others |

---

## ⚙️ Prerequisites

Before starting Next.js, students must know:
- ✅ React fundamentals (components, props, state)
- ✅ React hooks (useState, useEffect, useContext)
- ✅ React Router DOM basics
- ✅ JavaScript async/await and Promises
- ✅ Basic REST API concepts (GET, POST, etc.)
