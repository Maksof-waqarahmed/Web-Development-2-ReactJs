# 🧠 What is `useEffect`?

`useEffect` is a **React Hook** that lets you run **side effects** after a component renders.

👉 “Side effects” mean tasks that go **beyond just rendering UI**, such as:

* Fetching data from an API
* Starting a timer/interval
* Adding or removing event listeners
* Updating `document.title`
* Logging values in the console

Basic syntax:

```jsx
useEffect(() => {
  // your side effect goes here
}, [dependencies]);
```

---

## ⏱️ `useEffect` Life Cycle

### 1. **Mounting Phase** (When the component loads for the first time)

👉 If you pass an empty dependency array `[]`, the effect runs **only once**.

```jsx
useEffect(() => {
  console.log("Component loaded for the first time");
}, []);
```

---

### 2. **Updating Phase** (When state or props change)

👉 If you pass dependencies, the effect runs **only when those values change**.

```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

---

### 3. **Unmounting Phase** (When the component is removed)

👉 Before a component unmounts, you can clean up using a return function.

```jsx
useEffect(() => {
  const timer = setInterval(() => console.log("Running..."), 1000);

  return () => clearInterval(timer); // cleanup
}, []);
```

---

## 🧹 Cleanup (very important)

Cleanup is used to **stop or remove things** that you start inside the effect:

* Remove event listeners
* Clear timers/intervals
* Cancel API requests

```jsx
useEffect(() => {
  const handler = (e) => console.log(e.clientX);
  window.addEventListener("mousemove", handler);

  return () => window.removeEventListener("mousemove", handler);
}, []);
```

---

## 📡 Example: Data Fetching with Cancel

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/users", { signal: controller.signal })
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => {
      if (err.name !== "AbortError") console.error(err);
    });

  return () => controller.abort(); // cancel request if component unmounts
}, []);
```

---

## 🧠 Simple Rules of `useEffect`

1. **`[]` →** runs only on mount (once).
2. **`[value]` →** runs when `value` changes.
3. **Always use cleanup** if you add listeners/timers.
4. If you use functions/objects as dependencies → wrap them in `useCallback` or `useMemo` to avoid unnecessary re-renders.

---

## ⚠️ Common Mistakes

❌ Writing `async` directly in `useEffect` (not allowed).
✅ Create an inner async function inside.

❌ Ignoring dependencies.
✅ Always include every state/prop used inside the effect in the dependency array.

---

## 🔑 Easy Summary

* **Mounting** → Runs once (`[]`)
* **Updating** → Runs when dependencies change (`[count]`)
* **Unmounting** → Cleanup runs automatically

---

### API Fetch Example

```jsx
import React, { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // API call inside useEffect
    fetch("https://jsonplaceholder.typicode.com/users")
      .then((res) => res.json())
      .then((data) => setUsers(data));
  }, []); // Empty dependency → runs only once

  return (
    <div>
      <h2>Users List (API Fetch)</h2>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} - {user.email}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default Users;
```

### 🎬 Movie Search App

```tsx
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

    if (query) {
      fetchMovie();
    }
  }, [query]); // query change hote hi new search

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
          <h3>{movie.Title} ({movie.Year})</h3>
          <img src={movie.Poster} alt={movie.Title} />
          <p>⭐ Rating: {movie.imdbRating}</p>
        </div>
      )}
    </div>
  );
};

export default MovieSearch;
```