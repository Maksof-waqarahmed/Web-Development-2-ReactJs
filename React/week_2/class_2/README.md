# ⚛️ Conditional Rendering in React

---

## 🧩 What is Conditional Rendering?

**Conditional Rendering** means:

> Displaying different UI elements based on certain conditions (true or false).

In simple words —
👉 It’s how React decides **“what to show and what to hide”** depending on app state, user actions, or data.

---

## 🧠 Why Do We Need Conditional Rendering?

In real-world React applications, we **don’t always show everything at once**.
We render components conditionally based on situations like:

| Scenario          | Example                                                |
| ----------------- | ------------------------------------------------------ |
| ✅ User Login      | Show dashboard if logged in, otherwise show login form |
| ⏳ Loading         | Show a loading spinner while fetching data             |
| ⚠️ Error Handling | Display error messages only when there’s an error      |
| 🛒 Cart           | Show cart items only when user has added something     |

---

## ⚙️ How Does It Work?

React uses **JavaScript logic** inside JSX to decide **what to render**.
We can use JavaScript control structures like:

* `if` / `else`
* Ternary operator `? :`
* Logical AND `&&`
* Logical OR `||`
* `switch` statements (less common)

Each of these methods has its own use case depending on complexity.

---

## ✨ Methods of Conditional Rendering (Step-by-Step)

---

### ✅ 1. Using `if...else` (Outside JSX)

This is useful when your logic is **complex** and you don’t want to clutter your JSX.

```jsx
function App() {
  const isLoggedIn = true;
  let content;

  if (isLoggedIn) {
    content = <h2>Welcome back, User!</h2>;
  } else {
    content = <h2>Please log in.</h2>;
  }

  return <div>{content}</div>;
}
```

**🧩 Explanation:**

* We define a variable `content`
* Based on the condition, assign JSX to it
* Then return that variable inside JSX

**✅ Best for:**
When you have **multiple if-else conditions** or **complex branching logic**.

---

### ✅ 2. Using Ternary Operator `condition ? true : false` (Inline JSX)

Perfect for **simple, inline** conditions.
It’s short and easy to read.

```jsx
function Greeting() {
  const isLoggedIn = false;

  return (
    <div>
      {isLoggedIn ? <h2>Welcome!</h2> : <h2>Please log in</h2>}
    </div>
  );
}
```

**🧩 Explanation:**

* If `isLoggedIn` is `true` → show “Welcome!”
* Else → show “Please log in”

**✅ Best for:**
Simple true/false UI changes within the same return statement.

---

### ✅ 3. Using Logical AND (`&&`)

When you want to render something **only if a condition is true**.
If the condition is false, React ignores it.

```jsx
function Cart() {
  const hasItems = true;

  return (
    <div>
      <h1>My Cart</h1>
      {hasItems && <p>You have items in your cart.</p>}
    </div>
  );
}
```

**🧩 Explanation:**

* If `hasItems` is true → shows the `<p>` tag
* If false → renders nothing

**✅ Best for:**
Rendering **optional elements** (like messages, banners, or extra sections).

---

### ✅ 4. Using Logical OR (`||`)

This is used to **show a fallback** when the condition is falsy.

```jsx
function Welcome() {
  const username = "";

  return <p>Welcome, {username || "Guest"}!</p>;
}
```

**🧩 Explanation:**

* If `username` is empty, `Guest` will be shown.
* Useful for default values or placeholders.

**✅ Best for:**
Providing **fallback values** or **defaults**.

---

### ✅ 5. Using `switch` (For Multiple Conditions)

Less common but great when you have **many possible UI outputs**.

```jsx
function Status({ status }) {
  switch (status) {
    case "loading":
      return <p>Loading...</p>;
    case "success":
      return <p>Data loaded successfully ✅</p>;
    case "error":
      return <p>Something went wrong ❌</p>;
    default:
      return <p>Idle state</p>;
  }
}
```

---

## 🧪 Practical Examples

---

### 🧭 Example 1: `if` Statement + useState Hook

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

---

### 💡 Example 2: Ternary Operator — Light Bulb App

```jsx
import { useState } from 'react';

export default function BulbApp() {
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

---

### 🧠 Example 3: Logical `&&` Operator

```jsx
import { useState } from 'react';

export default function AndExample() {
  const [showMessage, setShowMessage] = useState(false);

  return (
    <div style={{ textAlign: 'center' }}>
      <button onClick={() => setShowMessage(true)}>Show Message</button>
      {showMessage && <p>Hello! This is a conditional message.</p>}
    </div>
  );
}
```

---

### ⚙️ Example 4: Logical `||` Operator (Fallback)

```jsx
export default function OrExample() {
  const userName = ""; // Try changing to "Rana"

  return (
    <div style={{ textAlign: 'center' }}>
      <h2>Welcome, {userName || 'Guest'}!</h2>
    </div>
  );
}
```

---

## ⚠️ Common Mistakes

| ❌ Mistake                                         | ✅ Correct Solution                                    |
| ------------------------------------------------- | ----------------------------------------------------- |
| Using `if` directly inside JSX                    | Move `if` logic outside JSX                           |
| Forgetting `return` in `if` blocks                | Always `return` JSX properly                          |
| Not wrapping multiple elements in a single parent | Use `<div>` or `<>` fragment                          |
| Overusing ternary inside JSX (nested)             | Split into smaller components or use helper functions |

---

## 💡 Best Practices

✅ Keep JSX **clean** — move heavy logic outside JSX
✅ Use **ternary** for simple conditions
✅ Use **`&&`** for showing optional UI elements
✅ Use **`if`** for complex logic
✅ Avoid **nested ternary operators** (hard to read)

---

## 📚 Summary Table

| Method      | Use When                | Example                           |               |            |   |           |
| ----------- | ----------------------- | --------------------------------- | ------------- | ---------- | - | --------- |
| `if / else` | Complex logic           | `if (x) { ... } else { ... }`     |               |            |   |           |
| `? :`       | Simple inline condition | `{isLoggedIn ? <A/> : <B/>}`      |               |            |   |           |
| `&&`        | Show only if true       | `{hasItems && <p>Item found</p>}` |               |            |   |           |
| `           |                         | `                                 | Show fallback | `{username |   | "Guest"}` |
| `switch`    | Multiple UI outcomes    | `switch(status) { ... }`          |               |            |   |           |

---