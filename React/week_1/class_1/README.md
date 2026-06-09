# 📘 What is React? — React Introduction & Project Setup

## 📚 Topics Covered
- What is React and why use it
- Single Page Application (SPA) concept
- Virtual DOM vs Real DOM
- Create React App (CRA) vs Vite
- React Frameworks (Next.js, Remix, Gatsby)
- Setting up a project with Vite — step by step
- Project folder structure explained
- Component-based architecture

---

**React** is a **JavaScript library** created by **Facebook** in **2011** and made open-source in **2013**.
It was built by **Jordan Walke**, a software engineer at Facebook.

React helps developers build **User Interfaces (UI)** — especially **Single Page Applications (SPA)** — where only parts of the page update without refreshing the whole browser window.

### 🧩 Example:

Imagine you have a page showing a list of posts.
When you click “Like” on one post, React updates **only that post’s like count**, not the whole page.
That’s what makes it fast and efficient.

### ⚙️ Key Idea:

React apps are made up of **components** — small, reusable pieces of UI.
For example:

```jsx
function Button() {
  return <button>Click Me</button>;
}
```

This simple `Button` can be used anywhere in your app.

---

# ⚙️ How to Create a React Project

There are multiple ways to start a React project. Let’s discuss the main ones.

---

## 1️⃣ Using Create React App (Old Way)

**Command:**

```bash
npx create-react-app my-app
```

### 🔍 Notes:

* It uses **Webpack** internally for bundling your files.
* All configuration (like webpack setup) is hidden by default.
* It’s easy for beginners but **slow to start** for large apps.

---

## 2️⃣ Using Vite (Recommended Modern Way)

**Command:**

```bash
npm create vite@latest my-app --template react
```

or with pnpm:

```bash
pnpm create vite@latest my-app --template react
```

### 💡 Why Vite is better:

* It starts instantly — no waiting for the dev server.
* Uses **esbuild** (written in Go) which is extremely fast.
* Uses **Rollup** for production builds — optimized and small.
* Supports **modern JavaScript features** automatically.

So, for new projects → **Always prefer Vite.**

---

## 3️⃣ Using Frameworks (Full-Stack React)

If you want backend + frontend features or SEO support, use a **React framework**.

### 🔹 Common ones:

| Framework   | Main Feature                                                |
| ----------- | ----------------------------------------------------------- |
| **Next.js** | Server-side rendering (SSR), file-based routing, API routes |
| **Remix**   | Great for forms and data-driven apps                        |
| **Gatsby**  | Ideal for static websites                                   |
| **Razzle**  | Handles SSR for React apps                                  |

✅ Use these when you need things like routing, backend APIs, or SEO.

---

# ⚡ What is Vite?

**Vite** (created by **Evan You**, who also made Vue.js) is a **build tool and development server** that is **super fast**.

### How Vite Works:

* In **development mode**, Vite uses **esbuild** for fast file transformation.
* In **production**, it uses **Rollup** to create optimized bundles.

### 🧮 CRA vs Vite Comparison

| Feature          | CRA (Webpack) | Vite              |
| ---------------- | ------------- | ----------------- |
| Startup Time     | Slow          | Instant           |
| HMR (Hot Reload) | Slower        | Very fast         |
| Config           | Complex       | Simple            |
| Output           | Heavy         | Optimized & small |

So, Vite is like the **new generation** of React build tools.

---

# 🔧 ESBuild and Rollup

## 🏗️ ESBuild

* Built in **Go** (a very fast language).
* Converts modern JavaScript (ES6+) to browser-compatible code very quickly.
* Used by **Vite** in dev mode for instant updates.

## 📦 Rollup

* Used by Vite when you build for production.
* Focuses on **tree shaking** — removes unused code.
* Generates **optimized bundles** for faster websites.

---

# 🧐 Why Use React?

React is one of the **most popular** libraries because of:

1. **Reusable Components**

   * You build UI pieces once and use them anywhere.

   Example:

   ```jsx
   function Button({ text }) {
     return <button>{text}</button>;
   }

   <Button text="Submit" />
   <Button text="Cancel" />
   ```

2. **Declarative UI**

   * You tell React *what* the UI should look like, and React updates it automatically when data changes.

3. **Virtual DOM**

   * React doesn’t directly change the HTML; it works with a **virtual copy** of the DOM to update only what’s necessary — faster performance.

4. **JSX**

   * Allows writing HTML directly inside JavaScript.

     ```jsx
     const Greeting = ({ name }) => <h1>Hello, {name}</h1>;
     ```

5. **Huge Ecosystem**

   * Tools like React Router, Redux, TanStack Query, etc., make development easier.

6. **TypeScript Support**

   * You can use React with TypeScript for safer, bug-free code.

---

# 🧠 Virtual DOM vs Real DOM

## 🧩 Real DOM:

* Represents HTML elements directly.
* Updating the real DOM (like `document.getElementById()`) is **slow** when done many times.

## 💡 Virtual DOM:

* React creates a **virtual copy** of the DOM in memory.
* When state changes:

  1. React builds a **new virtual DOM**.
  2. Compares (diffs) it with the old one.
  3. Updates only the changed parts in the real DOM.

Example:

```jsx
setItems([...items, newItem]);
```

React adds only the new item to the list — not re-renders the whole list.

---

# 🛠️ Setting Up React Environment

## ✅ Requirements:

* **Node.js** (LTS version)
* **npm**, **yarn**, or **pnpm**

### Why Node.js?

Because it gives us:

* A runtime to execute JavaScript outside browsers.
* A package manager (npm) to install React, Vite, etc.

---

## 🚀 Steps to Create Project with Vite

```bash
npm create vite@latest my-app --template react
cd my-app
npm install
npm run dev
```

### Scripts in package.json:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

---

# 📂 Folder Structure Explanation

```
my-app/
├── node_modules/
├── public/
├── src/
│   ├── App.jsx
│   ├── main.jsx
│   ├── index.css
│   └── assets/
├── index.html
├── package.json
├── vite.config.js
└── README.md
```

---

## 📁 node_modules/

* Contains all dependencies your app uses.
* Automatically created when you run `npm install`.
* Never edit it manually.

---

## 📁 public/

* Stores static files like images or icons.
* Files here are copied as-is to the final build.
* Example: `public/logo.png` → accessible via `http://localhost:5173/logo.png`.

---

## 📁 src/

* Main folder for your React code.

Inside it:

```
src/
├── App.jsx
├── main.jsx
├── index.css
└── assets/
```

### 🔹 App.jsx

Main React component — your app starts here.

```jsx
function App() {
  return <h1>Hello React!</h1>;
}
export default App;
```

### 🔹 main.jsx

Entry point — connects `App` to the HTML page.

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### 🔹 index.html

Contains a `<div id="root"></div>` where React injects your app.

### 🔹 vite.config.js

Config file for Vite (plugins, paths, etc.)

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
export default defineConfig({
  plugins: [react()],
});
```

---

# 🧭 Component-Based Architecture

React apps are made up of **components** that build on each other.

Example:

```
components/
  └── Button.jsx
pages/
  └── Home.jsx
```

### Button.jsx

```jsx
export default function Button({ children, onClick }) {
  return <button onClick={onClick}>{children}</button>;
}
```

### Home.jsx

```jsx
import Button from '../components/Button';

export default function Home() {
  return (
    <div>
      <h1>Welcome</h1>
      <Button onClick={() => alert('Hi!')}>Click Me</Button>
    </div>
  );
}
```

You can mix small reusable components to build big pages.

---

# ✅ Summary Table

| Folder/File      | Purpose                          |
| ---------------- | -------------------------------- |
| `node_modules/`  | Stores all dependencies          |
| `public/`        | Static files like images         |
| `src/`           | Main React code                  |
| `App.jsx`        | Main component                   |
| `main.jsx`       | Entry point (renders App)        |
| `index.html`     | HTML file with root div          |
| `vite.config.js` | Vite configuration               |
| `package.json`   | Project scripts and dependencies |

---

## 🎯 Interview Questions

**Q1: What is React and what problem does it solve?**

> React is a JavaScript library for building user interfaces. It solves the problem of inefficient DOM updates by using a Virtual DOM that computes the minimal changes needed, making UI updates fast.

**Q2: What is a Single Page Application (SPA)?**

> An SPA loads one HTML page and dynamically updates the UI without full page reloads. React, Vue, and Angular are all commonly used for SPAs. Examples: Gmail, Twitter, Netflix.

**Q3: What is the Virtual DOM and how does it work?**

> The Virtual DOM is a lightweight JavaScript representation of the real DOM. When state changes, React creates a new Virtual DOM, diffs it against the previous one (reconciliation), and applies only the minimal required changes to the real DOM.

**Q4: What is the difference between CRA and Vite?**

> CRA uses Webpack which is slower to start. Vite uses ESBuild (written in Go) for near-instant dev server startup and Rollup for optimized production builds. Vite is the recommended choice for new projects.

**Q5: What is component-based architecture?**

> Breaking the UI into small, reusable, independent pieces called components. Each component manages its own logic and UI. They can be composed together to build complex interfaces.

---

## 🏠 Home Task

Build a **React app with Vite** that includes:
1. Create a new Vite React project from scratch
2. Create at least 4 components: `Navbar`, `Hero`, `About`, `Footer`
3. Import and use all components inside `App.jsx`
4. Add basic inline styles or a CSS file
5. Verify `node_modules`, `public/`, `src/`, `main.jsx` and `App.jsx` structure
6. Run `npm run dev` and explore the folder structure in your code editor