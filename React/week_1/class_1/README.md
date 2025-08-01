# React + Vite Fundamentals 🚀

This document summarizes the core concepts and tools we've learned so far while working with **React.js** and **Vite**. It serves as a solid foundation for beginners aiming to become advanced React developers.

---

## 📘 What is React?

- **React** is a JavaScript library developed by **Facebook** in **2011** and made open-source in **2013**.
- Created by **Jordan Walke**, a software engineer at Facebook.
- React is used for building **User Interfaces (UI)**, especially for **Single Page Applications (SPA)**.
- It promotes building applications using **reusable components**.

---

## ⚙️ What is Vite?

- **Vite** is a fast modern frontend build tool developed by **Evan You** (creator of Vue.js).
- It serves as a faster alternative to Create React App (CRA).
- Vite uses **ESBuild** for development and **Rollup** for production bundling.

### 🔥 Why Vite over CRA?

| Feature          | CRA (Create React App) | Vite              |
|------------------|------------------------|-------------------|
| Startup Speed    | Slow                   | Instant           |
| Build Time       | Long                   | Fast              |
| Underlying Tool  | Webpack                | ESBuild + Rollup  |
| Modern Support   | Average                | Excellent         |

---

## 🧠 What is SPA (Single Page Application)?

- SPA is a web application that loads a single HTML page and dynamically updates content without refreshing the entire page.
- Navigation between pages is handled on the **client side** using **React Router** (later).

### ✅ Advantages of SPA:
- Faster performance
- Smooth user experience
- Efficient API usage (only content changes)

---

## 🧱 Real DOM vs Virtual DOM

| Aspect           | Real DOM                             | Virtual DOM                           |
|------------------|--------------------------------------|----------------------------------------|
| Definition       | The actual DOM in the browser        | A lightweight JS copy of the Real DOM |
| Update Process   | Entire DOM may re-render             | Only changed parts are updated        |
| Performance      | Slower for frequent changes          | Much faster and efficient             |

- React uses **Virtual DOM** to improve performance by minimizing direct manipulations to the Real DOM.

---

## 🧰 Bundlers Overview

Bundlers are tools that compile and optimize frontend assets like JS, CSS, and images.

### 1. Webpack
- Used by Create React App (CRA)
- Powerful but slow and complex
- Handles JS, CSS, images, SCSS, etc.

### 2. ESBuild
- Written in Go language
- Extremely fast (used by Vite for development)
- Transpiles modern JS

### 3. Rollup
- Used by Vite for production build
- Great for library bundling
- Smaller and optimized output

---

## 📂 How to Create a React + Vite App

```bash
npm create vite@latest my-app --template react
cd my-app
npm install
npm run dev
````

---

## ✅ Summary

* React is a component-based JavaScript library for building user interfaces.
* Vite is a modern and faster development tool compared to CRA.
* SPAs are web apps that behave like desktop apps with fast navigation.
* React uses Virtual DOM for better performance.
* Webpack, ESBuild, and Rollup are tools that handle the building and bundling of your app efficiently.

---

*This `README.md` will grow as we progress further through advanced topics like React Router, Context API, Redux, Custom Hooks, and more.*