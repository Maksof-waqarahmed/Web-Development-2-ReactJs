# JSX & Functional Components

In this step, we will learn what **JSX** is and how to create **functional components** in React. This is the foundation of React development.

---

## 📘 What is JSX?

**JSX** stands for **JavaScript XML**.
It allows us to write **HTML-like code** inside JavaScript.
React uses this JSX code to create the user interface (UI).

### 🎯 Example:

```jsx
const h1 = <h1>Hello World</h1>
```

This looks like HTML, but it's actually JSX.

---

## 🧠 Rules of JSX

1. **Only one parent tag allowed**
   Wrap everything inside a single parent tag (usually a `<div>`).

   ✅ Correct:

   ```jsx
   return (
     <div>
       <h1>Hello</h1>
       <p>Welcome</p>
     </div>
   );
   ```

   ❌ Incorrect:

   ```jsx
   return (
     <h1>Hello</h1>
     <p>Welcome</p>
   );
   ```

2. **Use `className` instead of `class`**
   Since `class` is a reserved keyword in JavaScript, JSX uses `className`.

3. **All tags must be closed properly**
   Even self-closing tags like `<img />` or `<input />` must be properly closed.

4. **Write JavaScript expressions inside `{}`**
   To use variables or expressions inside JSX, wrap them in curly braces.

   Example:

   ```jsx
   const name = "Ali";
   return <h1>Hello {name}</h1>;
   ```

---

## ⚙️ Functional Components

There are two ways to create components in React: **class-based** and **functional**.
But **functional components** are the most commonly used.

### ✅ Syntax 1: Function

```jsx
function MyComponent() {
  return <h1>Hello</h1>;
}
```

### ✅ Syntax 2: Arrow Function

```jsx
const MyComponent = () => {
  return <h1>Hello</h1>;
};
```

✅ These components always **return JSX**.
✅ You can use them **multiple times**, just like regular HTML elements.

---

## 🔄 Reusing a Component

Once a component is made, you can reuse it like an HTML tag:

```jsx
<MyComponent />
```

---

## 📦 Export and Import

To use a component from one file in another, you need to **export** it and then **import** it.

### 🔹 1. Default Export:

**MyComponent.jsx**

```jsx
const MyComponent = () => {
  return <h1>Hello</h1>;
};

export default MyComponent;
```

**App.jsx**

```jsx
import MyComponent from './MyComponent';
```

---

### 🔹 2. Named Export:

**Header.jsx**

```jsx
export const Header = () => {
  return <h1>This is a Header</h1>;
};
```

**App.jsx**

```jsx
import { Header } from './Header';
```

---

## 📌 Summary

* JSX allows writing **HTML inside JavaScript**.
* Components are **reusable building blocks** of React.
* You can use both **function** and **arrow function** to create components.
* Use `export` and `import` to share components across files.
* Always return **only one parent element** from a component.
* **Close all tags properly.**
* Use `className` instead of `class`.

---

## 🔹 What is `useState`?

`useState` is a **React hook** that allows you to create **state variables** inside functional components.

### 🔸 Example:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

### 🧠 Key Points:

* `useState` returns **two values**: the current state and a function to update it.
* When the state changes, the component **re-renders**.
* State is **local** to the component.

---

## 🔹 Variable vs State in React

| Feature              | Variable (`let` / `const`) | `useState` (React State)   |
| -------------------- | -------------------------- | -------------------------- |
| Scope                | Local to function/block    | Stored in React memory     |
| UI updates on change | ❌ No                       | ✅ Yes                      |
| Update method        | Direct assignment (`=`)    | `setState` method (`setX`) |
| Re-render behavior   | No re-render               | Triggers re-render         |

---

### ⚠️ Wrong Way:

```js
// ❌ This won't re-render
count = count + 1;
```

### ✅ Correct Way:

```js
setCount(count + 1);
```

React detects the change and updates the UI.

---

## 🔁 When Does Re-render Happen?

* ✅ When **state changes**, React re-renders **that specific component**.
* ⚠️ If that state is **passed as props**, then **child components also re-render**.

---

## 🔐 Mutable vs Immutable

| Concept       | Variable (`let`, `const`) | State (`useState`)                     |
| ------------- | ------------------------- | -------------------------------------- |
| Mutable?      | ✅ Yes                     | ❌ No (Immutable — use setter function) |
| Update Method | Direct assignment         | Must use `setState`                    |

---

## 📦 Why is State Immutable?

➕ **React uses immutability** to efficiently detect changes by comparing the **previous and new state**.
This leads to **better performance**, **fewer bugs**, and **predictable behavior**.

---

## 🔍 Summary Table

| Concept         | Variable (`let` / `const`) | React State (`useState`) |
| --------------- | -------------------------- | ------------------------ |
| Scope           | Function/local             | React component memory   |
| Tracks changes? | ❌ No                       | ✅ Yes                    |
| UI re-renders?  | ❌ No                       | ✅ Yes                    |
| Mutable?        | ✅ Yes                      | ❌ No (Immutable)         |
| Use case        | Temporary logic            | UI interaction/state     |

---

## 💡 Interview Insight

> “In React, state is like the memory of your component. When state changes, the UI updates automatically. Variables, on the other hand, change silently — React doesn’t notice.”

---

### useState()

### Counter App

```jsx
import { useState } from 'react'

export default function App() {
  const [count, setCount] = useState(0);

  const updateCount = () => {
    setCount(count + 1);
    console.log("Before re-render (stale value):", count);
  }

  const decreaseCount = () => {
    if (count > 0) setCount(count - 1);
  }

  const increaseCountBy5 = () => {
    setCount(count + 5);
  }

  const decreaseCountBy5 = () => {
    setCount(prev => Math.max(0, prev - 5));
  }

  const resetCount = () => {
    setCount(0);
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={updateCount}>Increase by 1</button>
      <button onClick={decreaseCount}>Decrease by 1</button>
      <button onClick={increaseCountBy5}>Increase by 5</button>
      <button onClick={decreaseCountBy5}>Decrease by 5</button>
      <button onClick={resetCount}>Reset</button>
    </div>
  )
}
```

**Notes for Students:**
* `console.log(count)` inside `updateCount` logs the old value because state updates are asynchronous.

---

1. **Plain variable (not React state):**

   ```js
   let count = 0;
   function increment() {
     count += 1;
     console.log(count); // value changes but UI doesn’t update automatically
   }
   ```

   *Explaination:* Changing `count` here updates the variable, but the screen (UI) doesn’t know it should re-render. So the user doesn’t see the change unless you manually force an update.

2. **React state (`useState`):**

   * When you call `setCount(count + 1)`, React *knows* the value changed and re-renders that part of UI automatically.
   * Think **state is the “memory” React tracks**, whereas a plain variable is just temporary.

3. **The “stale log” gotcha:**

   * In functional component, `console.log(count)` right after `setCount(...)` logs the previous value beacuse state updates are scheduled; React batches and updates before the next render.