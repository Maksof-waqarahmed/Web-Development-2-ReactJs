### 📘 Conditional Rendering in React (or JavaScript)

**Conditional rendering** means:
👉 "Only show something in the UI **if a certain condition is true**."

Just like in regular JavaScript we use `if`, `?:`, `&&`, etc., in React we do the same — but within JSX.

---

## 🧠 **Why Use Conditional Rendering?**

In real-world apps:

* Sometimes, we want to **show/hide components** based on user actions.
* For example:

  * Show a **loading spinner** while data is loading.
  * Show a **message** only if there's an error.
  * Display **different buttons** for admin vs. regular users.

---

## ✨ Different Methods of Conditional Rendering

---

### ✅ 1. `if` statement (before return, outside JSX)

Use when logic is more complex.

```jsx
function App() {
  const isLoggedIn = true;
  let content;

  if (isLoggedIn) {
    content = <h2>Welcome back!</h2>;
  } else {
    content = <h2>Please log in</h2>;
  }

  return <div>{content}</div>;
}
```

---

### ✅ 2. Ternary operator `condition ? true : false` (inline JSX)

Use for **short and readable** conditions inside JSX.

```jsx
function App() {
  const isLoggedIn = false;

  return (
    <div>
      {isLoggedIn ? <h2>Welcome!</h2> : <h2>Please log in</h2>}
    </div>
  );
}
```

---

### ✅ 3. `&&` (AND operator)

Use when you want to **show something only if condition is true**.
(It does nothing if false.)

```jsx
function App() {
  const hasItems = true;

  return (
    <div>
      <h1>My Cart</h1>
      {hasItems && <p>You have items in your cart.</p>}
    </div>
  );
}
```

---

### ✅ 4. `||` (OR operator – less common)

Use to show a default fallback message if the condition is false.

```jsx
function App() {
  const username = "";

  return <p>{username || "Guest User"}</p>;
}
```

---

## 🔄 Combining These in Real Examples

```jsx
function UserProfile({ user }) {
  return (
    <div>
      <h1>Hello {user.name}</h1>
      {user.isAdmin && <p>You are an admin</p>}
      {user.age > 18 ? <p>Adult</p> : <p>Underage</p>}
    </div>
  );
}
```

---

## ⚠️ Common Mistakes

* ❌ Forgetting `return` when using `if` blocks before `return`.
* ❌ Using `if` inside JSX (not allowed).
* ✅ Solution: move logic **outside** JSX and use variables.

---

## 📚 Summary Table

| Method | Use When                  | Example                            |                        |             |   |            |
| ------ | ------------------------- | ---------------------------------- | ---------------------- | ----------- | - | ---------- |
| `if`   | Complex logic outside JSX | `if (x) { ... }`                   |                        |             |   |            |
| `? :`  | Simple inline condition   | `{isLoggedIn ? <A /> : <B />}`     |                        |             |   |            |
| `&&`   | Only render if true       | `{hasItems && <p>Items found</p>}` |                        |             |   |            |
| `||`   | Only render if one ondition is true       | `{userName || Guest}` |                        |             |   |            |


---

## Projects

### Conditional Rendering

---

### ✅ 1. `if` Statement (Outside JSX)

> Use this when the logic is complex or when you want to avoid cluttering your JSX.

```jsx
import { useState } from 'react';

export default function IfExample() {
  const [isLoggedIn, setIsLoggedIn] = useState(true);
  let message;

  if (isLoggedIn) {
    message = <h2>Welcome back, User!</h2>;
  } else {
    message = <h2>Please log in.</h2>;
  }

  return (
    <div style={{ textAlign: 'center' }}>
      {message}
      <button onClick={() => setIsLoggedIn(!isLoggedIn)}>
        Toggle Login
      </button>
    </div>
  );
}
```

### ✅ 2. Ternary `? :` Operator (Inside JSX)

> Use this when rendering **one of two components** based on a condition.

```jsx
import { useState } from 'react';

export default function App() {
  const [off, setOff] = useState(true);

  const toggleBulb = () => {
    setOff(!off);
  };

  return (
    <div style={{ textAlign: 'center', padding: '20px' }}>
      <button onClick={toggleBulb}>
        {off ? 'Turn On' : 'Turn Off'}
      </button>

      <div style={{ marginTop: '20px' }}>
        {off ? (
          <img
            style={{ width: '100px', height: '100px' }}
            src="https://thumbs.dreamstime.com/b/d-illustration-empty-light-bulb-isolated-white-background-realistic-d-rendering-electric-lamp-inside-d-103349315.jpg"
            alt="Bulb Off"
          />
        ) : (
          <img
            style={{ width: '100px', height: '100px' }}
            src="https://5.imimg.com/data5/WL/SG/MY-8835990/10w-gls-light-bulb.jpg"
            alt="Bulb On"
          />
        )}
      </div>
    </div>
  );
}
```

### ✅ 3. Logical `&&` Operator (Short-Circuit Rendering)

> Use this when you want to render something **only if a condition is true**.

```jsx
import { useState } from 'react';

export default function AndExample() {
  const [showMessage, setShowMessage] = useState(false);

  return (
    <div style={{ textAlign: 'center' }}>
      <button onClick={() => setShowMessage(true)}>
        Show Message
      </button>

      {showMessage && <p>Hello! This is a conditional message.</p>}
    </div>
  );
}
```

### ✅ 4. Logical `||` Operator (Fallback if Falsy)

> Use this to show a default value if the left-hand value is falsy.

```jsx
export default function OrExample() {
  const userName = ""; // Try changing this to "Rana"

  return (
    <div style={{ textAlign: 'center' }}>
      <h2>Welcome, {userName || 'Guest'}!</h2>
    </div>
  );
}
```