## 🔹 Controlled vs Uncontrolled Components

### 🔸 Controlled Component

In **controlled components**, **React state controls the form input**.

```jsx
function MyForm() {
  const [input, setInput] = useState("");

  return (
    <input
      type="text"
      value={input}
      onChange={(e) => setInput(e.target.value)}
    />
  );
}
```

🧠 The value of the input is tied to the component's state.

---

### 🔸 Uncontrolled Component

In **uncontrolled components**, the **DOM handles the value**, and you use `ref` to access it.

```jsx
import { useRef } from "react";

function MyForm() {
  const inputRef = useRef();

  const handleSubmit = () => {
    alert(inputRef.current.value);
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
}
```

🧠 React is not tracking the input value; the DOM is.

---

## 🔄 Key Differences: Controlled vs Uncontrolled

| Feature       | Controlled Component      | Uncontrolled Component      |
| ------------- | ------------------------- | --------------------------- |
| Value Source  | React state (`useState`)  | DOM via `ref`               |
| React Control | Full control              | No control                  |
| Validation    | Easy                      | Manual                      |
| State Sync    | Always synced             | Not automatically synced    |
| Use Case      | Modern forms, validations | Simple, legacy, quick forms |
| Initial Value | `useState("default")`     | `defaultValue="default"`    |

---

## 📌 When to Use Which?

### ✅ Use Controlled:

* For dynamic forms.
* When validation or interactivity is needed.
* For full control and predictability.

### ❌ Use Uncontrolled:

* For simple/quick forms.
* When integrating with third-party DOM libraries.
* When minimal re-rendering is preferred.

---

## 🤔 Summary

* **Controlled** = React is in charge (uses state).
* **Uncontrolled** = DOM is in charge (uses refs).
* ✅ Controlled components are **recommended** for most use cases.

---

### TODO APP (Simple)

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
    if (!input) return alert("Todo is required");
    const copyTodoList = [...todos];
    copyTodoList.push(input);
    setTodos(copyTodoList);
    setInput('');
  };

  const removeTodo = (index) => {
    const copyTodoList = [...todos];
    copyTodoList.splice(index, 1);
    setTodos(copyTodoList);
  };

  return (
    <div>
      <input value={input} onChange={getInputValue} placeholder="New todo" />
      <button onClick={addTodo}>Add</button>
      <ul>
        {todos.map((t, i) => (
          <li key={i}>
            {t} <button onClick={() => removeTodo(i)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## 🔹 What is a Key in React?

In React, a **key** is a special attribute used to give **unique identity** to each element in a list.

### 📌 Why Keys Are Important

* Keys help React **identify which items have changed, been added, or removed**.
* They make list rendering **efficient** by allowing React to update **only the changed elements** instead of re-rendering the entire list.

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
2. **Keys are only used by React internally.**

   * They are **not accessible via `props`** inside the child component.
3. **Use keys in lists or arrays of elements.**

   * You **don’t need a key** for single components.

---

### ⚠️ Using Index as Key

```jsx
<li key={index}>{product.name}</li>
```

* This works but is **not recommended** for dynamic lists (adding/removing/reordering items) because it can cause **unexpected UI behavior**.
* Prefer a **stable, unique ID**.

---

### 🧠 Quick Analogy

> Keys are like **roll numbers in a classroom**:
> If a new student joins or one leaves, the teacher uses roll numbers to identify students without confusing them.