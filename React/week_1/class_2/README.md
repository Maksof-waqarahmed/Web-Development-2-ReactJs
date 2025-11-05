# 💎 JSX & Functional Components — The Foundation of React

## 🧩 What is JSX?

**JSX** stands for **JavaScript XML**.
It’s a **syntax extension** for JavaScript that allows you to write **HTML-like code** inside your JS files.

But remember — **JSX is not HTML**, it just **looks like HTML**.
Behind the scenes, React uses **Babel** (a JavaScript compiler) to convert JSX into **React.createElement()** calls.

### ⚙️ How it works internally:

```jsx
const element = <h1>Hello World</h1>;
```

👉 Behind the scenes, this becomes:

```js
const element = React.createElement('h1', null, 'Hello World');
```

So JSX is basically a **shortcut syntax** for writing React elements in a clean and readable way.

---

## 🎯 Why JSX is Powerful

✅ It makes UI code **more readable** and **intuitive**
✅ It **combines logic + UI** together in one place
✅ It allows **embedding JavaScript** directly inside markup using `{}`

### Example:

```jsx
const name = "Rana";
const age = 22;

const element = (
  <div>
    <h1>Hello {name}</h1>
    <p>You are {age} years old</p>
  </div>
);
```

Output 👇
**Hello Rana**
**You are 22 years old**

---

## ⚙️ JSX Rules (Important)

1. **Single Parent Element Rule**

   * Your component must return **only one parent element**.
   * Wrap everything inside `<div>` or a fragment `<>...</>`.

   ✅ Correct:

   ```jsx
   return (
     <div>
       <h1>Hello</h1>
       <p>Welcome to React</p>
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

2. **Use `className` Instead of `class`**

   * `class` is a reserved JavaScript keyword.

   ```jsx
   <h1 className="title">Hello</h1>
   ```

3. **All Tags Must Be Closed**

   ```jsx
   <img src="logo.png" alt="Logo" />  ✅
   <input type="text" /> ✅
   ```

4. **Embed JavaScript Using `{}`**

   * You can use variables, expressions, and even function calls.

   ```jsx
   const getTime = () => new Date().toLocaleTimeString();
   return <h2>The time is {getTime()}</h2>;
   ```

---

## 🧱 Class Components (Old Way)

Before React Hooks were introduced, we used **class components**.
They are still valid, but less common now.

Example:

```jsx
import React, { Component } from 'react';

class Counter extends Component {
  constructor() {
    super();
    this.state = { count: 0 };
  }

  increment = () => this.setState({ count: this.state.count + 1 });

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

### 🧠 Notes:

* Uses `this.state` for managing data
* Uses lifecycle methods like `componentDidMount`
* Longer syntax, harder to read

---

## ⚡ Functional Components (Modern Way)

After **React Hooks (2019)**, functional components became standard.
They are **simpler**, **shorter**, and **more powerful**.

Example:

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}

export default Counter;
```

### ✅ Advantages:

* Clean and readable
* Easy to test and maintain
* Uses **Hooks** for features like state and lifecycle
* Better performance and easier debugging

---

## ⚙️ Functional Component Syntax

### 🔹 Regular Function

```jsx
function Greeting() {
  return <h1>Hello, React!</h1>;
}
```

### 🔹 Arrow Function

```jsx
const Greeting = () => <h1>Hello, React!</h1>;
```

You can use these components **multiple times**:

```jsx
<Greeting />
<Greeting />
```

---

## 📦 Exporting and Importing Components

React follows **modular architecture**.
Each component lives in its own file and can be **imported** where needed.

---

### 🔹 1. Default Export

**Header.jsx**

```jsx
const Header = () => {
  return <h1>This is the Header</h1>;
};

export default Header; // Only one default export per file
```

**App.jsx**

```jsx
import Header from './Header';

function App() {
  return <Header />;
}
```

📘 Note:
You can only have **one default export** per file, because it doesn’t require `{}` during import.

---

### 🔹 2. Named Export

**Header.jsx**

```jsx
export const Header = () => {
  return <h1>This is the Header</h1>;
};

export const Footer = () => {
  return <p>This is the Footer</p>;
};
```

**App.jsx**

```jsx
import { Header, Footer } from './Header';

function App() {
  return (
    <>
      <Header />
      <Footer />
    </>
  );
}
```

📘 Note:

* Named exports must be imported **with the same name** and inside `{}`.
* You can have **multiple named exports** in one file.

---

## 🧪 Hands-on Practice

build a **Simple Portfolio Landing Page** using components.

---