# 🔐 Middleware & Authentication (NextAuth.js)

## 📚 Topics Covered
- What is Middleware in Next.js
- `middleware.js` — runs before every request
- `matcher` config — apply middleware to specific routes
- Redirecting and rewriting in middleware
- **NextAuth.js (Auth.js v5)** — authentication library
- Setting up NextAuth with Credentials provider
- Google OAuth provider setup
- `SessionProvider` and `useSession`
- `auth()` — reading session on server
- `signIn()` and `signOut()`

---

## 1️⃣ What is Middleware?

Middleware runs **before a request is completed** — before the page renders or the route handler runs. It can:

- Redirect users (e.g., to login if not authenticated)
- Rewrite URLs
- Add/modify headers
- Check authentication

```
Request → Middleware → Page / API Route → Response
```

---

## 2️⃣ Creating `middleware.js`

The file must be at the **root of the project** (same level as `app/`):

```js
// middleware.js (at project root, not inside app/)
import { NextResponse } from "next/server";

export function middleware(request) {
  const { pathname } = request.nextUrl;
  const token = request.cookies.get("token");

  // Protect dashboard routes
  if (pathname.startsWith("/dashboard") && !token) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  // Redirect logged-in users away from login page
  if (pathname === "/login" && token) {
    return NextResponse.redirect(new URL("/dashboard", request.url));
  }

  return NextResponse.next(); // continue to the page
}

// Apply middleware only to specific paths
export const config = {
  matcher: ["/dashboard/:path*", "/login", "/profile"],
};
```

---

## 3️⃣ Middleware — Matcher Patterns

```js
export const config = {
  matcher: [
    "/dashboard/:path*",     // matches /dashboard and all sub-paths
    "/admin/:path*",         // matches /admin/*
    "/api/protected/:path*", // matches /api/protected/*
    "/((?!_next|favicon.ico|public).*)", // all routes except Next.js internals
  ],
};
```

---

## 4️⃣ NextAuth.js — Authentication Setup

**NextAuth.js (Auth.js v5)** is the most popular authentication library for Next.js.

### Installation

```bash
npm install next-auth@beta
```

### Setup Auth Config

```js
// auth.js (at project root)
import NextAuth from "next-auth";
import Credentials from "next-auth/providers/credentials";
import Google from "next-auth/providers/google";

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    // Email + Password login
    Credentials({
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        // In real app: check database
        if (credentials.email === "admin@test.com" && credentials.password === "123456") {
          return {
            id: "1",
            name: "Admin User",
            email: "admin@test.com",
            role: "admin",
          };
        }
        return null; // return null = login failed
      },
    }),

    // Google OAuth (optional)
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    }),
  ],

  callbacks: {
    // Add role to JWT token
    async jwt({ token, user }) {
      if (user) token.role = user.role;
      return token;
    },
    // Add role to session
    async session({ session, token }) {
      session.user.role = token.role;
      return session;
    },
  },

  pages: {
    signIn: "/login", // custom login page
  },
});
```

### Route Handler for Auth

```js
// app/api/auth/[...nextauth]/route.js
import { handlers } from "@/auth";
export const { GET, POST } = handlers;
```

### Environment Variables

```bash
# .env.local
AUTH_SECRET=your-random-secret-here  # run: openssl rand -base64 32
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
```

---

## 5️⃣ SessionProvider — Client-Side Session Access

```jsx
// app/layout.jsx
import { SessionProvider } from "next-auth/react";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <SessionProvider>
          {children}
        </SessionProvider>
      </body>
    </html>
  );
}
```

---

## 6️⃣ `useSession` — Client Component

```jsx
// components/UserNav.jsx
"use client";
import { useSession, signOut } from "next-auth/react";
import Link from "next/link";

export default function UserNav() {
  const { data: session, status } = useSession();

  if (status === "loading") return <span>Loading...</span>;

  if (!session) {
    return <Link href="/login">Sign In</Link>;
  }

  return (
    <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
      <img
        src={session.user.image || "/default-avatar.png"}
        alt={session.user.name}
        style={{ width: 36, height: 36, borderRadius: "50%" }}
      />
      <span>{session.user.name}</span>
      <button
        onClick={() => signOut({ callbackUrl: "/" })}
        style={{ padding: "6px 12px", background: "#f44336", color: "#fff", border: "none", borderRadius: 4 }}
      >
        Sign Out
      </button>
    </div>
  );
}
```

---

## 7️⃣ `auth()` — Server Component Session

```jsx
// app/dashboard/page.jsx — Server Component
import { auth } from "@/auth";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const session = await auth();

  if (!session) {
    redirect("/login");
  }

  return (
    <div style={{ padding: 24 }}>
      <h1>Dashboard</h1>
      <p>Welcome, <strong>{session.user.name}</strong>!</p>
      <p>Email: {session.user.email}</p>
      {session.user.role === "admin" && (
        <div style={{ background: "#fff3cd", padding: 12, borderRadius: 4 }}>
          🔑 Admin Panel
        </div>
      )}
    </div>
  );
}
```

---

## 8️⃣ Custom Login Page

```jsx
// app/login/page.jsx
"use client";
import { signIn } from "next-auth/react";
import { useState } from "react";
import { useRouter } from "next/navigation";

export default function LoginPage() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");
  const router = useRouter();

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError("");

    const result = await signIn("credentials", {
      email,
      password,
      redirect: false,
    });

    if (result?.error) {
      setError("Invalid email or password");
    } else {
      router.push("/dashboard");
    }
  };

  return (
    <div style={{ maxWidth: 400, margin: "80px auto", padding: 24, border: "1px solid #ddd", borderRadius: 8 }}>
      <h1>Sign In</h1>

      {error && <p style={{ color: "red" }}>{error}</p>}

      <form onSubmit={handleSubmit}>
        <input
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          placeholder="Email"
          style={{ display: "block", width: "100%", padding: 10, marginBottom: 12, borderRadius: 4, border: "1px solid #ddd" }}
        />
        <input
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          placeholder="Password"
          style={{ display: "block", width: "100%", padding: 10, marginBottom: 12, borderRadius: 4, border: "1px solid #ddd" }}
        />
        <button
          type="submit"
          style={{ width: "100%", padding: 12, background: "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}
        >
          Sign In
        </button>
      </form>

      <hr style={{ margin: "20px 0" }} />

      <button
        onClick={() => signIn("google", { callbackUrl: "/dashboard" })}
        style={{ width: "100%", padding: 12, background: "#fff", border: "1px solid #ddd", borderRadius: 4 }}
      >
        Sign in with Google
      </button>
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What is Next.js Middleware and when does it run?**

> Middleware runs at the Edge (before the request reaches the page or API route). It executes on every matched request and can redirect, rewrite, or add headers before the response is sent. It's ideal for authentication checks, A/B testing, and localization.

**Q2: What is NextAuth.js and what providers does it support?**

> NextAuth.js (Auth.js) is a complete authentication library for Next.js. It supports OAuth providers (Google, GitHub, Facebook, Twitter), email/password (Credentials), magic links (Email), and database sessions. It handles JWT tokens, session management, and CSRF protection automatically.

**Q3: What is the difference between `useSession` and `auth()`?**

> `useSession` is a React hook for Client Components — it reads the session from the SessionProvider context. `auth()` is for Server Components and Route Handlers — it directly reads the session from the JWT/database without any client overhead. Always prefer `auth()` in Server Components.

**Q4: What does the `matcher` in middleware config do?**

> `matcher` defines which routes the middleware runs on. Without it, middleware runs on every request (including static files). Using `matcher: ["/dashboard/:path*"]` ensures middleware only runs on dashboard routes, improving performance by skipping unnecessary middleware execution.

---

## 🏠 Home Task

Build a **Protected App** with NextAuth:
1. Install NextAuth and set up `auth.js` with Credentials provider
2. Create a custom `/login` page with email/password form
3. Use `auth()` in `/dashboard/page.jsx` to protect it — redirect to `/login` if no session
4. Show the logged-in user's name and email on the dashboard
5. Add a Sign Out button using `signOut()`
6. Add Middleware to protect all `/dashboard/*` routes
7. Bonus: Add a Google OAuth button (requires Google Cloud Console setup)
