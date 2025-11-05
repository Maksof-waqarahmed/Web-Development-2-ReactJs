## 💬 Interview Questions — Conditional Rendering

---

### 1. ❓ What is conditional rendering in React?

**Answer:**
It’s the process of rendering components or elements based on a certain condition (true/false).

---

### 2. ❓ How many ways can we implement conditional rendering in React?

**Answer:**

* `if / else`
* Ternary (`? :`)
* Logical AND (`&&`)
* Logical OR (`||`)
* Switch statements

---

### 3. ❓ Can we use `if` inside JSX?

**Answer:**
❌ No. JSX is an expression, so `if` statements must be used **outside JSX**.
✅ Use ternary operators or logical operators inside JSX.

---

### 4. ❓ When should we use `&&` operator in JSX?

**Answer:**
Use `&&` when you want to render an element only if the condition is true.
If it’s false, React ignores it.

---

### 5. ❓ What is the difference between `&&` and `||` in conditional rendering?

| Operator | Meaning                                      | Example                         |                                          |            |   |           |
| -------- | -------------------------------------------- | ------------------------------- | ---------------------------------------- | ---------- | - | --------- |
| `&&`     | Renders right side only if left side is true | `{isLoggedIn && <Dashboard />}` |                                          |            |   |           |
| `        |                                              | `                               | Renders right side if left side is falsy | `{username |   | "Guest"}` |

---

### 6. ❓ How can you conditionally render multiple elements without using `div`?

**Answer:**
By using **React Fragments (`<> </>`)**.

---

### 7. ❓ Why is conditional rendering important in React?

**Answer:**
It allows **dynamic UI updates** — showing or hiding components based on user interaction or data, making apps interactive and efficient.

---

## 🎯 Final Example: Combine All

```jsx
function UserProfile({ user }) {
  if (!user) {
    return <p>Loading...</p>;
  }

  return (
    <div>
      <h1>Hello, {user.name || "Guest"}</h1>
      {user.isAdmin && <p>Welcome Admin!</p>}
      {user.age > 18 ? <p>Adult</p> : <p>Underage</p>}
    </div>
  );
}
```

---