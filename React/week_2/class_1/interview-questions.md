# 💬 JSX & Functional Components — Interview Questions with Answers

---

### 1. ❓ What is JSX in React?

**Answer:**
JSX stands for **JavaScript XML**.
It allows us to write **HTML-like syntax inside JavaScript**. React converts this JSX into **React elements** using a process called **transpilation** (done by Babel).

**Example:**

```jsx
const element = <h1>Hello React!</h1>;
```

This looks like HTML but is actually JavaScript code that React converts into:

```js
React.createElement('h1', null, 'Hello React!');
```

---

### 2. ❓ Why do we use JSX instead of plain JavaScript?

**Answer:**
JSX makes the code:

* Easier to **read and write**
* **Declarative**, meaning we describe *what* we want, not *how* to do it
* Helps detect **syntax errors** during compilation

Without JSX:

```js
const element = React.createElement('h1', null, 'Hello React');
```

With JSX:

```jsx
const element = <h1>Hello React</h1>;
```

---

### 3. ❓ What are the rules of JSX?

**Answer:**
✅ Must have **one parent tag**
✅ Use **className** instead of `class`
✅ All tags must be **closed properly**
✅ JS expressions must be written inside `{}`

**Example:**

```jsx
const name = "Ali";
return (
  <div className="card">
    <h2>Hello {name}</h2>
  </div>
);
```

---

### 4. ❓ What is a functional component in React?

**Answer:**
A **functional component** is a **JavaScript function** that returns **JSX**.
It is the modern way of writing components in React.

**Example:**

```jsx
function Welcome() {
  return <h1>Hello, User!</h1>;
}
```

---

### 5. ❓ What is the difference between Class Components and Functional Components?

| Feature           | Class Component                         | Functional Component         |
| ----------------- | --------------------------------------- | ---------------------------- |
| Syntax            | Uses `class` keyword                    | Uses function/arrow function |
| State             | Uses `this.state`                       | Uses `useState` Hook         |
| Lifecycle Methods | Uses methods like `componentDidMount()` | Uses `useEffect`             |
| Code Length       | Longer                                  | Shorter and cleaner          |
| Performance       | Slightly slower                         | Faster and lightweight       |

---

### 6. ❓ What are Hooks in React?

**Answer:**
Hooks are special functions that allow functional components to use **state** and **lifecycle features**.

**Common Hooks:**

* `useState` — to manage state
* `useEffect` — to handle side effects
* `useContext` — to use context data

**Example using `useState`:**

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

---

### 7. ❓ Can we return multiple elements in JSX without using a `<div>`?

**Answer:**
Yes ✅ — by using **React Fragments** (`<></>` or `<React.Fragment>`).

**Example:**

```jsx
return (
  <>
    <h1>Hello</h1>
    <p>Welcome to React!</p>
  </>
);
```

---

### 8. ❓ What is the difference between default export and named export?

| Type           | Syntax                            | Import                               |
| -------------- | --------------------------------- | ------------------------------------ |
| Default Export | `export default MyComp;`          | `import MyComp from './MyComp';`     |
| Named Export   | `export const Header = () => {};` | `import { Header } from './Header';` |

✅ **Only one default export** per file
✅ **Multiple named exports** allowed

---

### 9. ❓ Why is `className` used instead of `class` in JSX?

**Answer:**
Because `class` is a **reserved keyword** in JavaScript.
JSX uses `className` to assign CSS classes.

**Example:**

```jsx
<div className="container">Hello</div>
```

---

### 10. ❓ How do we use JavaScript expressions inside JSX?

**Answer:**
Wrap any JS variable, function, or expression inside `{}`.

**Example:**

```jsx
const name = "Rana";
const year = 2025;

return <h1>Hello {name}, Welcome to {year}!</h1>;
```

---

### 11. ❓ What happens behind the scenes when JSX is compiled?

**Answer:**
JSX is **transpiled by Babel** into `React.createElement()` calls.

**Example:**

```jsx
const element = <h1>Hello</h1>;
```

is converted into:

```js
const element = React.createElement("h1", null, "Hello");
```

This creates a **virtual DOM object**, not real HTML.

---

### 12. ❓ What is the return type of a functional component?

**Answer:**
It returns a **React element (JSX)**, which represents the **UI structure** that React will render.

---

### 13. ❓ Why are functional components preferred over class components?

**Answer:**

* Simpler syntax
* Easier to test
* Better performance
* Support **Hooks**
* Less boilerplate code

---