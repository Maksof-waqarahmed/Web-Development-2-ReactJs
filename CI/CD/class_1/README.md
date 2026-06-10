# ⚙️ CI/CD with GitHub Actions

## 📚 Topics Covered
- What is CI/CD and why it matters
- GitHub Actions — workflows, jobs, steps, runners
- Creating `.github/workflows/ci.yml`
- Running automated tests on every Pull Request
- ESLint + TypeScript check in CI
- Next.js build verification in CI
- Branch protection rules
- Using GitHub Secrets for environment variables

---

## 1️⃣ What is CI/CD?

```
CI — Continuous Integration:
  Every time code is pushed/PRed → automatically run tests, lint, build
  Goal: catch bugs before they reach main branch

CD — Continuous Deployment:
  Every time code merges to main → automatically deploy to production
  Goal: no manual deployment steps
```

```
Developer pushes code
       ↓
GitHub Actions triggers
       ↓
Install dependencies → Lint → Test → Build
       ↓ (if all pass)
Merge allowed / Deploy to Vercel
       ↓ (if any fail)
Block merge / Notify developer
```

---

## 2️⃣ GitHub Actions — Core Concepts

```yaml
# .github/workflows/ci.yml

name: CI                          # workflow name (shown in GitHub UI)

on:                               # WHEN to run
  push:
    branches: [main]              # on push to main
  pull_request:
    branches: [main]              # on PR targeting main

jobs:                             # what to run (can have multiple jobs)
  test:                           # job name
    runs-on: ubuntu-latest        # runner (virtual machine)

    steps:                        # sequential steps in this job
      - name: Checkout code
        uses: actions/checkout@v4  # built-in action

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"

      - name: Install dependencies
        run: npm ci                # faster than npm install in CI

      - name: Run tests
        run: npm test
```

---

## 3️⃣ Complete CI Workflow for Next.js

```yaml
# .github/workflows/ci.yml
name: CI — Test, Lint & Build

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js 20
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"              # cache node_modules between runs

      - name: Install dependencies
        run: npm ci

      - name: Run ESLint
        run: npm run lint

      - name: Run Tests
        run: npm test
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}   # from GitHub Secrets

      - name: Build Next.js
        run: npm run build
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          AUTH_SECRET: ${{ secrets.AUTH_SECRET }}
          NEXT_PUBLIC_API_URL: ${{ secrets.NEXT_PUBLIC_API_URL }}
```

---

## 4️⃣ Setting Up Vitest for Next.js

```bash
npm install -D vitest @vitejs/plugin-react jsdom @testing-library/react @testing-library/jest-dom
```

```js
// vitest.config.ts
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: "./vitest.setup.ts",
  },
});
```

```ts
// vitest.setup.ts
import "@testing-library/jest-dom";
```

```json
// package.json — add test scripts
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

---

## 5️⃣ Adding GitHub Secrets

Secrets are environment variables stored securely in GitHub — NOT visible in logs or to forks.

**How to add:**
1. Go to your repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **"New repository secret"**
3. Add each variable (name + value)

```
DATABASE_URL        = postgresql://...
AUTH_SECRET         = your-secret
NEXT_PUBLIC_API_URL = https://api.myapp.com
```

**Using in workflow:**
```yaml
env:
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
  AUTH_SECRET: ${{ secrets.AUTH_SECRET }}
```

---

## 6️⃣ Caching Dependencies (Speed Optimization)

Without cache: `npm ci` downloads all packages every run (~30s–2min)
With cache: skips download if `package-lock.json` hasn't changed (~5s)

```yaml
- name: Setup Node.js with cache
  uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: "npm"               # caches ~/.npm directory
```

---

## 7️⃣ Branch Protection Rules

Prevent merging unless CI passes:

1. Go to repo → **Settings** → **Branches**
2. Click **"Add rule"** for `main`
3. Enable:
   - ✅ **Require status checks to pass before merging**
   - Select your CI workflow (`CI — Test, Lint & Build`)
   - ✅ **Require branches to be up to date before merging**
   - ✅ **Require pull request reviews before merging**

Now no one can push broken code directly to `main`.

---

## 8️⃣ Viewing CI Results

In GitHub, on every PR you'll see:

```
✅ All checks passed
  ✅ CI — Test, Lint & Build (2m 14s)
     ✅ Run ESLint
     ✅ Run Tests
     ✅ Build Next.js

Merge pull request ← (button only appears after CI passes)
```

If a check fails:
```
❌ Some checks were not successful
  ❌ CI — Test, Lint & Build
     ✅ Run ESLint
     ❌ Run Tests — 2 tests failed
     ⏭️ Build Next.js — skipped
```

---

## 🎯 Interview Questions

**Q1: What is the difference between CI and CD?**

> CI (Continuous Integration) automatically runs tests, linting, and builds on every code change — catching bugs before they merge. CD (Continuous Deployment) automatically deploys the application when code merges to the main branch — eliminating manual deployment steps. CI is a prerequisite for CD.

**Q2: Why use `npm ci` instead of `npm install` in CI?**

> `npm ci` is designed for CI environments: it installs exact versions from `package-lock.json` (no version resolution), fails if `package-lock.json` is out of sync with `package.json`, and is faster because it skips some checks. `npm install` updates `package-lock.json` which could cause unexpected version changes in CI.

**Q3: How do you securely use API keys in GitHub Actions?**

> Store them in **GitHub Secrets** (repo Settings → Secrets → Actions). Secrets are encrypted, never shown in logs, and not accessible to forked PRs. Reference them in workflows as `${{ secrets.MY_SECRET }}`. Never hardcode secrets in `.yml` files or commit `.env` files.

**Q4: What are Branch Protection Rules?**

> They prevent direct pushes to protected branches (like `main`) and enforce requirements before merging: all CI checks must pass, required number of PR reviews, branches must be up-to-date. This ensures broken code never reaches production.

---

## 🏠 Home Task

Set up CI for your Next.js project:
1. Create `.github/workflows/ci.yml` with lint, test, and build steps
2. Add Vitest to your project and write at least 2 component tests
3. Push to a new branch and open a Pull Request — verify CI runs automatically
4. Add all required secrets to GitHub repository settings
5. Enable Branch Protection Rules on `main` — require CI to pass before merge
6. Break a test intentionally — observe the PR blocked by failed CI
7. Fix the test — observe CI pass and PR unblocked
