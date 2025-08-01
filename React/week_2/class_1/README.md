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


### Conditional Rendering

```jsx
import { useState } from 'react';

export default function App() {
  const [off, setOff] = useState(true);

  const toggleBulb = () => {
    setOff(prev => !prev);
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








### Simple User Info Display Card

```jsx
import React from "react";

// ✅ Sample user object
const userInfo = {
  name: "John Doe",
  email: "john.doe@example.com",
  age: 30,
  isAdmin: true,
  destination: "New York",
  phone: "+1234567890",
  address: "123 Main St, Anytown, USA",
  city: "Anytown",
  state: "CA",
  zip: "12345",
  country: "USA",
};

export default function App() {
  return (
    <div>
      <CardComponent userInfo={userInfo} />
    </div>
  );
}

// ✅ Card Component
export function CardComponent({ userInfo }) {
  return (
    <div className="card-component" style={cardStyle}>
      <h1>{userInfo.name}</h1>
      <p>Email: {userInfo.email}</p>
      <p>Phone: {userInfo.phone}</p>
      <p>Address: {userInfo.address}</p>
      <p>City: {userInfo.city}</p>
      <p>State: {userInfo.state}</p>
      <p>ZIP: {userInfo.zip}</p>
      <p>Country: {userInfo.country}</p>
    </div>
  );
}

// ✅ Optional Inline Style (for demo)
const cardStyle = {
  border: "1px solid #ccc",
  padding: "20px",
  width: "300px",
  borderRadius: "10px",
  background: "#f9f9f9",
};
```


### Products List

```jsx
import React from "react";

const products = [
  {
    id: 1,
    name: "Wireless Earbuds",
    price: 1999,
    quantity: 50,
    category: "Electronics",
    description: "Noise-cancelling wireless earbuds with long battery life and touch controls.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 2,
    name: "Smartwatch Pro",
    price: 4999,
    quantity: 30,
    category: "Wearables",
    description: "Fitness tracking smartwatch with heart rate monitor, GPS, and customizable watch faces.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 3,
    name: "Gaming Laptop",
    price: 89999,
    quantity: 15,
    category: "Computers",
    description: "High-performance gaming laptop with RTX GPU, 16GB RAM, and 1TB SSD.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 4,
    name: "Leather Wallet",
    price: 1299,
    quantity: 100,
    category: "Accessories",
    description: "Genuine leather slim wallet with multiple card slots and coin pocket.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 5,
    name: "Bluetooth Speaker",
    price: 2499,
    quantity: 60,
    category: "Audio",
    description: "Portable waterproof Bluetooth speaker with deep bass and 12-hour playback.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 6,
    name: "Yoga Mat",
    price: 899,
    quantity: 200,
    category: "Fitness",
    description: "Eco-friendly non-slip yoga mat with cushioning for joint support.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 7,
    name: "Organic Skincare Set",
    price: 3599,
    quantity: 80,
    category: "Beauty",
    description: "Daily skincare set with cleanser, toner, moisturizer made from natural ingredients.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 8,
    name: "Wireless Charger",
    price: 1499,
    quantity: 120,
    category: "Accessories",
    description: "Fast wireless charging pad compatible with all Qi-enabled phones.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 9,
    name: "Kids’ Backpack",
    price: 2199,
    quantity: 90,
    category: "Bags",
    description: "Durable and colorful backpack with multiple compartments for school kids.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  },
  {
    id: 10,
    name: "Stainless Steel Water Bottle",
    price: 799,
    quantity: 150,
    category: "Home & Kitchen",
    description: "Insulated water bottle keeps drinks cold for 24h or hot for 12h.",
    image: "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500"
  }
];

export default function App() {
  return (
    <div>
      <h1>Products List</h1>
      <div style={{ display: "flex", flexWrap: "wrap" }}>
        {products.map(product => (
          <CardComponent key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
}

export function CardComponent({ product }) {
  const { name, price, description, image, category, quantity } = product;
  return (
    <div
      className="card-component"
      style={{
        border: "1px solid #ccc",
        padding: "15px",
        margin: "10px",
        width: "220px",
        borderRadius: "8px",
        boxShadow: "0 2px 6px rgba(0,0,0,0.1)",
        background: "#fff",
        display: "flex",
        flexDirection: "column",
        gap: "8px"
      }}
    >
      <img
        src={image}
        alt={name}
        style={{ width: "100%", height: "120px", objectFit: "cover", borderRadius: "4px" }}
      />
      <h2 style={{ margin: 0, fontSize: "1.1rem" }}>{name}</h2>
      <p style={{ margin: 0, fontSize: "0.9rem" }}>{description}</p>
      <p style={{ margin: 0, fontWeight: "bold" }}>Price: ₹{price}</p>
      <p style={{ margin: 0 }}>Category: {category}</p>
      <p style={{ margin: 0 }}>In stock: {quantity}</p>
    </div>
  );
}
```

Here’s a **concise and professional explanation** of the **`key` concept** for your `README.md` in **clear English**:

---

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

---



```jsx
import React, { useState } from "react";

// Sample products
const products = [
  {
    id: 1,
    name: "Wireless Earbuds",
    price: 1999,
    category: "Electronics",
    description:
      "Noise-cancelling wireless earbuds with long battery life and touch controls.",
    image:
      "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500",
  },
  {
    id: 2,
    name: "Smartwatch Pro",
    price: 4999,
    category: "Wearables",
    description:
      "Fitness tracking smartwatch with heart rate monitor, GPS, and customizable watch faces.",
    image:
      "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500",
  },
  {
    id: 3,
    name: "Gaming Laptop",
    price: 89999,
    category: "Computers",
    description:
      "High-performance gaming laptop with RTX GPU, 16GB RAM, and 1TB SSD.",
    image:
      "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500",
  },
  {
    id: 4,
    name: "Leather Wallet",
    price: 1299,
    category: "Accessories",
    description:
      "Genuine leather slim wallet with multiple card slots and coin pocket.",
    image:
      "https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500",
  },
];

// Category filter component (form/menu)
function CategoryFilter({ categories, selected, onChange }) {
  return (
    <div style={{ marginBottom: 16 }}>
      <label style={{ marginRight: 8, fontWeight: "bold" }}>Filter by category:</label>
      <select value={selected} onChange={(e) => onChange(e.target.value)}>
        <option value="">All</option>
        {categories.map((c) => (
          <option key={c} value={c}>
            {c}
          </option>
        ))}
      </select>
    </div>
  );
}

// Product card component
function CardComponent({ product, onAddToCart }) {
  const { name, price, description, image, category } = product;
  return (
    <div
      style={{
        border: "1px solid #ccc",
        padding: "12px",
        borderRadius: 8,
        width: 220,
        margin: 8,
        boxShadow: "0 2px 6px rgba(0,0,0,0.1)",
        display: "flex",
        flexDirection: "column",
        gap: 6,
        background: "#fff",
      }}
    >
      <img
        src={image}
        alt={name}
        style={{
          width: "100%",
          height: 100,
          objectFit: "cover",
          borderRadius: 4,
        }}
      />
      <h3 style={{ margin: 0, fontSize: "1rem" }}>{name}</h3>
      <p style={{ margin: 0, fontSize: "0.85rem" }}>{description}</p>
      <p style={{ margin: 0, fontWeight: "bold" }}>₹{price}</p>
      <p style={{ margin: 0, fontSize: "0.75rem" }}>Category: {category}</p>
      <button
        onClick={() => onAddToCart(product)}
        style={{
          marginTop: "auto",
          padding: "8px",
          border: "none",
          borderRadius: 6,
          background: "#0070f3",
          color: "#fff",
          cursor: "pointer",
        }}
      >
        Add to Cart
      </button>
    </div>
  );
}

// Modal component (simple)
function Modal({ open, onClose, cartItem }) {
  if (!open) return null;
  return (
    <div
      style={{
        position: "fixed",
        inset: 0,
        background: "rgba(0,0,0,0.4)",
        display: "flex",
        justifyContent: "center",
        alignItems: "center",
        padding: 20,
        zIndex: 100,
      }}
    >
      <div
        style={{
          background: "#fff",
          borderRadius: 10,
          padding: 20,
          width: 300,
          boxShadow: "0 4px 12px rgba(0,0,0,0.2)",
          position: "relative",
        }}
      >
        <button
          onClick={onClose}
          style={{
            position: "absolute",
            top: 8,
            right: 8,
            border: "none",
            background: "transparent",
            fontSize: 16,
            cursor: "pointer",
          }}
        >
          ×
        </button>
        <h2 style={{ marginTop: 0 }}>Added to Cart</h2>
        {cartItem ? (
          <div style={{ display: "flex", gap: 10 }}>
            <img
              src={cartItem.image}
              alt={cartItem.name}
              style={{ width: 60, height: 60, objectFit: "cover", borderRadius: 4 }}
            />
            <div>
              <p style={{ margin: "4px 0", fontWeight: "bold" }}>{cartItem.name}</p>
              <p style={{ margin: "4px 0" }}>₹{cartItem.price}</p>
              <p style={{ margin: "4px 0", fontSize: "0.85rem" }}>
                Category: {cartItem.category}
              </p>
            </div>
          </div>
        ) : (
          <p>No item</p>
        )}
        <div style={{ marginTop: 16, textAlign: "right" }}>
          <button
            onClick={onClose}
            style={{
              padding: "8px 12px",
              border: "none",
              borderRadius: 6,
              background: "#555",
              color: "#fff",
              cursor: "pointer",
            }}
          >
            Close
          </button>
        </div>
      </div>
    </div>
  );
}

// Main App
export default function App() {
  const [selectedCategory, setSelectedCategory] = useState("");
  const [modalOpen, setModalOpen] = useState(false);
  const [lastAdded, setLastAdded] = useState(null);

  // derive categories from products
  const categories = Array.from(new Set(products.map((p) => p.category)));

  const filtered = selectedCategory
    ? products.filter((p) => p.category === selectedCategory)
    : products;

  const handleAddToCart = (product) => {
    setLastAdded(product);
    setModalOpen(true);
  };

  return (
    <div style={{ padding: 20, fontFamily: "system-ui, sans-serif" }}>
      <h1>Products List</h1>

      <CategoryFilter
        categories={categories}
        selected={selectedCategory}
        onChange={setSelectedCategory}
      />

      <div style={{ display: "flex", flexWrap: "wrap" }}>
        {filtered.map((product) => (
          <CardComponent
            key={product.id}
            product={product}
            onAddToCart={handleAddToCart}
          />
        ))}
      </div>

      <Modal
        open={modalOpen}
        cartItem={lastAdded}
        onClose={() => setModalOpen(false)}
      />
    </div>
  );
}
```