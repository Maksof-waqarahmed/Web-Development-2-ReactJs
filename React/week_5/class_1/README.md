# ⚛️ Rules of Hooks, `useEffect` & Data Fetching

## 📚 Topics Covered
- The 2 Rules of Hooks (top-level, React functions only)
- Why these rules exist — React's call order dependency
- `useEffect` hook — mount, update, unmount lifecycle
- `useEffect` dependencies array
- Cleanup function in `useEffect`
- `fetch` API vs `axios` — full comparison
- `async/await` inside `useEffect`
- `AbortController` — request cancellation
- Project: Movie Search App

---

## 📘 **Introduction**

React Hooks are special functions that let you **use state and other React features** (like lifecycle methods) in **functional components**.

Example:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Clicked {count} times</button>;
}
```

Hooks make functional components powerful — but to make sure they work correctly, **React enforces two important rules** called **“The Rules of Hooks.”**

---

## ⚖️ **The 2 Rules of Hooks**

| 🧩 Rule                                      | 🧠 Explanation                                                                                |
| -------------------------------------------- | --------------------------------------------------------------------------------------------- |
| **1️⃣ Only Call Hooks at the Top Level**     | Never call Hooks inside loops, conditions, or nested functions.                               |
| **2️⃣ Only Call Hooks from React Functions** | Call Hooks only from React components or custom Hooks, not from regular JavaScript functions. |

---

# 🧩 **Rule 1: Only Call Hooks at the Top Level**

---

### 📜 **Meaning**

You must call Hooks **at the top level of your React function**, before any `return`, `if`, `for`, or nested function.

❌ **Invalid Example**

```jsx
function Counter() {
  if (true) {
    // ❌ Hook inside a condition (not allowed)
    const [count, setCount] = useState(0);
  }
}
```

✅ **Valid Example**

```jsx
function Counter() {
  const [count, setCount] = useState(0); // ✅ Top level
  if (count > 5) {
    console.log("Count is greater than 5");
  }
  return <h2>{count}</h2>;
}
```

---

### 🧠 **Why This Rule Exists**

React uses an **internal Hook call order** to track state.
If you call Hooks inside conditions or loops, the **order of Hooks changes**, and React **loses track** of which state belongs to which Hook.

React assumes Hooks are called **in the same order on every render**.

🧩 Example:

```jsx
function Example({ flag }) {
  // Hook 1
  const [name, setName] = useState("Ali");

  // ❌ Hook inside condition
  if (flag) {
    const [age, setAge] = useState(22);
  }

  // Hook 2
  const [city, setCity] = useState("Lahore");
}
```

If `flag` changes between renders, Hook order breaks → ❌ Error like:

> “Rendered more hooks than during the previous render.”

---

### 💬 **Summary**

| ✅ Allowed                         | ❌ Not Allowed                       |
| --------------------------------- | ----------------------------------- |
| Call Hooks at top of the function | Inside `if`, `for`, or any block    |
| Same order every render           | Changing order based on condition   |
| Inside React component            | Inside plain JS or helper functions |

---

# ⚛️ **Rule 2: Only Call Hooks from React Functions**

---

### 📜 **Meaning**

You can only call Hooks from:

1. **React functional components**
2. **Custom Hooks (functions starting with “use”)**

You **cannot call Hooks** from:

* Regular JavaScript functions
* Class components
* Event handlers (directly)
* Loops or conditionals

---

### ✅ **Correct Example**

```jsx
function Profile() {
  const [name, setName] = useState("Rana");
  return <h2>{name}</h2>;
}
```

✅ **Custom Hook Example**

```jsx
function useUserData() {
  const [user, setUser] = useState("Rana");
  return user;
}

function Dashboard() {
  const user = useUserData();
  return <h3>Welcome, {user}</h3>;
}
```

---

### ❌ **Incorrect Example**

```jsx
// ❌ Not a React component or custom hook
function fetchUser() {
  const [user, setUser] = useState("Rana"); // ❌ Error
}
```

📛 React will show an error:

> “Invalid Hook Call. Hooks can only be called inside the body of a function component.”

---

### 🧠 **Why This Rule Exists**

React must **know where your Hooks live** in the component tree.

If you call Hooks in random functions, React can’t associate them with any component’s state or lifecycle — causing unexpected behavior.

---

# 🔧 **Custom Hooks**

---

### 💡 What Are Custom Hooks?

Custom Hooks are **your own reusable functions** that follow the **Rules of Hooks** and **start with “use”**.

✅ Example:

```jsx
function useCounter() {
  const [count, setCount] = useState(0);
  const increment = () => setCount((c) => c + 1);
  return { count, increment };
}

function App() {
  const { count, increment } = useCounter();
  return <button onClick={increment}>Count: {count}</button>;
}
```

🧠 React identifies any function starting with `use` as a Hook and automatically applies the **Rules of Hooks** to it.

---

# 🔬 **Why the Rules Are Important**

| 🧩 Reason              | 🔍 Description                                        |
| ---------------------- | ----------------------------------------------------- |
| 🧠 **Consistency**     | Ensures Hooks are called in the same order each time. |
| 🪄 **Predictability**  | Keeps state and effect management predictable.        |
| ⚙️ **React Internals** | React uses Hook call order to map states and effects. |
| 🚫 **Avoid Bugs**      | Prevents invalid state mismatches and crashes.        |

---

# 🚫 **Common Mistakes and Fixes**

| ❌ Wrong                                      | ✅ Correct                                    |
| -------------------------------------------- | -------------------------------------------- |
| Calling Hook inside condition                | Call at top level always                     |
| Calling Hook in non-React function           | Move logic inside a component or custom hook |
| Forgetting to start a custom hook with `use` | Always name like `useFetch`, `useCounter`    |
| Using Hooks in class components              | Hooks work only in functional components     |

---

# 🧠 **Example of Violating vs Following Rules**

### ❌ Violating the Rules

```jsx
function App() {
  const [isVisible, setIsVisible] = useState(true);

  if (isVisible) {
    // ❌ Not allowed
    const [count, setCount] = useState(0);
  }

  return <button onClick={() => setIsVisible(!isVisible)}>Toggle</button>;
}
```

### ✅ Correct Version

```jsx
function App() {
  const [isVisible, setIsVisible] = useState(true);
  const [count, setCount] = useState(0); // ✅ Top-level call

  return (
    <div>
      {isVisible && <p>Count: {count}</p>}
      <button onClick={() => setIsVisible(!isVisible)}>Toggle</button>
    </div>
  );
}
```

---

# 🧭 **How React Enforces These Rules**

React uses a special ESLint plugin to catch invalid Hook usage:

> `eslint-plugin-react-hooks`

📦 **Install:**

```bash
npm install eslint-plugin-react-hooks --save-dev
```

📄 **Add to `.eslintrc`**

```json
{
  "plugins": ["react-hooks"],
  "rules": {
    "react-hooks/rules-of-hooks": "error", 
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

This plugin **automatically checks**:

* You follow the two main Rules of Hooks
* Dependencies of `useEffect` are correctly declared

---

# 🧩 **Practical Tips**

✅ Always:

* Call Hooks **at top level**
* Call Hooks **inside React components or custom Hooks**
* Name your custom hooks starting with **“use”**
* Keep Hooks order **consistent**
* Use ESLint plugin for React Hooks

---

# 🏁 **Summary**

| 🧩 Concept          | 📖 Description                                          |
| ------------------- | ------------------------------------------------------- |
| **Rule 1**          | Call Hooks only at top level (no loops/conditions)      |
| **Rule 2**          | Call Hooks only inside React components or custom Hooks |
| **Why Important**   | Keeps React’s internal state mapping stable             |
| **Custom Hooks**    | Your own functions using other hooks (start with `use`) |
| **Linting Support** | Use `eslint-plugin-react-hooks` to detect violations    |

---

# ⚔️ `fetch` vs `axios` in React + `useEffect`

---

## 🧠 Introduction

When building React applications, you often need to **fetch data from APIs**, send data to servers, or handle asynchronous tasks.
React itself doesn’t provide a built-in API client — so we use tools like **`fetch`** or **`axios`** to perform these operations.

---

# 🔹 `fetch`

---

### 💡 What is `fetch`?

`fetch()` is a **built-in JavaScript function** that allows you to make HTTP requests to a server.
It’s part of the **browser’s native API** — no installation is needed.

It returns a **Promise**, which resolves to the response of the request.

---

### ✅ Basic Example

```js
  fetch("https://opentdb.com/api.php?amount=5&type=multiple")
    .then((res) => res.json()) // convert response to JSON
    .then((data) => console.log(data))
    .catch((err) => console.error("Error:", err));
```

---

### 🧩 Step-by-Step Explanation

1. **`fetch(url)`** → starts an HTTP request.
2. It returns a **Promise** that resolves when the response is received.
3. You must manually call `.json()` to parse the response body.
4. `.catch()` handles any **network errors**.

---

### ✅ Pros of `fetch`

| Advantage            | Description                                                    |
| -------------------- | -------------------------------------------------------------- |
| 🧠 **Built-in**      | No need to install any library — works in all modern browsers. |
| ⚡ **Lightweight**    | Minimal code footprint.                                        |
| 💪 **Promise-based** | Works perfectly with async/await syntax.                       |
| 🌍 **Standard API**  | Supported natively in browsers and Node (with polyfills).      |

---

### ❌ Cons of `fetch`

| Disadvantage                            | Description                                                                               |
| --------------------------------------- | ----------------------------------------------------------------------------------------- |
| 🚫 Doesn’t throw errors for HTTP errors | Even if the server returns `404` or `500`, `fetch` **resolves** instead of **rejecting**. |
| 🔄 Manual JSON conversion               | You must explicitly call `.json()` on every response.                                     |
| 🧩 Verbose syntax                       | Requires extra handling for headers, base URLs, and errors.                               |

---

### ⚙️ Example with Error Handling

```jsx
  fetch("https://jsonplaceholder.typicode.com/users")
    .then((res) => {
      if (!res.ok) throw new Error(`HTTP Error: ${res.status}`);
      return res.json();
    })
    .then((data) => console.log(data))
    .catch((err) => console.error("Error fetching data:", err));
```

✅ This ensures proper error messages for failed HTTP responses.

---

### 🧱 Using `async/await`

```jsx
  const fetchData = async () => {
    try {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      if (!res.ok) throw new Error("Failed to fetch users");
      const data = await res.json();
      console.log(data);
    } catch (error) {
      console.error(error);
    }
  };
```

---

# 🔸 `axios`

---

### 💡 What is `axios`?

`axios` is a **third-party HTTP client library** that simplifies data fetching and request handling in JavaScript and React.

📦 Install using:

```bash
npm install axios
```

Then import:

```js
import axios from "axios";
```

---

### ✅ Example

```jsx
  axios
    .get("https://opentdb.com/api.php?amount=5&type=multiple")
    .then((res) => console.log(res.data))
    .catch((err) => console.error(err));
```

---

### ✅ Pros of `axios`

| Advantage                     | Description                                                 |
| ----------------------------- | ----------------------------------------------------------- |
| 🧠 **Automatic JSON parsing** | No need to call `.json()`.                                  |
| 🚫 **Error handling**         | Automatically throws errors for bad HTTP status codes.      |
| ⚙️ **Easy configuration**     | Supports base URLs, headers, and interceptors.              |
| 🔁 **Interceptors**           | Modify requests/responses globally (e.g., add auth tokens). |
| ⚡ **Request cancellation**    | Supports `CancelToken` for aborting requests easily.        |

---

### ❌ Cons of `axios`

| Disadvantage             | Description                                    |
| ------------------------ | ---------------------------------------------- |
| 📦 Requires installation | Must install via npm or yarn.                  |
| 🧮 Slightly heavier      | Larger bundle size compared to native `fetch`. |

---

### 🧩 Example with Async/Await

```jsx
import axios from "axios";

  const getData = async () => {
    try {
      const res = await axios.get("https://jsonplaceholder.typicode.com/users");
      console.log(res.data);
    } catch (error) {
      console.error("Error fetching users:", error);
    }
  };
```

---

# ⚖️ Comparison: `fetch` vs `axios`

| Feature                 | `fetch`                     | `axios`                |
| ----------------------- | --------------------------- | ---------------------- |
| 🧱 Installation         | Not required                | `npm install axios`    |
| 🔄 JSON Parsing         | Manual: `.json()`           | Automatic              |
| 🚫 HTTP Error Handling  | Manual check (`!res.ok`)    | Automatic              |
| 🧩 Interceptors         | ❌ Not available             | ✅ Built-in             |
| 🧠 Base URL             | ❌ Must repeat URL           | ✅ Can define globally  |
| 🧹 Request Cancel       | Complex (`AbortController`) | Simple (`CancelToken`) |
| 🧮 Syntax               | Slightly verbose            | Cleaner and shorter    |
| 🌍 Browser Support      | All modern browsers         | All modern browsers    |
| ⚙️ File Upload/Download | Manual setup                | Built-in helpers       |

---

# 🧭 When to Use Which?

| Scenario                           | Recommended |
| ---------------------------------- | ----------- |
| Small / beginner project           | ✅ `fetch`   |
| Need simple GET/POST               | ✅ `fetch`   |
| Large app with auth headers        | ✅ `axios`   |
| Reusable base URL and interceptors | ✅ `axios`   |
| File uploads/downloads             | ✅ `axios`   |
| Pure browser-only app              | ✅ `fetch`   |

---

# ⚡ Example: Using `axios` with Base URL and Interceptor

```jsx
import axios from "axios";

const api = axios.create({
  baseURL: "https://jsonplaceholder.typicode.com",
});

// Interceptor to log requests
api.interceptors.request.use((config) => {
  console.log("Request Sent:", config.url);
  return config;
});

api.get("/users")
    .then((res) => console.log(res.data))
    .catch((err) => console.error(err));
```

---

# 🧠 `useEffect`

---

### 💡 What is `useEffect`?

`useEffect` is a **React Hook** that lets you perform **side effects** in function components.

👉 Side effects are **actions that affect something outside the component**, like:

* Fetching data from an API
* Updating the DOM
* Managing subscriptions
* Setting up timers or intervals

---

### 🧩 Basic Syntax

```jsx
useEffect(() => {
  // side effect logic
}, [dependencies]);
```

---

### ⚙️ Parameters Explained

| Parameter        | Description                                            |
| ---------------- | ------------------------------------------------------ |
| `callback`       | Function that runs after render (side effect code).    |
| `[dependencies]` | Array of variables — effect re-runs when these change. |

---

# 🔄 Lifecycle of `useEffect`

| Phase          | Behavior                                  | Example              |
| -------------- | ----------------------------------------- | -------------------- |
| **Mounting**   | Runs once when component loads (use `[]`) | API calls, setup     |
| **Updating**   | Runs when dependency changes              | Re-fetching, syncing |
| **Unmounting** | Cleanup using return function             | Removing listeners   |

---

### 🧩 Mounting Example

```jsx
useEffect(() => {
  console.log("Component mounted");
}, []);
```

### 🧩 Updating Example

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

### 🧹 Cleanup Example

```jsx
useEffect(() => {
  const interval = setInterval(() => console.log("Running..."), 1000);

  return () => clearInterval(interval); // cleanup when unmount
}, []);
```

---

# 📡 Data Fetching with Cleanup (using AbortController)

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch("https://jsonplaceholder.typicode.com/users", {
    signal: controller.signal,
  })
    .then((res) => res.json())
    .then((data) => console.log(data))
    .catch((err) => {
      if (err.name !== "AbortError") console.error(err);
    });

  return () => controller.abort(); // cancel fetch if unmounted
}, []);
```

---

# ⚙️ Common Mistakes with `useEffect`

| Mistake                             | Fix                                                           |
| ----------------------------------- | ------------------------------------------------------------- |
| ❌ Using async directly in useEffect | ✅ Create an inner async function                              |
| ❌ Forgetting dependencies           | ✅ Add all variables used in effect                            |
| ❌ Missing cleanup                   | ✅ Return cleanup function                                     |
| ❌ Running too often                 | ✅ Use correct dependency array                                |
| ❌ Infinite re-renders               | ✅ Don’t update state directly inside effect without condition |

---

# 🎬 Real-World Example: Movie Search App

```jsx
import React, { useState, useEffect } from "react";

const MovieSearch = () => {
  const [query, setQuery] = useState("spiderman");
  const [movie, setMovie] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  useEffect(() => {
    const fetchMovie = async () => {
      setLoading(true);
      setError("");
      try {
        const res = await fetch(
          `https://www.omdbapi.com/?t=${query}&apikey=your_api_key`
        );
        const data = await res.json();
        if (data.Response === "True") {
          setMovie(data);
        } else {
          setError("Movie not found!");
          setMovie(null);
        }
      } catch (err) {
        setError("Something went wrong!");
      }
      setLoading(false);
    };

    if (query) fetchMovie();
  }, [query]);

  return (
    <div style={{ textAlign: "center", marginTop: "20px" }}>
      <h2>🎬 Movie Search App</h2>
      <input
        type="text"
        placeholder="Enter movie name..."
        onKeyDown={(e) => e.key === "Enter" && setQuery(e.target.value)}
      />
      {loading && <p>Loading...</p>}
      {error && <p style={{ color: "red" }}>{error}</p>}
      {movie && (
        <div>
          <h3>
            {movie.Title} ({movie.Year})
          </h3>
          <img src={movie.Poster} alt={movie.Title} />
          <p>⭐ Rating: {movie.imdbRating}</p>
        </div>
      )}
    </div>
  );
};

export default MovieSearch;
```

---

## 🎯 Interview Questions

**Q1: What are the 2 Rules of Hooks?**

> 1. Only call hooks at the **top level** — never inside loops, conditions, or nested functions. 2. Only call hooks from **React functional components** or custom hooks — not regular JavaScript functions. These rules ensure React can maintain hook call order between renders.

**Q2: What are the three phases of `useEffect`?**

> Mount (runs after first render), Update (runs after re-render when dependencies change), Unmount (cleanup function runs when component is removed). The dependency array controls when the effect re-runs.

**Q3: What does an empty dependency array `[]` mean in `useEffect`?**

> The effect runs once after the first render and never again — equivalent to `componentDidMount` in class components.

**Q4: What is the difference between `fetch` and `axios`?**

> `fetch` is native browser API — needs manual JSON parsing, no automatic error handling for non-2xx status. `axios` is a library with automatic JSON parsing, automatic error throwing for non-2xx responses, request/response interceptors, and better request cancellation support.

**Q5: What is the cleanup function in `useEffect` and when do you need it?**

> The optional function returned from `useEffect` — runs when the component unmounts or before the effect re-runs. Use it to cancel API requests (AbortController), clear timers, or unsubscribe from subscriptions to avoid memory leaks.

---

## 🏠 Home Task

Build a **News Feed App**:
1. Fetch top headlines from a news API (e.g., NewsAPI or GNews)
2. Show article title, description, image, and source
3. Loading spinner while fetching
4. Error message if fetch fails
5. Search input — debounced (wait 500ms), fetches new results on change
6. Category tabs: Technology, Sports, Business, Health — each triggers a new fetch
7. Use `AbortController` to cancel previous request when category changes
8. Bonus: Implement `useEffect` cleanup to prevent state updates on unmounted component