# 🌐 React Context API — Global State Without Prop Drilling

## 📚 Topics Covered
- Prop drilling problem — why it's painful
- Context API as the solution
- `createContext`, `Provider`, `useContext` — the three building blocks
- User data context example
- Theme context (light/dark mode)
- Auth context (login/logout state)
- Multiple contexts — nesting providers
- Combined provider pattern for cleaner code

---

## **1️⃣ What is Prop Drilling?**

In React, data is usually passed **parent → child** using **props**.
But when components are **deeply nested**, props need to be passed at every level.

👉 This problem is called **Prop Drilling**.

### **Example of Prop Drilling:**

```jsx
function App() {
  const user = "Rana";

  return <Parent user={user} />;
}

function Parent({ user }) {
  return <Child user={user} />;
}

function Child({ user }) {
  return <GrandChild user={user} />;
}

function GrandChild({ user }) {
  return <h2>Hello {user}</h2>;
}
```

⚠️ Here `user` is being passed down at every level → messy & hard to maintain.

---

## **2️⃣ Solution: Context API**

React **Context API** allows you to:

* Create a **global state** accessible to all components.
* No need to pass props manually at every level.
* Useful for managing themes, authentication, language, user info, etc.

---

## **3️⃣ Core Building Blocks**

### 🔹 `createContext`

* Creates a **Context object**.
* Example: `const ThemeContext = createContext();`

### 🔹 `Provider`

* A component that **provides values** (data, functions) to children.
* Example:

```jsx
<ThemeContext.Provider value={theme}>
  <App />
</ThemeContext.Provider>
```

### 🔹 `useContext`

* A hook used by child components to **consume values** from Context.
* Example:

```jsx
const theme = useContext(ThemeContext);
```

---

## **4️⃣ Context API Example (User Data Context)**

```jsx
// UserData.js
import { createContext, useState } from "react";

// 1. Create Context
export const UserContext = createContext();

// 2. Provider Component
export function UserDataProvider({ children }) {
  const [user, setUser] = useState({
    name: "Waqar Rana",
    contact: "0318",
    location: "Karachi",
    email: "waqar@gmail.com"
  });

  return (
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  );
}
```

```jsx
// App.jsx
import { UserDataProvider } from "./UserData";
import Home from "./Home";

export default function App() {
  return (
    <UserDataProvider>
      <Home />
    </UserDataProvider>
  );
}
```

```jsx
// Home.jsx
import { useContext } from "react";
import { UserContext } from "./UserData";

export default function Home() {
  const { user } = useContext(UserContext);

  return (
    <div>
      <h1>{user.name.toUpperCase()}</h1>
      <h2>{user.email.toUpperCase()}</h2>
    </div>
  );
}
```

## 🔹 Why we wrap with `<UserDataProvider>`

When you use **Context API**, the `Provider` defines **which part of the app tree** will have access to that context.

```jsx
<UserDataProvider>
  <Home />
</UserDataProvider>
```

This means:

* Everything **inside** `<UserDataProvider>` (like `<Home />`) can access the `userData` context.
* Anything **outside** of it cannot use that context.

So we wrap `<Home />` (or sometimes the **entire app**) with the provider to make sure all child components can consume the context.

---

## **5️⃣ Another Example: Theme Context (Light/Dark Mode)**

👉 Manage **Light/Dark Theme** globally using Context.

```jsx
// ThemeContext.js
import { createContext, useState } from "react";

// 1. Create Context
export const ThemeContext = createContext();

// 2. Provider Component
export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

```jsx
// App.jsx
import { ThemeProvider } from "./ThemeContext";
import ThemeSwitcher from "./ThemeSwitcher";

export default function App() {
  return (
    <ThemeProvider>
      <ThemeSwitcher />
    </ThemeProvider>
  );
}
```

```jsx
// ThemeSwitcher.jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

export default function ThemeSwitcher() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  const styles = {
    backgroundColor: theme === "light" ? "#fff" : "#333",
    color: theme === "light" ? "#000" : "#fff",
    padding: "20px",
    textAlign: "center"
  };

  return (
    <div style={styles}>
      <h1>{theme.toUpperCase()} MODE</h1>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}
```

---

## **6️⃣ Another Example: Auth Context**

👉 Manage global login state.

```jsx
// AuthContext.js
import { createContext, useState } from "react";

export const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (name) => setUser({ name });
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}
```

```jsx
// App.jsx
import { AuthProvider } from "./AuthContext";
import Profile from "./Profile";

export default function App() {
  return (
    <AuthProvider>
      <Profile />
    </AuthProvider>
  );
}
```

```jsx
// Profile.jsx
import { useContext } from "react";
import { AuthContext } from "./AuthContext";

export default function Profile() {
  const { user, login, logout } = useContext(AuthContext);

  return (
    <div>
      {user ? (
        <>
          <h2>Welcome {user.name}</h2>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <button onClick={() => login("Rana")}>Login</button>
      )}
    </div>
  );
}
```

---

## **📌 Summary**

* **Prop Drilling Problem** → Context API solves it.
* Use `createContext`, `Provider`, and `useContext`.
* Examples:

  * User Data Context
  * Theme Context (Light/Dark)
  * Auth Context

👉 Best for global states like authentication, theme, language, or cart in an e-commerce app.

---

## 🔹 What if we have **multiple contexts**?

Sometimes you’ll have more than one global state (e.g., `UserContext`, `ThemeContext`, `AuthContext`).
In that case, you need to **nest providers**.

### Example:

```jsx
import { UserDataProvider } from "./UserData";
import { ThemeProvider } from "./ThemeContext";
import { AuthProvider } from "./AuthContext";
import Home from "./Home";

export default function App() {
  return (
    <AuthProvider>
      <UserDataProvider>
        <ThemeProvider>
          <Home />
        </ThemeProvider>
      </UserDataProvider>
    </AuthProvider>
  );
}
```

Here:

* `AuthProvider` gives access to authentication state.
* `UserDataProvider` gives access to user info.
* `ThemeProvider` gives access to light/dark mode.

⚡ All three are available inside `<Home />`.

---

## 🔹 Cleaner way for multiple contexts

Nesting too many providers looks ugly. So we can create a **combined provider**:

```jsx
// AppProviders.jsx
import { AuthProvider } from "./AuthContext";
import { UserDataProvider } from "./UserData";
import { ThemeProvider } from "./ThemeContext";

export function AppProviders({ children }) {
  return (
    <AuthProvider>
      <UserDataProvider>
        <ThemeProvider>{children}</ThemeProvider>
      </UserDataProvider>
    </AuthProvider>
  );
}
```

Then in `App.jsx`:

```jsx
import { AppProviders } from "./AppProviders";
import Home from "./Home";

export default function App() {
  return (
    <AppProviders>
      <Home />
    </AppProviders>
  );
}
```

👉 Now it’s **cleaner** and still works the same.

---

## 🎯 Interview Questions

**Q1: What problem does the Context API solve?**

> Prop drilling — passing data through many intermediate components that don’t need it. Context provides a way to share data globally within a component tree without explicitly passing props at every level.

**Q2: What are the three main pieces of Context API?**

> 1. `createContext()` — creates the context object. 2. `Provider` — wraps the component tree and provides the value. 3. `useContext(MyContext)` — consumes the value in any descendant component.

**Q3: Does every component re-render when a Context value changes?**

> Yes — every component that calls `useContext(MyContext)` will re-render when the context value changes, even if the specific part of the value they use didn’t change. This is why large apps use multiple focused contexts or state management libraries like Redux/Zustand.

**Q4: When should you use Context API vs props?**

> Use props for data that’s specific to a parent-child relationship. Use Context for genuinely global data shared across many components: current user, theme, language, authentication state.

**Q5: Can you have multiple contexts in one app?**

> Yes. Each context has its own Provider and value. Nesting providers lets different contexts coexist. A common pattern is a single `AppProviders` component that nests all providers.

---

## 🏠 Home Task

Build a **Theme + Auth App** using Context API:
1. `ThemeContext` — light/dark mode, toggle button accessible from anywhere
2. `AuthContext` — `user` object, `login(name)`, `logout()` functions
3. `CartContext` — `items`, `addItem(product)`, `removeItem(id)`, `total` computed value
4. `Navbar` uses all three contexts: shows username, theme toggle, cart count
5. `ProductPage` — add to cart button (uses CartContext)
6. `ProfilePage` — shows user info, logout button (uses AuthContext)
7. All providers combined in `AppProviders.jsx`


