# 🔑 Environment Variables & Deployment

## 📚 Topics Covered
- Environment variables in Next.js
- `NEXT_PUBLIC_` prefix — browser-exposed variables
- Server-only variables (no prefix)
- `.env.local`, `.env.production`, `.env.development`
- Never commit secrets to git
- Deploying to Vercel — zero-config
- Vercel environment variables dashboard
- `npm run build` and `next start`
- React Router fix — `vercel.json` (not needed in Next.js!)
- Common deployment issues and fixes

---

## 1️⃣ Environment Variables in Next.js

```
Variable Type         Accessible In        Use For
─────────────────────────────────────────────────
NEXT_PUBLIC_*         Browser + Server     Public API URLs, maps key
No prefix             Server only          DB password, secret keys, JWT secret
```

---

## 2️⃣ Creating `.env` Files

```bash
# .env.local  (personal local overrides — NEVER commit)
DATABASE_URL="file:./dev.db"
NEXT_PUBLIC_API_URL="http://localhost:3000"
AUTH_SECRET="your-random-secret-here"
GOOGLE_CLIENT_ID="your-id"
GOOGLE_CLIENT_SECRET="your-secret"

# .env.development  (shared dev config — can commit if no secrets)
NEXT_PUBLIC_APP_NAME="My App (Dev)"
NEXT_PUBLIC_ENABLE_DEBUG="true"

# .env.production  (production config — set in hosting dashboard, don't commit)
NEXT_PUBLIC_API_URL="https://api.myapp.com"
NEXT_PUBLIC_APP_NAME="My App"
```

### `.env.example` — Template for Teammates

Always create this file (commit it to git):

```bash
# .env.example
DATABASE_URL="file:./dev.db"
NEXT_PUBLIC_API_URL="http://localhost:3000"
AUTH_SECRET=""                  # run: openssl rand -base64 32
GOOGLE_CLIENT_ID=""
GOOGLE_CLIENT_SECRET=""
NEXT_PUBLIC_APP_NAME="My App"
```

---

## 3️⃣ Accessing Variables in Code

```jsx
// Server Component or Route Handler (server-only vars accessible)
export default async function Page() {
  const dbUrl = process.env.DATABASE_URL;             // ✅ server only
  const apiUrl = process.env.NEXT_PUBLIC_API_URL;     // ✅ server + browser
  const secret = process.env.AUTH_SECRET;             // ✅ server only

  const data = await fetch(apiUrl + "/posts");
  // ...
}
```

```jsx
// Client Component (only NEXT_PUBLIC_ vars accessible)
"use client";
export default function ClientComponent() {
  const apiUrl = process.env.NEXT_PUBLIC_API_URL;     // ✅
  const dbUrl = process.env.DATABASE_URL;             // ❌ undefined in browser!
  // ...
}
```

### Centralize in a config file

```js
// lib/config.js
export const config = {
  apiUrl: process.env.NEXT_PUBLIC_API_URL || "http://localhost:3000",
  appName: process.env.NEXT_PUBLIC_APP_NAME || "My App",
  isDev: process.env.NODE_ENV === "development",
  isProd: process.env.NODE_ENV === "production",
};

// Validate required variables on startup
const required = ["DATABASE_URL", "AUTH_SECRET"];
required.forEach((key) => {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
});
```

---

## 4️⃣ `.gitignore` — Never Commit Secrets

```bash
# .gitignore
.env
.env.local
.env.*.local
.env.production
```

Always commit: `.env.example` (template with empty values)

---

## 5️⃣ Deploying to Vercel

Vercel is the company that created Next.js — it has first-class support.

### Method 1: Vercel CLI

```bash
npm install -g vercel
vercel login
vercel          # in your project folder
# follow prompts
```

### Method 2: GitHub Integration (Recommended)

1. Push your code to GitHub
2. Go to **vercel.com** → Log in with GitHub
3. Click **"New Project"** → Import your repo
4. Configure:
   ```
   Framework Preset: Next.js (auto-detected)
   Build Command: next build (auto)
   Output Directory: .next (auto)
   ```
5. Click **"Deploy"**
6. Every `git push` to `main` → automatic deployment 🎉

---

## 6️⃣ Setting Environment Variables on Vercel

**In Vercel Dashboard:**
1. Go to your project → **Settings** → **Environment Variables**
2. Add each variable with its value
3. Select which environments it applies to (Production, Preview, Development)

```
DATABASE_URL        = postgresql://user:pass@host:5432/db
AUTH_SECRET         = your-production-secret
GOOGLE_CLIENT_ID    = your-id
GOOGLE_CLIENT_SECRET = your-secret
NEXT_PUBLIC_API_URL = https://api.myapp.com
```

**Via CLI:**
```bash
vercel env add DATABASE_URL
# Enter value: postgresql://...
# Select environments: Production, Preview
```

---

## 7️⃣ `next build` and `next start`

For self-hosted deployment (VPS, Docker):

```bash
# Build for production
npm run build     # = next build
# Creates .next/ folder with optimized assets

# Start production server
npm run start     # = next start -p 3000

# Check the build output
npm run build && npm run start
```

### Production Checklist

```bash
# 1. Build locally first — catch errors before deploying
npm run build

# 2. Check no TypeScript/ESLint errors
# 3. Make sure all env vars are set
# 4. Run: next start — test production build locally
npm run start
# Open http://localhost:3000
```

---

## 8️⃣ Common Deployment Issues & Fixes

### Issue 1: Environment variables undefined

```bash
# Problem: Variable not set in Vercel/Netlify dashboard
# Fix: Add variable in dashboard, redeploy
# Note: Client variables must use NEXT_PUBLIC_ prefix!
```

### Issue 2: Build fails — "Module not found"

```bash
# Problem: Import path case mismatch (works on Mac, fails on Linux)
# Wrong:   import Header from "./header"
# Fix:     import Header from "./Header"  (match exact filename case)
```

### Issue 3: Database connection fails in production

```bash
# Problem: DATABASE_URL not set or wrong format
# Fix: Check Vercel env vars, ensure connection string is correct
# Tip: Use connection pooling for serverless (PgBouncer, Neon, etc.)
```

### Issue 4: Prisma client error

```bash
# Problem: Prisma client not generated for production
# Fix: Add to package.json:
{
  "scripts": {
    "postinstall": "prisma generate"
  }
}
```

---

## 🎯 Interview Questions

**Q1: What is the difference between `NEXT_PUBLIC_*` and non-prefixed env vars?**

> `NEXT_PUBLIC_` variables are embedded in the JavaScript bundle at build time — visible to anyone inspecting the code. Non-prefixed variables are only available on the server (Server Components, Route Handlers, Server Actions) and never sent to the browser. Use `NEXT_PUBLIC_` only for values safe to expose publicly (API URLs, maps key, analytics ID).

**Q2: Why should you never commit `.env.local` to git?**

> It contains secrets like database passwords, API keys, and JWT secrets. Once committed, these secrets become part of the git history permanently (even if deleted later) and are visible to anyone with repository access. Use `.env.example` as a documented template with empty values instead.

**Q3: Why is Vercel the recommended hosting for Next.js?**

> Vercel created Next.js and optimizes every feature for it: Edge Functions for Middleware, Image Optimization CDN, Incremental Static Regeneration, automatic environment detection, zero-config deployment (auto-detects Next.js), and preview deployments for every pull request.

**Q4: What does `npm run build` do?**

> It runs `next build` which: compiles TypeScript, bundles client-side JS with code splitting and tree-shaking, pre-renders all static pages (SSG), generates optimized CSS, and creates the `.next/` folder ready for production. It also reports page sizes and performance warnings.

---

## 🏠 Home Task

Prepare a Next.js app for deployment:
1. Create `.env.local` with all required variables
2. Create `.env.example` with empty values for teammates
3. Add all variable names to `.gitignore`
4. Create `lib/config.js` with validation for required variables
5. Deploy to Vercel via GitHub integration
6. Set all environment variables in Vercel dashboard
7. Trigger a deploy and test the live URL
8. Create a feature branch and push — observe the Preview deployment URL
