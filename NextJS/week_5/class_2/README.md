# 🛡️ Protected Routes & Session Management

## 📚 Topics Covered
- Protecting pages with `auth()` in Server Components
- Middleware-based route protection
- Role-based access control (RBAC)
- Session data — reading user info
- Updating session with `update()`
- Database sessions vs JWT sessions
- `callbacks` — customizing session and JWT
- Logout and session clearing

---

## 1️⃣ Three Ways to Protect Routes

### Method 1: In the Page (Server Component)

```jsx
// app/admin/page.jsx
import { auth } from "@/auth";
import { redirect } from "next/navigation";

export default async function AdminPage() {
  const session = await auth();

  if (!session) {
    redirect("/login");
  }

  if (session.user.role !== "admin") {
    redirect("/unauthorized");
  }

  return (
    <div>
      <h1>Admin Panel</h1>
      <p>Welcome, {session.user.name}</p>
    </div>
  );
}
```

### Method 2: Middleware (Recommended for bulk protection)

```js
// middleware.js
import { auth } from "@/auth";

export default auth((req) => {
  const { pathname } = req.nextUrl;
  const isLoggedIn = !!req.auth;

  // Protect all /dashboard routes
  if (pathname.startsWith("/dashboard") && !isLoggedIn) {
    return Response.redirect(new URL("/login", req.url));
  }

  // Protect admin routes — check role
  if (pathname.startsWith("/admin")) {
    if (!isLoggedIn) return Response.redirect(new URL("/login", req.url));
    if (req.auth?.user?.role !== "admin") {
      return Response.redirect(new URL("/unauthorized", req.url));
    }
  }
});

export const config = {
  matcher: ["/((?!api|_next/static|_next/image|favicon.ico).*)"],
};
```

### Method 3: HOC — `withAuth` wrapper

```jsx
// lib/withAuth.js
import { auth } from "@/auth";
import { redirect } from "next/navigation";

export function withAuth(Component, { requiredRole } = {}) {
  return async function ProtectedPage(props) {
    const session = await auth();

    if (!session) redirect("/login");
    if (requiredRole && session.user.role !== requiredRole) redirect("/unauthorized");

    return <Component {...props} session={session} />;
  };
}

// Usage:
// export default withAuth(AdminDashboard, { requiredRole: "admin" });
```

---

## 2️⃣ Role-Based Access Control (RBAC)

### Auth Config — Adding Role to Session

```js
// auth.js
export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    Credentials({
      async authorize(credentials) {
        // Fetch user from database
        const user = await db.users.findByEmail(credentials.email);

        if (!user || !bcrypt.compareSync(credentials.password, user.password)) {
          return null;
        }

        return {
          id: user.id,
          name: user.name,
          email: user.email,
          role: user.role,      // "admin" | "editor" | "user"
          avatar: user.avatar,
        };
      },
    }),
  ],

  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.role = user.role;
        token.id = user.id;
        token.avatar = user.avatar;
      }
      return token;
    },
    async session({ session, token }) {
      session.user.role = token.role;
      session.user.id = token.id;
      session.user.avatar = token.avatar;
      return session;
    },
  },
});
```

### Using Roles in Components

```jsx
// app/dashboard/layout.jsx
import { auth } from "@/auth";

export default async function DashboardLayout({ children }) {
  const session = await auth();

  return (
    <div style={{ display: "flex" }}>
      <aside style={{ width: 220, padding: 16, background: "#f5f5f5" }}>
        <nav>
          <a href="/dashboard">📊 Overview</a>
          <a href="/dashboard/profile">👤 Profile</a>

          {/* Only show for admins */}
          {session?.user?.role === "admin" && (
            <a href="/dashboard/users">👥 Manage Users</a>
          )}
          {session?.user?.role === "admin" && (
            <a href="/dashboard/settings">⚙️ Settings</a>
          )}
        </nav>
      </aside>
      <main style={{ flex: 1, padding: 24 }}>{children}</main>
    </div>
  );
}
```

---

## 3️⃣ Unauthorized Page

```jsx
// app/unauthorized/page.jsx
import Link from "next/link";

export default function UnauthorizedPage() {
  return (
    <div style={{ textAlign: "center", padding: 80 }}>
      <h1 style={{ fontSize: 60 }}>🚫</h1>
      <h2>Access Denied</h2>
      <p>You do not have permission to view this page.</p>
      <Link href="/dashboard" style={{ color: "#2196f3" }}>
        Back to Dashboard
      </Link>
    </div>
  );
}
```

---

## 4️⃣ Session Data in Client Components

```jsx
// components/UserProfile.jsx
"use client";
import { useSession } from "next-auth/react";

export default function UserProfile() {
  const { data: session, status } = useSession();

  if (status === "loading") {
    return <div>Loading profile...</div>;
  }

  if (status === "unauthenticated") {
    return <div>Please sign in to view your profile.</div>;
  }

  return (
    <div style={{ padding: 24, border: "1px solid #eee", borderRadius: 8 }}>
      <img
        src={session.user.image || "/default-avatar.png"}
        alt={session.user.name}
        style={{ width: 80, height: 80, borderRadius: "50%", marginBottom: 12 }}
      />
      <h2>{session.user.name}</h2>
      <p>{session.user.email}</p>
      <span style={{
        display: "inline-block",
        padding: "4px 10px",
        background: session.user.role === "admin" ? "#e3f2fd" : "#f5f5f5",
        color: session.user.role === "admin" ? "#1976d2" : "#666",
        borderRadius: 4,
        fontSize: 13,
      }}>
        {session.user.role}
      </span>
    </div>
  );
}
```

---

## 5️⃣ Sign Out

```jsx
// Client Component — with confirmation
"use client";
import { signOut } from "next-auth/react";

export default function SignOutButton() {
  const handleSignOut = async () => {
    if (window.confirm("Are you sure you want to sign out?")) {
      await signOut({ callbackUrl: "/" });
    }
  };

  return (
    <button
      onClick={handleSignOut}
      style={{ padding: "8px 16px", background: "#f44336", color: "#fff", border: "none", borderRadius: 4 }}
    >
      Sign Out
    </button>
  );
}
```

```jsx
// Server Component — via form + Server Action
import { signOut } from "@/auth";

export default function SignOutForm() {
  return (
    <form
      action={async () => {
        "use server";
        await signOut({ redirectTo: "/" });
      }}
    >
      <button type="submit">Sign Out</button>
    </form>
  );
}
```

---

## 6️⃣ JWT vs Database Sessions

| | JWT Sessions | Database Sessions |
|--|-------------|-----------------|
| **How stored** | Encrypted cookie | Database record |
| **Scalable** | ✅ No DB lookup needed | ❌ DB query per request |
| **Revoke anytime** | ❌ Hard — token persists | ✅ Delete session record |
| **Config** | `session: { strategy: "jwt" }` | `session: { strategy: "database" }` |
| **Best for** | Most apps (default) | When you need instant revocation |

```js
// auth.js — to use database sessions
export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db), // requires a database adapter
  session: { strategy: "database" },
  // ...
});
```

---

## 🎯 Interview Questions

**Q1: What are the different ways to protect routes in Next.js with NextAuth?**

> Three approaches: (1) Page-level: call `auth()` inside each Server Component and `redirect()` if no session. (2) Middleware: use the `auth` middleware wrapper to protect all matching routes centrally. (3) Layout-level: check session in a layout to protect all pages within that section. Middleware is the most scalable for protecting many routes.

**Q2: How do you add custom data like `role` to the session?**

> Use NextAuth callbacks: in `jwt`, add data to the token when the user first logs in (`if (user) token.role = user.role`). In `session`, copy from the token to the session (`session.user.role = token.role`). This makes `role` available in `useSession()` and `auth()`.

**Q3: What is the difference between JWT and database sessions?**

> JWT sessions store all session data in an encrypted cookie — stateless and scalable, but you can't invalidate a specific session without changing the secret key. Database sessions store a session ID in a cookie and look up the full session in the database — slower but allows instant session revocation (e.g., for security incidents).

**Q4: Why should you use Middleware instead of checking auth in every page?**

> Middleware runs at the edge before the page renders, so unauthorized users never even receive the page HTML. Page-level checks run after the server starts rendering — the page might briefly flash before redirecting. Middleware also centralizes auth logic so you don't forget to protect a new page.

---

## 🏠 Home Task

Build a **Multi-Role App** with NextAuth:
1. Auth config with Credentials provider — 3 users: one `admin`, one `editor`, one `user`
2. `/dashboard` — accessible by all logged-in users
3. `/dashboard/admin` — only accessible by `admin` role
4. `/dashboard/editor` — accessible by `admin` and `editor` roles
5. Show role-based nav links in the dashboard sidebar
6. Add Middleware to protect all `/dashboard/*` routes from unauthenticated access
7. Show user's name, email, and role on the profile page
8. Sign Out button that redirects to the home page
