# ⚛️ React Hooks Deep Dive

## `useRef`, Custom Hooks, Best Practices, and Debouncing Example

---

## 🔹 1. `useRef` Hook — DOM Access & Persistent Values

### 🧠 What is `useRef`?

`useRef` is a that gives you a **mutable reference** to store values that **persist between re-renders** — **without causing a re-render**.

It can be used for:

* Accessing and interacting with **DOM elements**
* Storing **previous values**
* Maintaining **mutable variables** that don’t trigger re-rendering

---

### 🧩 Syntax

```jsx
const refName = useRef(initialValue);
```

* `refName.current` → stores the current value.
* The value **does not reset** across re-renders.

---

### 📍 Example 1: Accessing DOM Elements

```jsx
import { useRef } from "react";

function FocusInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus(); // direct DOM access
  };

  return (
    <div>
      <input ref={inputRef} placeholder="Type something..." />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

🧠 **Explanation:**

* `useRef(null)` creates a reference object.
* `ref={inputRef}` attaches it to the `<input>`.
* Using `inputRef.current.focus()` directly accesses the DOM node.

---

### 📍 Example 2: Persistent Values (Without Re-rendering)

```jsx
import { useEffect, useRef, useState } from "react";

function RenderCounter() {
  const [count, setCount] = useState(0);
  const renderCount = useRef(0);

  useEffect(() => {
    renderCount.current += 1;
  });

  return (
    <div>
      <h3>Count: {count}</h3>
      <h4>Renders: {renderCount.current}</h4>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
}
```

🧩 **Explanation:**

* `renderCount.current` stores a mutable value.
* It updates without triggering a re-render.
* Each render increases the count persistently.

---

### 📍 Example 3: Storing Previous State

```jsx
import { useEffect, useRef, useState } from "react";

function PreviousValue() {
  const [count, setCount] = useState(0);
  const prevCount = useRef();

  useEffect(() => {
    prevCount.current = count;
  }, [count]);

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCount.current}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

✅ This allows you to **track previous values** across renders.

---

## 🔹 2. Custom Hooks — Creating Reusable Logic

### 🧠 What are Custom Hooks?

Custom Hooks are **functions that start with `use`** and allow you to **extract reusable logic** from components.

> Think of them as “mini hooks” built using existing React Hooks (`useState`, `useEffect`, `useRef`, etc.).

---

### 🧩 Benefits of Custom Hooks

| Advantage           | Description                              |
| ------------------- | ---------------------------------------- |
| ♻️ Reusability      | Share logic between multiple components  |
| 🧹 Clean Code       | Avoid duplication and clutter            |
| 🔄 Easy Maintenance | Update logic once, apply everywhere      |
| ⚡ Testability       | Easier to test business logic separately |

---

### 📦 Example: Custom Hook for Window Width

```jsx
import { useState, useEffect } from "react";

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", handleResize);

    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return width;
}

export default useWindowWidth;
```

**Usage:**

```jsx
import useWindowWidth from "./useWindowWidth";

function App() {
  const width = useWindowWidth();
  return <h3>Window Width: {width}px</h3>;
}
```

🧠 **Logic:**
This custom hook encapsulates the resize event listener logic and returns current width — reusable in multiple components.

---

### 📦 Example: Custom Hook for Local Storage

```jsx
import { useState } from "react";

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const saved = localStorage.getItem(key);
    return saved ? JSON.parse(saved) : initialValue;
  });

  const setStoredValue = (newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, setStoredValue];
}

export default useLocalStorage;
```

**Usage:**

```jsx
const [theme, setTheme] = useLocalStorage("theme", "light");
```

✅ Reusable across multiple apps.

---

## 🔹 3. Hook Best Practices & Common Patterns

### 🧭 Rules of Hooks (Reminder)

1. ✅ Only call hooks at the **top level** (not inside loops, conditions, or nested functions).
2. ✅ Only call hooks from:

   * React function components
   * Custom Hooks
3. ⚡ Hook names **must start with `use`** (like `useFetch`, `useTheme`).

---

### 💡 Best Practices

| Practice                   | Example                                                                |
| -------------------------- | ---------------------------------------------------------------------- |
| 📍 Keep hooks focused      | `useForm`, `useAuth`, `useFetch` — one responsibility per hook         |
| 🔄 Return minimal API      | Return only what’s needed (state + function)                           |
| 🧩 Reuse instead of repeat | Move duplicate `useEffect` or `useState` logic into a custom hook      |
| 💬 Use meaningful names    | `useDebounce`, `useToggle`, `useFetchData` — clear intent              |
| ⚠️ Always clean up         | Use return functions inside `useEffect` for listeners, intervals, etc. |

---

### 🧱 Common Patterns

#### 🔸 Pattern 1: useToggle Hook

```jsx
function useToggle(initial = false) {
  const [state, setState] = useState(initial);
  const toggle = () => setState((prev) => !prev);
  return [state, toggle];
}
```

Usage:

```jsx
const [isOpen, toggleOpen] = useToggle();
```

---

#### 🔸 Pattern 2: useDebounce Hook

Used to delay a function call (like search API) until the user stops typing.

```jsx
import { useEffect, useState } from "react";

function useDebounce(value, delay = 500) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debounced;
}

export default useDebounce;
```

---

## 🔹 4. Hands-On: Search Filter Component with Debouncing (Custom Hook)

Let’s build a **real-world component** that uses custom hooks.

---

### ⚙️ Step 1: Create `useDebounce.js`

```jsx
import { useState, useEffect } from "react";

function useDebounce(value, delay = 500) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebounced(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debounced;
}

export default useDebounce;
```

---

### ⚙️ Step 2: Create `SearchFilter.jsx`

```jsx
import React, { useEffect, useState } from "react";
import useDebounce from "./useDebounce";

const items = [
  "React",
  "Next.js",
  "Node.js",
  "MongoDB",
  "TypeScript",
  "Prisma",
  "Express",
];

function SearchFilter() {
  const [search, setSearch] = useState("");
  const [filtered, setFiltered] = useState(items);
  const debouncedSearch = useDebounce(search, 400);

  useEffect(() => {
    if (!debouncedSearch) {
      setFiltered(items);
    } else {
      const result = items.filter((item) =>
        item.toLowerCase().includes(debouncedSearch.toLowerCase())
      );
      setFiltered(result);
    }
  }, [debouncedSearch]);

  return (
    <div style={{ textAlign: "center", marginTop: "30px" }}>
      <h2>🔍 Debounced Search Filter</h2>
      <input
        type="text"
        placeholder="Search technology..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        style={{ padding: "8px", width: "250px" }}
      />
      <ul style={{ listStyle: "none", marginTop: "20px" }}>
        {filtered.map((item, i) => (
          <li key={i}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

export default SearchFilter;
```

---

### 🧠 How It Works

1. User types → `search` updates instantly.
2. `useDebounce` waits 400ms before updating `debouncedSearch`.
3. If the user keeps typing → timer resets.
4. When typing stops → the search filter executes only once.
   ✅ Prevents unnecessary re-renders and API calls.

---

### ⚡ Advantages

* Better **performance** (fewer renders)
* **Reusable hook** for any input-based API
* Cleaner code separation (`useDebounce` handles delay logic)

---

## 🧾 Final Summary

| Concept           | Purpose                                | Example Use                                  |
| ----------------- | -------------------------------------- | -------------------------------------------- |
| **`useRef`**      | Access DOM, store mutable values       | Focus input, store previous value            |
| **Custom Hook**   | Reusable logic for multiple components | `useFetch`, `useDebounce`, `useLocalStorage` |
| **Hook Patterns** | Standard solutions to common problems  | `useToggle`, `useForm`, `useAuth`            |
| **Debouncing**    | Optimize input events                  | Search filter, API queries                   |

---

### 🏁 Key Takeaways

* `useRef` = For **DOM access** or **mutable persistent data**.
* Custom Hooks = For **logic reusability** and **clean code**.
* Follow **Rules of Hooks** strictly.
* Use **debouncing** to make input handling efficient.
* Keep Hooks **simple, reusable, and testable**.

---