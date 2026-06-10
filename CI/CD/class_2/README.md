# 🚀 Continuous Deployment — Automated Releases

## 📚 Topics Covered
- CD pipeline — deploy on merge to main
- Vercel CLI in GitHub Actions
- Environment-based deployments (staging vs production)
- GitHub Environments with protection rules
- Deployment status notifications
- Rollback strategies
- Docker basics for Next.js
- Self-hosted deployment with PM2

---

## 1️⃣ CD Pipeline — Full Workflow

```
Developer opens PR
       ↓
CI runs: lint + test + build (from class_1)
       ↓ (CI passes)
PR reviewed and merged to main
       ↓
CD pipeline triggers automatically
       ↓
Deploy to Vercel/Production
       ↓
Notify: "Deployed to https://myapp.vercel.app ✅"
```

---

## 2️⃣ Vercel Auto-Deploy (Zero-Config)

The simplest CD: connect GitHub repo to Vercel.

```
Every push to main  →  Vercel auto-deploys to production
Every push to any branch  →  Vercel creates a Preview URL
```

**Setup:**
1. Push repo to GitHub
2. vercel.com → New Project → Import repo
3. Done — every merge to `main` deploys automatically

**Preview URLs (automatic for every branch/PR):**
```
PR #42: https://myapp-git-feature-login-username.vercel.app
main:   https://myapp.vercel.app
```

---

## 3️⃣ Vercel CLI in GitHub Actions

For more control, deploy via GitHub Actions:

```bash
npm install -g vercel
vercel login
vercel link           # link to existing project
```

```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]   # only deploy when merging to main

jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: [ci]         # wait for CI job to pass first

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Deploy to Vercel
        run: npx vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

**Getting Vercel secrets:**
```bash
vercel login
vercel link            # creates .vercel/project.json with org+project IDs
cat .vercel/project.json

# VERCEL_TOKEN: generate at vercel.com → Settings → Tokens
```

---

## 4️⃣ Combined CI + CD Workflow

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  # Job 1: Run on every PR
  ci:
    name: Test & Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"
      - run: npm ci
      - run: npm run lint
      - run: npm test
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
      - run: npm run build
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          AUTH_SECRET: ${{ secrets.AUTH_SECRET }}

  # Job 2: Deploy only when merged to main
  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: ci                        # only runs after CI passes
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"
      - run: npm ci
      - name: Deploy to Vercel
        run: npx vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

---

## 5️⃣ GitHub Environments (Staging vs Production)

Environments add approval gates before deployment:

```yaml
jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    environment: staging              # uses staging secrets
    steps:
      - run: npx vercel --token=${{ secrets.VERCEL_TOKEN }}

  deploy-production:
    runs-on: ubuntu-latest
    environment: production           # requires manual approval
    needs: deploy-staging
    steps:
      - run: npx vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
```

**Setup in GitHub:**
1. Repo → **Settings** → **Environments**
2. Create `staging` and `production` environments
3. On `production`: enable **Required reviewers** (forces manual approval before prod deploy)

---

## 6️⃣ Docker for Next.js (Self-hosted Deployment)

For deploying to a VPS (DigitalOcean, AWS EC2) instead of Vercel:

```dockerfile
# Dockerfile
FROM node:20-alpine AS base

# Install dependencies
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# Build the app
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production

COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public

EXPOSE 3000
CMD ["node", "server.js"]
```

```yaml
# next.config.mjs — enable standalone output for Docker
const nextConfig = {
  output: "standalone",
};
export default nextConfig;
```

```bash
# Build and run Docker container
docker build -t my-nextjs-app .
docker run -p 3000:3000 -e DATABASE_URL="..." my-nextjs-app
```

---

## 7️⃣ Self-Hosted with PM2

For running Next.js on a VPS without Docker:

```bash
# On your VPS
npm install -g pm2

# Build and start
npm run build
pm2 start npm --name "my-app" -- start

# Auto-restart on server reboot
pm2 startup
pm2 save

# View logs
pm2 logs my-app

# Restart after new deploy
pm2 restart my-app
```

**Deployment script:**
```bash
#!/bin/bash
# deploy.sh
git pull origin main
npm ci
npm run build
pm2 restart my-app
echo "Deployed successfully!"
```

---

## 8️⃣ Deployment Notifications

```yaml
# Notify on Slack/Discord after deploy
- name: Notify Slack on success
  if: success()
  uses: slackapi/slack-github-action@v1
  with:
    payload: |
      {
        "text": "✅ Deployed *${{ github.repository }}* to production\nCommit: ${{ github.sha }}\nBy: ${{ github.actor }}"
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

- name: Notify on failure
  if: failure()
  uses: slackapi/slack-github-action@v1
  with:
    payload: |
      {
        "text": "❌ Deployment FAILED for *${{ github.repository }}*\nBranch: ${{ github.ref_name }}"
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

---

## 🎯 Interview Questions

**Q1: What is the difference between CI and CD?**

> CI (Continuous Integration) automatically runs tests and builds on every code change to verify correctness. CD (Continuous Deployment/Delivery) automatically deploys verified code to production. CI answers "is the code correct?", CD answers "how do we release it?".

**Q2: What are GitHub Environments and why use them?**

> GitHub Environments allow different configuration (secrets, approval gates) per deployment target. A `production` environment can require manual approval from a senior developer before deploy — preventing accidental production releases. A `staging` environment deploys automatically for testing.

**Q3: What does `needs: ci` in a GitHub Actions job do?**

> It creates a dependency between jobs — the `deploy` job only starts after the `ci` job completes successfully. If CI fails (tests break, build fails), the deploy job is automatically skipped. This prevents deploying broken code.

**Q4: What is `output: "standalone"` in Next.js?**

> It tells Next.js to create a self-contained `server.js` file in `.next/standalone/` that includes only the necessary Node.js code — no `node_modules` folder needed to run it. This produces a much smaller Docker image, ideal for containerized deployments.

---

## 🏠 Home Task

Build a complete CI/CD pipeline:
1. Create a combined `ci-cd.yml` workflow: CI job on PRs, CD job on merge to main
2. Add two GitHub Environments: `staging` and `production` with different secrets
3. Require manual approval for the `production` environment
4. Create a Dockerfile for your Next.js app with `output: "standalone"`
5. Build the Docker image locally and verify it runs at `localhost:3000`
6. Add a deploy notification step (can mock with a simple echo message)
7. Observe the full pipeline: PR → CI → merge → staging deploy → approval → production deploy
