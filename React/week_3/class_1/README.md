# 🎯 React Forms — Handling Events & User Interactions

## 📚 Topics Covered
- React Synthetic Event System
- Event naming conventions (camelCase)
- `onClick`, `onChange`, `onSubmit` handlers
- `preventDefault()` — stopping page reload
- Controlled vs Uncontrolled components
- Form validation basics
- `key` prop in lists — why it matters
- Project: Todo App (Create, Delete)

---

Forms in React are one of the most common ways to interact with users — typing input, submitting data, clicking buttons, etc.
But unlike regular HTML, **React controls everything via state and event handlers**, allowing **predictable and dynamic behavior**.

This helps React applications stay **declarative**, meaning the UI always reflects the current state — not the other way around.

---

## 🔹 What Are Events in React?

In React, **events** are similar to native browser events (like `click`, `change`, `submit`) — but React uses its own **Synthetic Event System**.

🧩 **Synthetic Event** is a wrapper around the browser’s native event. It provides **cross-browser compatibility** and **consistent behavior** across all environments.

For example:

| HTML Event  | React Equivalent |
| ----------- | ---------------- |
| `onclick`   | `onClick`        |
| `onchange`  | `onChange`       |
| `onsubmit`  | `onSubmit`       |
| `onkeydown` | `onKeyDown`      |

---

### 💡 Why React Uses Synthetic Events

1. ✅ **Cross-browser support:** Works the same in all browsers.
2. ⚡ **Performance optimization:** React reuses event objects using an event pooling system.
3. 🔄 **Consistency:** Ensures uniform event handling across different platforms.

---

## 🧩 Example: Handling Events

```jsx
function EventExample() {
  function handleClick() {
    alert("Button clicked!");
  }

  return <button onClick={handleClick}>Click Me</button>;
}
```

📘 **Explanation:**

* The event name is written in **camelCase** (`onClick` not `onclick`).
* You pass a **function reference**, not a function call (`onClick={handleClick}` not `onClick={handleClick()}`).
* React automatically passes the **event object** as the first parameter when the function is called.

---

## 🧠 Example: Handling Input Change

```jsx
import { useState } from "react";

function InputExample() {
  const [name, setName] = useState("");

  const handleChange = (e) => {
    setName(e.target.value);
  };

  return (
    <div>
      <input type="text" value={name} onChange={handleChange} />
      <p>Hello, {name}!</p>
    </div>
  );
}
```

📝 **Explanation:**

* `onChange` triggers whenever the user types something.
* `e.target.value` accesses the input’s latest value.
* React updates `name` using `setName()`, re-rendering the UI instantly.
* This is how React maintains a **two-way data binding** between UI and state.

---

## 🔹 Controlled vs Uncontrolled Components

When dealing with form elements (inputs, selects, textareas, etc.), there are two main patterns:

1. **Controlled Components** → React manages the data.
2. **Uncontrolled Components** → The DOM manages the data.

---

### 🔸 Controlled Component

A **controlled component** is a form element that is **fully controlled by React state**.
Whatever the user types or selects goes directly into the component's **state variable**, and the state decides what is displayed.

### ✅ Example:

```jsx
import { useState } from "react";

function ControlledForm() {
  const [email, setEmail] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Email Submitted: ${email}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### 🧠 Key Points:

* The input field’s `value` is **bound to React state**.
* Any change in state instantly updates the input and vice versa.
* React fully **controls the data flow** and makes debugging easier.
* You can easily **validate** and **manipulate** the input data before submission.

---

### 🔸 Uncontrolled Component

In **uncontrolled components**, form data is managed by the **DOM**, not React.
Instead of `useState`, we use `useRef()` to directly access the element’s value when needed.

### ✅ Example:

```jsx
import { useRef } from "react";

function UncontrolledForm() {
  const nameRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Name Entered: ${nameRef.current.value}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" ref={nameRef} placeholder="Enter name" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### ⚙️ When to Use:

* When using **third-party libraries** that already manage their own inputs.
* For **simple, quick forms** that don’t require validation.
* When you want **minimal re-rendering** for performance reasons.
* For **file uploads**, since file inputs can’t be easily controlled.

---

## ⚖️ Controlled vs Uncontrolled Summary

| Feature       | Controlled            | Uncontrolled |
| ------------- | --------------------- | ------------ |
| Data Source   | React State           | DOM          |
| Access Method | `useState`            | `useRef`     |
| Validation    | Easy                  | Manual       |
| Syncing       | Automatic             | Manual       |
| Re-render     | Yes (on every change) | No           |
| Use Case      | Complex forms         | Simple forms |

🧠 **Tip:** Always prefer **controlled components** when you need to validate, store, or modify input data dynamically.

---

## 📌 When to Use Which?

### ✅ Use Controlled:

* When your form requires **validation**, **instant feedback**, or **dynamic behavior**.
* When you need to store or update input values globally (e.g., Redux or Context).
* For predictable and debuggable state management.

### ❌ Use Uncontrolled:

* When forms are **simple** and **don’t need validation**.
* When using external **non-React libraries** that manipulate the DOM.
* When you want **performance optimization** (less state updates).

---

## 🤔 Summary

* **Controlled** = React is in charge (uses state).
* **Uncontrolled** = DOM is in charge (uses refs).
* ✅ Controlled components are **recommended** for most use cases.

---

## 🧠 Handling User Interaction in React

User interaction includes **clicking, typing, hovering, focusing, submitting**, and more.
Each of these triggers an **event handler**, which can update the **state**, **perform logic**, or **fetch data**.

### 🎯 Example: Button + Input Interaction

```jsx
import { useState } from "react";

function GreetingApp() {
  const [name, setName] = useState("");
  const [greet, setGreet] = useState("");

  const handleClick = () => {
    setGreet(`Hello, ${name || "Stranger"} 👋`);
  };

  return (
    <div>
      <input
        type="text"
        placeholder="Enter your name"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <button onClick={handleClick}>Greet Me</button>
      <h3>{greet}</h3>
    </div>
  );
}
```

💡 **Explanation:**

* The user types in an input → state updates → React re-renders → new greeting appears.
* This is the essence of **React’s one-way data flow**.

---

## Project

### ✅ TODO APP (Simple)

---

```jsx
import { useState } from 'react'

export default function App() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  const getInputValue = (e) => {
    const value = e.target.value.trim();
    setInput(value);
  }

  const addTodo = () => {
    if (!input.trim()) return alert("Todo is required");
    const copyTodoList = [...todos];
    copyTodoList.push(input);
    setTodos(copyTodoList);
    // setTodos([...todos, input]); short way
    setInput('');
  };

  const removeTodo = (index) => {
    const copyTodoList = [...todos];
    copyTodoList.splice(index, 1);
    setTodos(copyTodoList);
  };

  return (
    <div>
      <input
        value={input}
        onChange={getInputValue}
        placeholder="Enter todo"
      />
      <button onClick={addTodo}>Add</button>

      <ul>
        {todos.map((todo, index) => (
          <li key={index}>
            {todo} <button onClick={() => removeTodo(index)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 🔹 What is a Key in React?

In React, a **key** is a special attribute used to give **unique identity** to each element in a list.

### 📌 Why Keys Are Important

* Keys help React **identify which items have changed, been added, or removed**.
* They make list rendering **efficient** by allowing React to update **only the changed elements**, not the entire list.

---

### ✅ Example:

```jsx
const products = [
  { id: 1, name: "Laptop" },
  { id: 2, name: "Phone" },
  { id: 3, name: "Watch" },
];

function App() {
  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

**Explanation:**

* Each `<li>` has a `key` that matches the product's `id`.
* If a product is removed or added, React uses the `key` to **determine exactly which element to update**.

---

### ⚡ Key Rules

1. **Keys must be unique among siblings.**

   * A common choice is an `id` from your data.

2. **Keys are used internally by React only.**

   * They are **not accessible via `props`** inside child components.

3. **Use keys when rendering lists or arrays of elements.**

   * You **don’t need keys** for single components.

---

### ⚠️ Using Index as Key

```jsx
<li key={index}>{product.name}</li>
```

* Works, but **not recommended** for dynamic lists (adding/removing/reordering items).
* It can cause **unexpected re-renders** and **UI mismatch**.
* Prefer a **unique ID** if possible.

---

### 🧠 Quick Analogy

> Keys are like **roll numbers in a classroom**:
> If a new student joins or one leaves, the teacher uses roll numbers to identify students without confusion.

---

---

## 🎯 Interview Questions

**Q1: What is a Synthetic Event in React?**

> React wraps native browser events in a `SyntheticEvent` object that has the same interface across all browsers. This ensures consistent event handling behavior regardless of the browser.

**Q2: What is the difference between a controlled and uncontrolled component?**

> Controlled: React state is the single source of truth — `value={state}` + `onChange` handler. Uncontrolled: DOM manages its own state — accessed via `useRef`. Controlled components are preferred because you always know the current value.

**Q3: Why do we call `e.preventDefault()` on form submit?**

> By default, form submission causes a full page reload (browser's native behavior). `preventDefault()` stops this so React can handle the data without refreshing the page.

**Q4: Why must list items have a `key` prop?**

> React uses `key` to identify which items changed, were added, or removed in a list. Without keys, React has to re-render the entire list. Keys must be unique among siblings and stable (not the array index for dynamic lists).

**Q5: What is the difference between `onChange` in React and the native DOM `change` event?**

> The native `change` event fires only when an input loses focus. React's `onChange` fires on every keystroke (more like `input` event). This makes controlled components work smoothly.

---

## 🏠 Home Task

Extend the **Todo App** with:
1. **Edit functionality** — clicking Edit loads the todo into the input field and shows an "Update" button
2. **Delete with confirmation** — confirm before removing a todo
3. **Filter buttons** — "All", "Active", "Completed" (toggle done/undone)
4. **Mark all complete** — single button to check all todos
5. **Count display** — "3 of 7 tasks completed"
6. Bonus: Persist todos to `localStorage` so they survive page refresh