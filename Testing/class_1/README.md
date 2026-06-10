# 🧪 Unit Testing with Vitest + React Testing Library

## 📚 Topics Covered
- Why test React components
- Vitest — fast unit test runner for Vite/Next.js
- React Testing Library (RTL) — test from the user's perspective
- `render`, `screen`, `userEvent` basics
- Common queries: `getByText`, `getByRole`, `getByPlaceholderText`
- Async testing with `waitFor` and `findBy`
- Testing form inputs and button clicks
- Mocking modules and `fetch`
- Testing custom hooks with `renderHook`

---

## 1️⃣ Why Test? — The Testing Pyramid

```
        /\
       /  \
      / E2E \        ← Few, slow, expensive (Playwright — week_12/class_2)
     /--------\
    / Integration \  ← Medium (test components + API together)
   /--------------\
  /   Unit Tests   \  ← Many, fast, cheap (Vitest + RTL — this class)
 /------------------\
```

**Unit tests check:** does this component render correctly? Does clicking the button do what it should?

---

## 2️⃣ Setup — Vitest + React Testing Library

```bash
npm install -D vitest @vitejs/plugin-react jsdom
npm install -D @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

```ts
// vitest.config.ts (at project root)
import { defineConfig } from "vitest/config";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",       // simulate browser DOM
    globals: true,              // no need to import describe/it/expect
    setupFiles: ["./vitest.setup.ts"],
  },
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./"),
    },
  },
});
```

```ts
// vitest.setup.ts
import "@testing-library/jest-dom";  // adds matchers: toBeInTheDocument, toHaveValue, etc.
```

```json
// package.json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```

---

## 3️⃣ First Test — Basic Component

```jsx
// components/Greeting.jsx
export default function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}
```

```ts
// components/Greeting.test.tsx
import { render, screen } from "@testing-library/react";
import Greeting from "./Greeting";

describe("Greeting", () => {
  it("renders the name", () => {
    render(<Greeting name="Waqar" />);
    expect(screen.getByText("Hello, Waqar!")).toBeInTheDocument();
  });

  it("renders different names", () => {
    render(<Greeting name="Ahmed" />);
    expect(screen.getByRole("heading", { name: /ahmed/i })).toBeInTheDocument();
  });
});
```

---

## 4️⃣ `screen` Queries — Finding Elements

```ts
// getBy* → throws if not found (use when element MUST exist)
screen.getByText("Submit")
screen.getByRole("button", { name: /submit/i })
screen.getByPlaceholderText("Enter email")
screen.getByLabelText("Email")
screen.getByTestId("submit-btn")      // data-testid="submit-btn"

// queryBy* → returns null if not found (use when element may not exist)
screen.queryByText("Error message")   // returns null if not found
expect(screen.queryByText("Error")).not.toBeInTheDocument()

// findBy* → async, waits for element to appear (use for async content)
const el = await screen.findByText("Loaded!")
```

---

## 5️⃣ Testing User Interactions

```jsx
// components/Counter.jsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

```ts
// components/Counter.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Counter from "./Counter";

describe("Counter", () => {
  it("starts at 0", () => {
    render(<Counter />);
    expect(screen.getByText("Count: 0")).toBeInTheDocument();
  });

  it("increments when button clicked", async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.click(screen.getByRole("button", { name: /increment/i }));
    expect(screen.getByText("Count: 1")).toBeInTheDocument();

    await user.click(screen.getByRole("button", { name: /increment/i }));
    expect(screen.getByText("Count: 2")).toBeInTheDocument();
  });

  it("resets to 0", async () => {
    const user = userEvent.setup();
    render(<Counter />);

    await user.click(screen.getByRole("button", { name: /increment/i }));
    await user.click(screen.getByRole("button", { name: /reset/i }));
    expect(screen.getByText("Count: 0")).toBeInTheDocument();
  });
});
```

---

## 6️⃣ Testing Forms

```jsx
// components/LoginForm.jsx
import { useState } from "react";

export default function LoginForm({ onSubmit }) {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!email.includes("@")) {
      setError("Invalid email");
      return;
    }
    onSubmit({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      {error && <p role="alert">{error}</p>}
      <input
        type="email"
        placeholder="Email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <input
        type="password"
        placeholder="Password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

```ts
// components/LoginForm.test.tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { vi } from "vitest";
import LoginForm from "./LoginForm";

describe("LoginForm", () => {
  it("shows error for invalid email", async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={vi.fn()} />);

    await user.type(screen.getByPlaceholderText("Email"), "notvalid");
    await user.type(screen.getByPlaceholderText("Password"), "123456");
    await user.click(screen.getByRole("button", { name: /login/i }));

    expect(screen.getByRole("alert")).toHaveTextContent("Invalid email");
  });

  it("calls onSubmit with email and password", async () => {
    const user = userEvent.setup();
    const mockSubmit = vi.fn();
    render(<LoginForm onSubmit={mockSubmit} />);

    await user.type(screen.getByPlaceholderText("Email"), "test@example.com");
    await user.type(screen.getByPlaceholderText("Password"), "secret123");
    await user.click(screen.getByRole("button", { name: /login/i }));

    expect(mockSubmit).toHaveBeenCalledWith({
      email: "test@example.com",
      password: "secret123",
    });
  });
});
```

---

## 7️⃣ Testing Async Components

```jsx
// components/UserProfile.jsx
import { useState, useEffect } from "react";

export default function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
      .then((r) => r.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  return <div><h2>{user.name}</h2><p>{user.email}</p></div>;
}
```

```ts
// components/UserProfile.test.tsx
import { render, screen } from "@testing-library/react";
import { vi } from "vitest";
import UserProfile from "./UserProfile";

// Mock global fetch
vi.stubGlobal("fetch", vi.fn());

describe("UserProfile", () => {
  it("shows loading initially then renders user", async () => {
    (fetch as any).mockResolvedValueOnce({
      json: async () => ({ id: 1, name: "John Doe", email: "john@example.com" }),
    });

    render(<UserProfile userId={1} />);

    // Loading state
    expect(screen.getByText("Loading...")).toBeInTheDocument();

    // Wait for data to load
    const name = await screen.findByText("John Doe");
    expect(name).toBeInTheDocument();
    expect(screen.getByText("john@example.com")).toBeInTheDocument();
  });
});
```

---

## 8️⃣ Mocking Modules

```ts
// Mock Next.js router
vi.mock("next/navigation", () => ({
  useRouter: () => ({
    push: vi.fn(),
    replace: vi.fn(),
    back: vi.fn(),
  }),
  usePathname: () => "/dashboard",
}));

// Mock a module
vi.mock("@/lib/db", () => ({
  default: {
    user: {
      findMany: vi.fn().mockResolvedValue([
        { id: 1, name: "Alice" },
        { id: 2, name: "Bob" },
      ]),
    },
  },
}));
```

---

## 🎯 Interview Questions

**Q1: What is React Testing Library's philosophy?**

> RTL encourages testing from the **user's perspective** — not implementation details. Instead of testing that `setState` was called, you test what the user sees and interacts with: "does clicking the button show a success message?" This makes tests more resilient to refactoring since they don't break when internals change.

**Q2: What is the difference between `getBy`, `queryBy`, and `findBy`?**

> `getBy` throws immediately if the element isn't found — use when the element must exist. `queryBy` returns `null` if not found — use to assert something is NOT present (`not.toBeInTheDocument()`). `findBy` is async and polls until the element appears — use for elements that appear after a fetch or state update.

**Q3: Why use `userEvent` instead of `fireEvent`?**

> `userEvent` simulates real browser interactions more accurately — typing triggers focus, keydown, input, keyup events in sequence, just like a real user. `fireEvent.click()` only fires the click event. For most tests `userEvent` produces more reliable results.

**Q4: What does `vi.fn()` do?**

> `vi.fn()` creates a mock function that records all calls. You can assert how many times it was called (`toHaveBeenCalledTimes`), with what arguments (`toHaveBeenCalledWith`), and control its return value (`mockResolvedValueOnce`). It's used to test callbacks and replace real implementations in tests.

---

## 🏠 Home Task

Write unit tests for a mini todo app:
1. `TodoList` component — renders list of todo items
2. `AddTodo` form — typing and submitting adds a new item
3. `TodoItem` — clicking "Delete" button removes the item
4. `TodoItem` — clicking checkbox marks it complete (toggle)
5. Mock `fetch` and test a component that loads todos from an API
6. Use `vi.fn()` to verify a callback prop is called with the right arguments
7. Run `npm test -- --coverage` and aim for 80%+ coverage
