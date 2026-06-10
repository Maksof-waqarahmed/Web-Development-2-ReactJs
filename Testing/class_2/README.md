# 🎭 End-to-End Testing with Playwright

## 📚 Topics Covered
- What is End-to-End (E2E) testing
- Playwright — cross-browser E2E framework
- Installation and project setup
- Writing test files — `test`, `expect`, `page`
- Navigating pages: `page.goto()`
- Finding elements: `page.locator()`, `getByRole()`, `getByText()`
- User actions: `fill()`, `click()`, `press()`
- Assertions: `toBeVisible()`, `toHaveText()`, `toHaveURL()`
- Screenshots and test reports
- Running in headless mode + CI integration

---

## 1️⃣ Unit Tests vs E2E Tests

```
Unit Tests (Vitest + RTL):          E2E Tests (Playwright):
- Test individual components         - Test complete user journeys
- Mock fetch / dependencies          - Real browser, real server
- Milliseconds to run                - Seconds to run
- Catch component-level bugs         - Catch integration bugs
- Run on every commit                - Run before release / nightly

Example unit test:                   Example E2E test:
"Does LoginForm show error           "Can a user sign in, add a product
 when email is invalid?"              to cart, and complete checkout?"
```

---

## 2️⃣ Setup

```bash
npm init playwright@latest
# Choose: TypeScript, tests/ folder, GitHub Actions workflow
```

This creates:
```
tests/
  example.spec.ts          ← sample tests
playwright.config.ts       ← configuration
.github/workflows/
  playwright.yml           ← CI workflow (optional)
```

```ts
// playwright.config.ts
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./tests",
  timeout: 30 * 1000,
  retries: process.env.CI ? 2 : 0,    // retry failed tests in CI

  use: {
    baseURL: "http://localhost:3000",   // your dev server URL
    screenshot: "only-on-failure",     // capture screenshots on failure
    video: "retain-on-failure",        // record video on failure
  },

  // Test in multiple browsers
  projects: [
    { name: "chromium", use: { ...devices["Desktop Chrome"] } },
    { name: "firefox",  use: { ...devices["Desktop Firefox"] } },
    { name: "Mobile Chrome", use: { ...devices["Pixel 5"] } },
  ],

  // Auto-start your dev server before tests
  webServer: {
    command: "npm run dev",
    url: "http://localhost:3000",
    reuseExistingServer: !process.env.CI,
  },
});
```

---

## 3️⃣ First E2E Test

```ts
// tests/home.spec.ts
import { test, expect } from "@playwright/test";

test("home page loads with correct title", async ({ page }) => {
  await page.goto("/");

  // Assert page title
  await expect(page).toHaveTitle(/My App/);

  // Assert a heading is visible
  await expect(page.getByRole("heading", { name: /welcome/i })).toBeVisible();
});

test("navigation links work", async ({ page }) => {
  await page.goto("/");

  // Click a navigation link
  await page.getByRole("link", { name: /products/i }).click();

  // Verify URL changed
  await expect(page).toHaveURL("/products");

  // Verify page content
  await expect(page.getByRole("heading", { name: /products/i })).toBeVisible();
});
```

---

## 4️⃣ Testing Forms and Authentication

```ts
// tests/auth.spec.ts
import { test, expect } from "@playwright/test";

test("user can log in with valid credentials", async ({ page }) => {
  await page.goto("/login");

  // Fill the form
  await page.getByPlaceholder("Email").fill("admin@test.com");
  await page.getByPlaceholder("Password").fill("123456");

  // Submit
  await page.getByRole("button", { name: /sign in/i }).click();

  // Verify redirect to dashboard
  await expect(page).toHaveURL("/dashboard");
  await expect(page.getByText("Welcome, Admin User!")).toBeVisible();
});

test("shows error for wrong credentials", async ({ page }) => {
  await page.goto("/login");

  await page.getByPlaceholder("Email").fill("wrong@test.com");
  await page.getByPlaceholder("Password").fill("wrongpassword");
  await page.getByRole("button", { name: /sign in/i }).click();

  // Error message should appear
  await expect(page.getByText(/invalid email or password/i)).toBeVisible();

  // Should stay on login page
  await expect(page).toHaveURL("/login");
});

test("redirect to login when accessing protected page", async ({ page }) => {
  await page.goto("/dashboard");

  // Should redirect to login
  await expect(page).toHaveURL("/login");
});
```

---

## 5️⃣ Testing CRUD Operations

```ts
// tests/posts.spec.ts
import { test, expect } from "@playwright/test";

// Login before each test in this file
test.beforeEach(async ({ page }) => {
  await page.goto("/login");
  await page.getByPlaceholder("Email").fill("admin@test.com");
  await page.getByPlaceholder("Password").fill("123456");
  await page.getByRole("button", { name: /sign in/i }).click();
  await page.waitForURL("/dashboard");
});

test("create a new post", async ({ page }) => {
  await page.goto("/posts/new");

  await page.getByLabel("Title").fill("My Test Post");
  await page.getByLabel("Content").fill("This is the content of my post.");
  await page.getByRole("button", { name: /create post/i }).click();

  // Verify redirect to posts list
  await expect(page).toHaveURL("/posts");

  // Verify new post appears in list
  await expect(page.getByText("My Test Post")).toBeVisible();
});

test("delete a post", async ({ page }) => {
  await page.goto("/posts");

  // Get the first post's delete button
  const firstPost = page.locator("[data-testid='post-item']").first();
  const postTitle = await firstPost.locator("h3").textContent();

  await firstPost.getByRole("button", { name: /delete/i }).click();

  // Confirm dialog
  await page.getByRole("button", { name: /confirm/i }).click();

  // Verify post is removed
  await expect(page.getByText(postTitle!)).not.toBeVisible();
});
```

---

## 6️⃣ Locators — Finding Elements

```ts
// Role-based (most reliable — mirrors accessibility)
page.getByRole("button", { name: /submit/i })
page.getByRole("link", { name: /home/i })
page.getByRole("heading", { level: 1 })
page.getByRole("textbox", { name: /email/i })

// Text-based
page.getByText("Click me")
page.getByText(/hello/i)            // regex — case insensitive

// Form labels
page.getByLabel("Email")            // matches <label>Email</label>
page.getByPlaceholder("Enter email")

// Test IDs (use sparingly)
page.getByTestId("submit-btn")      // data-testid="submit-btn"

// CSS selector fallback
page.locator(".navbar .logo")
page.locator("#main-content")
```

---

## 7️⃣ Running Tests

```bash
# Run all tests (headless)
npx playwright test

# Run with browser UI (watch tests run)
npx playwright test --headed

# Run specific test file
npx playwright test tests/auth.spec.ts

# Run in specific browser
npx playwright test --project=chromium

# Debug mode (pauses on each step)
npx playwright test --debug

# Generate HTML report
npx playwright show-report
```

---

## 8️⃣ Playwright in GitHub Actions CI

```yaml
# .github/workflows/playwright.yml
name: Playwright E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  e2e:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      - name: Run Playwright tests
        run: npx playwright test
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          AUTH_SECRET: ${{ secrets.AUTH_SECRET }}

      - name: Upload test report
        uses: actions/upload-artifact@v4
        if: always()              # upload even if tests fail
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 7
```

---

## 🎯 Interview Questions

**Q1: What is the difference between Unit tests and E2E tests?**

> Unit tests (Vitest + RTL) test individual components in isolation with mocked dependencies — fast but don't test the full system. E2E tests (Playwright) open a real browser, navigate real pages, and interact exactly like a user — slow but test the complete flow including the database, authentication, and all integrations working together.

**Q2: Why prefer `getByRole` over CSS selectors in Playwright?**

> `getByRole` mirrors how assistive technologies (screen readers) find elements — it's more resilient and encourages accessible code. CSS selectors like `.btn-primary` break when styling changes; `getByRole("button", { name: /submit/i })` keeps working as long as there's a submit button, regardless of styling.

**Q3: What is `test.beforeEach` and when do you use it?**

> `test.beforeEach` runs a function before each test in a `describe` block or file. It's used for setup that every test needs — like logging in, navigating to a starting page, or seeding test data. It avoids repeating the same setup code in every test.

**Q4: How do you handle a test that depends on a slow network request?**

> Playwright automatically waits for elements — `page.getByText("Loaded!")` waits up to 30 seconds (configurable) for the text to appear. You don't need manual `sleep`. For network interception, use `page.route()` to mock API responses, making tests deterministic and faster.

---

## 🏠 Home Task

Write E2E tests for your Next.js app:
1. Test home page loads with correct heading and navigation links
2. Test login flow — valid credentials → redirect to dashboard
3. Test login flow — invalid credentials → error message shown
4. Test protected route — accessing `/dashboard` without login → redirect to `/login`
5. Test creating a new item (post/task/product) via a form
6. Test deleting an item and verifying it disappears from the list
7. Add Playwright workflow to GitHub Actions
8. Run `npx playwright test --headed` and watch the browser perform all tests
