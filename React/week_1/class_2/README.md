# 🚀 Setup React with Vite

This guide explains how to set up a modern React application using **Vite**, a fast frontend build tool. We'll cover prerequisites, installation, project structure, and Vite config basics.

---

## 🧰 Prerequisites

Before getting started, make sure you have the following installed:

- [Node.js (LTS version)](https://nodejs.org/)
  - You can verify by running:
    ```bash
    node -v
    npm -v
    ```

---

## ⚙️ Create a React App using Vite

Follow the steps below to create a new React + Vite project:

### 1️⃣ Create a Vite App

```bash
# 1. Create a Vite project
npm create vite@latest

# Follow the prompt:
# ✔ Project name: » my-app
# ✔ Select a framework: » React
# ✔ Select a variant: » JavaScript (or TypeScript if preferred)
```

### 2️⃣ Navigate to the Project Folder

```bash
cd my-app
```

### 3️⃣ Install Dependencies

```bash
npm install
```

### 4️⃣ Start the Development Server

```bash
npm run dev
```

Your project will be running at [http://localhost:5173](http://localhost:5173)

---

## 📂 Default Project Structure

After creating the app, your folder will look like this:

```
my-app/
├── public/              # Static files (e.g., favicon)
├── src/                 # Source files (JSX, CSS)
│   ├── App.jsx          # Main app component
│   ├── main.jsx         # Entry point
│   └── index.css        # Global CSS
├── .gitignore           # Git ignore rules
├── index.html           # Root HTML file
├── package.json         # Project metadata & dependencies
├── vite.config.js       # Vite configuration
└── README.md            # Project documentation
```

---

## ⚙️ Vite Config Basics (`vite.config.js`)

Here’s what a default `vite.config.js` looks like:

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
});
```

### 🔍 What this does:

* `defineConfig`: Helps with type hints and cleaner config
* `@vitejs/plugin-react`: Adds support for React, JSX, and fast refresh

---

## 🧠 Why Vite?

* ⚡ Super fast hot reload and build
* 🛠️ Minimal and easy configuration
* 📦 Uses **ESBuild** for dev server and **Rollup** for production
* 🧹 Clean folder structure
* 🚀 Ideal for modern React projects

---

## ✅ You're Ready!

Now you're ready to build modern React apps with blazing speed and better performance using **Vite**. 🎉

Want to add routing, state management, or API fetching next? Stay tuned!

---

*This setup guide is the first step in mastering React + Vite. More advanced concepts like routing, state management, and deployment will follow in future lessons.*

```

---
---
# React + Vite Project Folder Structure Explained

## 📁 Root Directory

```

my-app/
├── node\_modules/
├── public/
├── src/
├── .gitignore
├── index.html
├── package.json
├── vite.config.js
├── README.md

```

---

## 📁 node_modules/

- Automatically created when you run `npm install`.
- Contains all installed dependencies and libraries your project needs to run.
- You should **never edit files in this folder manually**.
- It's usually ignored in version control via `.gitignore`.

---

## 📁 public/

- Static assets like images, favicons, or other files you want available as-is.
- Files here are copied as-is to the final build.
- Example: `public/logo.png` will be available at `http://localhost:5173/logo.png`.

---

## 📁 src/

This is the main source code directory where you write your React code.

```

src/
├── assets/
├── App.css
├── App.jsx
├── index.css
├── main.jsx

````

### 📁 assets/

- Place images, SVGs, and other static files used in your components here.

### 📄 App.jsx

- This is the main App component.
- It acts as the **root component** where other components are imported and rendered.

### 📄 App.css

- Contains the styling for the App component.

### 📄 index.css

- Global styles for the whole app (body, html, etc.).

### 📄 main.jsx

- This is the **entry point** for the React app.
- It renders the `App` component inside the root HTML element in `index.html`.
- It typically looks like this:

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
````

---

## 📄 .gitignore

* Specifies which files/folders Git should ignore (like `node_modules`, `.env`, etc.)

---

## 📄 index.html

* Located at the root level, **not inside `public/` like in CRA**.
* Has a `div` with the `id="root"` where your React app gets injected.
* Vite injects scripts here during build.

---

## 📄 package.json

* Lists all dependencies, scripts, and metadata for the project.
* Important scripts:

  * `dev`: Runs the dev server (`vite`)
  * `build`: Builds the app for production
  * `preview`: Previews the built app locally

---

## 📄 vite.config.js

* Configuration file for Vite.
* You can customize aliases, plugins, and build options here.

Example:

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
});
```

---

## 📄 README.md

* A markdown file for describing the project.
* Great place to explain features, how to run, and setup instructions.

---

## ✅ Summary

| Folder/File    | Purpose                                  |
| -------------- | ---------------------------------------- |
| node\_modules/ | Dependencies                             |
| public/        | Static files (served as-is)              |
| src/           | Your actual React app code               |
| App.jsx        | Main React component                     |
| main.jsx       | Entry point, renders App into index.html |
| index.html     | Root HTML file                           |
| vite.config.js | Vite-specific configuration              |
| package.json   | Project metadata and dependencies        |

---

> 🧠 As your app grows, you can organize `src/` into more folders like `components/`, `pages/`, `hooks/`, `context/`, etc.
