# 🗺️ Routing in React — React Router DOM

## 📚 Topics Covered
- What is routing — client-side vs server-side
- `react-router-dom` installation and setup
- `BrowserRouter`, `Routes`, `Route` components
- `Link` component — navigation without page reload
- Dynamic routes with `useParams`
- Nested routing and `Outlet`
- `createBrowserRouter` — modern configuration-based routing
- `RouterProvider`
- Project: Product list with dynamic product detail pages

---

## **1️⃣ What is Routing?**

Routing is the process of **navigating between different pages or views** in an application.

* **Traditional Websites** → Each click requests a **new HTML page** from the server.
* **React (SPA – Single Page Application)** → Routing allows you to switch between **components** instead of reloading the entire page.
  This makes apps **faster, smoother, and more interactive**.

### Example Routes

* `/` → Home Page
* `/about` → About Page
* `/contact` → Contact Page

---

## **2️⃣ Types of Routing**

### (A) Based on Handling

* **Client-side Routing**

  * Happens inside the browser.
  * React Router DOM updates the component without page reload.
  * **Faster and smoother user experience**.

* **Server-side Routing**

  * Each request goes to the server.
  * Server sends a new HTML page.
  * **Slower** because the whole page reloads.

---

### (B) Based on Path

* **Static Routing**

  * Fixed path → Always shows the same component.

  ```jsx
  <Route path="/about" element={<About />} />
  <Route path="/contact" element={<Contact />} />
  ```

  * `/about` → About Page
  * `/contact` → Contact Page

* **Dynamic Routing**

  * Path contains **parameters** (`:id`, `:slug`).
  * Used for profiles, product details, blogs, etc.

  ```jsx
  <Route path="/user/:id" element={<User />} />
  ```

  * `/user/1` → User 1 Page
  * `/user/2` → User 2 Page

---

## **3️⃣ React Router DOM**

React itself does not have routing built in.
👉 We use a library called **React Router DOM** to:

* Define routes (`/`, `/about`, `/user/1`)
* Navigate with `<Link>` instead of `<a>` (no reload)
* Handle **dynamic routes** with `useParams`
* Create **nested routes** for dashboards or layouts

---

## **4️⃣ Installation**

Install React Router DOM with:

```bash
npm install react-router-dom
```

---

## **5️⃣ Core Concepts in React Router**

### 🔹 `BrowserRouter`

`BrowserRouter` is the **wrapper** that enables routing in your app. It listens to URL changes in the browser and renders the corresponding component.

Example:

```jsx
import { BrowserRouter } from "react-router-dom";
import App from "./App";

function Root() {
  return (
    <BrowserRouter>
      <App />
    </BrowserRouter>
  );
}

export default Root;
```

> Always wrap your app (or at least the part with routes) in `BrowserRouter`.

---

### 🔹 `Routes` and `Route`

`Routes` acts like a container for **all your routes**, and `Route` defines **each path and its component**.

```jsx
import { Routes, Route } from "react-router-dom";
import Home from "./Home";
import About from "./About";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  );
}
```

✅ **Explanation:**

* `path` → The URL path.
* `element` → The component to render.
* `/` → Home page.
* `/about` → About page.

---

### 🔹 `Link` (Navigation without Reload)

Instead of `<a>`, use `<Link>` to navigate.
It changes the URL and renders the route **without refreshing** the page.

```jsx
import { Link } from "react-router-dom";

function Navbar() {
  return (
    <nav>
      <Link to="/">Home</Link> | <Link to="/about">About</Link>
    </nav>
  );
}
```
> Clicking a `<Link>` updates the URL **and renders the component** instantly.

---

### 🔹 Dynamic Routes with `useParams`

Sometimes URLs have **dynamic parts**, like:

```
/user/1
/user/2
```

We use **dynamic parameters**:

```jsx
<Route path="/user/:id" element={<User />} />
```

* `:id` → Dynamic parameter
* `useParams` → Hook to access that value

Component:

```jsx
import { useParams } from "react-router-dom";

function User() {
  const { id } = useParams(); // get the value of :id
  return <h1>User ID: {id}</h1>;
}
```

Example:

* URL: `/user/5` → Renders `User ID: 5`
* URL: `/user/10` → Renders `User ID: 10`

---

### 🔹 Nested Routing

Nested routing means **routes inside another route**.
Useful for dashboards, settings, or layouts.

#### Step 1: Define Parent + Child Routes

```jsx
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    <Route path="profile" element={<Profile />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

#### Step 2: Parent with `<Outlet />`

`<Outlet />` is where child components will render.

```jsx
import { Link, Outlet } from "react-router-dom";

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <Link to="profile">Profile</Link> | <Link to="settings">Settings</Link>
      </nav>
      <Outlet />
    </div>
  );
}
```

#### Step 3: Child Components

```jsx
function Profile() {
  return <h2>Profile Page</h2>;
}

function Settings() {
  return <h2>Settings Page</h2>;
}
```

✅ `/dashboard/profile` → Renders Profile inside Dashboard

---

### 🔹 Passing Props with Outlet Context

Parent (Dashboard):

```jsx
<Outlet context={{ user }} />
```

Child (Profile):

```jsx
import { useOutletContext } from "react-router-dom";

function Profile() {
  const { user } = useOutletContext();
  return <p>Name: {user.name}</p>;
}
```

---

## **6️⃣ New Way: `createBrowserRouter`**

Earlier (Old Way):

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<App />} />
    <Route path="/about" element={<About />} />
  </Routes>
</BrowserRouter>
```

New Way:

```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Blog from "./Blog";
import About from "./About";
import Contact from "./Contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Blog />,
  },
  {
    path: "about",
    element: <About />,
  },
  {
    path: "contact",
    element: <Contact />,
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

## 🔸 Example with Nested Route

```jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Dashboard from "./Dashboard";
import Profile from "./Profile";
import Settings from "./Settings";

const router = createBrowserRouter([
  {
    path: "dashboard",
    element: <Dashboard />,
    children: [
      { path: "profile", element: <Profile /> },
      { path: "settings", element: <Settings /> },
    ],
  },
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

👉 Ab `/dashboard/profile` aur `/dashboard/settings` automatically `Dashboard` ke andar render honge (nested routing).

---

### ✅ Benefits of `createBrowserRouter`

* Cleaner, centralized config
* Supports **nested routes** easily
* Has **loaders** (fetch data before load)
* Has **actions** (handle form submissions)
* Per-route **error handling**

---

## **7️⃣ Summary Table**

| Concept               | What it does                              |
| --------------------- | ----------------------------------------- |
| `BrowserRouter`       | Wraps the app for routing                 |
| `Routes`              | Container for all routes                  |
| `Route`               | Defines path + component                  |
| `Link`                | Navigate without reloading the page       |
| `useParams`           | Get dynamic URL parameters                |
| `Nested Routes`       | Routes inside routes (layouts/dashboards) |
| `createBrowserRouter` | Newer, config-based routing approach      |

---

✅ **Key Tips for Students:**

1. Always wrap your app with `BrowserRouter` (or `RouterProvider` in new way).
2. Use `Link` instead of `<a>` for internal navigation.
3. Use **static routes** for fixed pages, **dynamic routes** for profiles/products.
4. Nested routing is great for dashboards.
5. Prefer `createBrowserRouter` for bigger apps.

---

## **Product List + Dynamic Routing**

```js
// App.jsx
import { BrowserRouter, Routes, Route, Link, useParams } from "react-router-dom";
import { useEffect, useState } from "react";

// Home Component - Show Products
function Home() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    fetch("https://fakestoreapi.com/products")
      .then((res) => res.json())
      .then((data) => setProducts(data));
  }, []);

  return (
    <div>
      <h1>All Products</h1>
      {products.map((p) => (
        <div key={p.id}>
          <h3>{p.title}</h3>
          <img src={p.image} alt={p.title} width="100" />
          <p>${p.price}</p>
          <Link to={`/product/${p.id}`}>View Details</Link>
          <hr />
        </div>
      ))}
    </div>
  );
}

// Product Detail Page
function ProductDetail() {
  const { id } = useParams();
  const [product, setProduct] = useState(null);

  useEffect(() => {
    fetch(`https://fakestoreapi.com/products/${id}`)
      .then((res) => res.json())
      .then((data) => setProduct(data));
  }, [id]);

  if (!product) return <h2>Loading...</h2>;

  return (
    <div>
      <h2>{product.title}</h2>
      <img src={product.image} alt={product.title} width="200" />
      <p><b>Price:</b> ${product.price}</p>
      <p>{product.description}</p>
      <p><b>Category:</b> {product.category}</p>
      <Link to="/">Back to Products</Link>
    </div>
  );
}

// Main App
export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/product/:id" element={<ProductDetail />} />
      </Routes>
    </BrowserRouter>
  );
}
```


## **Home Task for Students 📝**

1. Create a **Navbar** with 3 links: `Home`, `About`, `Products`.
2. Show **Products** list on `/products` using Fake Store API.
3. When user clicks a product, open its **detail page** dynamically (`/products/:id`).
4. Add a **Back button** on detail page.
5. Bonus 🌟: Implement **Category Filter** (Men, Women, Electronics, etc).

---

## 🎯 Interview Questions

**Q1: What is client-side routing?**

> In client-side routing, the browser handles navigation without making a new request to the server. JavaScript intercepts the URL change and renders the correct component. This makes transitions instant — no full page reloads.

**Q2: What is the difference between `Link` and an `<a>` tag in React?**

> `<a href>` causes a full page reload, losing all React state. `Link` from React Router changes the URL and renders the new component client-side — without any page reload or state loss.

**Q3: What is `useParams` and when do you use it?**

> `useParams` returns an object containing the dynamic segments of the current URL. Used when you have a route like `/products/:id` — calling `useParams()` gives `{ id: "42" }` so you can fetch the right data.

**Q4: What is the difference between `BrowserRouter` and `createBrowserRouter`?**

> `BrowserRouter` wraps JSX routes declaratively. `createBrowserRouter` (React Router v6.4+) takes a configuration array and supports advanced features like data loaders, actions, and error elements. It's the recommended modern approach.

**Q5: What is an `Outlet` in React Router?**

> `Outlet` is a placeholder in a parent route's component where child route components are rendered. It's what makes nested routing work — the parent layout stays rendered while the child content changes.

---