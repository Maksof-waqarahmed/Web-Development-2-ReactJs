# 🗃️ State Management in Next.js (Zustand & Context)

## 📚 Topics Covered
- State management patterns in Next.js
- Where to put client state vs server state
- **Zustand** — lightweight global state
- Setting up Zustand stores
- Using Zustand with Server Components
- **Context API** in Next.js — client provider pattern
- Combining server data with client state
- Shopping cart example with Zustand

---

## 1️⃣ Client State vs Server State in Next.js

```
Server State:                    Client State:
- Data from API / DB             - UI state (modals, tabs)
- Fetched in Server Components   - Shopping cart
- Cached by Next.js              - User preferences
- Use: fetch(), Prisma           - Form inputs
- NOT stored in useState         - Notifications
```

**Rule of thumb:**
- Data that comes from the server → fetch in Server Component
- UI interactions and local state → client state (Zustand / Context / useState)

---

## 2️⃣ Zustand — Setup

```bash
npm install zustand
```

```js
// store/cart-store.js
import { create } from "zustand";
import { persist } from "zustand/middleware";

export const useCartStore = create(
  persist(
    (set, get) => ({
      items: [],

      addItem: (product) => {
        const items = get().items;
        const existing = items.find((i) => i.id === product.id);

        if (existing) {
          set({
            items: items.map((i) =>
              i.id === product.id ? { ...i, qty: i.qty + 1 } : i
            ),
          });
        } else {
          set({ items: [...items, { ...product, qty: 1 }] });
        }
      },

      removeItem: (id) => {
        set({ items: get().items.filter((i) => i.id !== id) });
      },

      updateQty: (id, qty) => {
        if (qty <= 0) {
          set({ items: get().items.filter((i) => i.id !== id) });
        } else {
          set({
            items: get().items.map((i) => (i.id === id ? { ...i, qty } : i)),
          });
        }
      },

      clearCart: () => set({ items: [] }),

      // Derived state
      totalItems: () => get().items.reduce((sum, i) => sum + i.qty, 0),
      totalPrice: () =>
        get().items.reduce((sum, i) => sum + i.price * i.qty, 0),
    }),
    {
      name: "cart-storage", // localStorage key
    }
  )
);
```

---

## 3️⃣ Using Zustand in Client Components

```jsx
// components/AddToCartButton.jsx
"use client";
import { useState } from "react";
import { useCartStore } from "@/store/cart-store";

export default function AddToCartButton({ product }) {
  const addItem = useCartStore((state) => state.addItem);
  const items = useCartStore((state) => state.items);
  const [added, setAdded] = useState(false);

  const isInCart = items.some((i) => i.id === product.id);

  const handleAdd = () => {
    addItem(product);
    setAdded(true);
    setTimeout(() => setAdded(false), 2000);
  };

  return (
    <button
      onClick={handleAdd}
      style={{
        padding: "10px 20px",
        background: isInCart ? "#4caf50" : "#2196f3",
        color: "#fff",
        border: "none",
        borderRadius: 6,
        cursor: "pointer",
        transition: "background 0.2s",
      }}
    >
      {added ? "✓ Added!" : isInCart ? "In Cart" : "Add to Cart"}
    </button>
  );
}
```

```jsx
// components/CartIcon.jsx
"use client";
import Link from "next/link";
import { useCartStore } from "@/store/cart-store";

export default function CartIcon() {
  const totalItems = useCartStore((state) => state.totalItems());

  return (
    <Link href="/cart" style={{ position: "relative", textDecoration: "none", color: "inherit" }}>
      🛒
      {totalItems > 0 && (
        <span style={{
          position: "absolute",
          top: -8,
          right: -8,
          background: "#f44336",
          color: "#fff",
          borderRadius: "50%",
          width: 18,
          height: 18,
          fontSize: 11,
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
        }}>
          {totalItems}
        </span>
      )}
    </Link>
  );
}
```

---

## 4️⃣ Cart Page with Zustand

```jsx
// app/cart/page.jsx
"use client";
import { useCartStore } from "@/store/cart-store";
import Image from "next/image";
import Link from "next/link";

export default function CartPage() {
  const { items, removeItem, updateQty, clearCart, totalPrice } = useCartStore();

  if (items.length === 0) {
    return (
      <div style={{ textAlign: "center", padding: 80 }}>
        <h2>Your cart is empty</h2>
        <Link href="/products" style={{ color: "#2196f3" }}>Continue Shopping</Link>
      </div>
    );
  }

  return (
    <div style={{ maxWidth: 800, margin: "0 auto", padding: 24 }}>
      <h1>Shopping Cart ({items.length} items)</h1>

      <div style={{ marginBottom: 24 }}>
        {items.map((item) => (
          <div key={item.id} style={{ display: "flex", alignItems: "center", gap: 16, padding: "16px 0", borderBottom: "1px solid #eee" }}>
            <div style={{ position: "relative", width: 80, height: 80, flexShrink: 0 }}>
              <Image src={item.image} alt={item.title} fill style={{ objectFit: "contain" }} />
            </div>
            <div style={{ flex: 1 }}>
              <h3 style={{ fontSize: 14, marginBottom: 4 }}>{item.title}</h3>
              <p style={{ color: "#2196f3" }}>${item.price}</p>
            </div>
            <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
              <button onClick={() => updateQty(item.id, item.qty - 1)} style={{ width: 28, height: 28, border: "1px solid #ddd", borderRadius: 4, cursor: "pointer" }}>-</button>
              <span style={{ width: 32, textAlign: "center" }}>{item.qty}</span>
              <button onClick={() => updateQty(item.id, item.qty + 1)} style={{ width: 28, height: 28, border: "1px solid #ddd", borderRadius: 4, cursor: "pointer" }}>+</button>
            </div>
            <p style={{ width: 80, textAlign: "right" }}>${(item.price * item.qty).toFixed(2)}</p>
            <button onClick={() => removeItem(item.id)} style={{ color: "#f44336", background: "none", border: "none", cursor: "pointer" }}>✕</button>
          </div>
        ))}
      </div>

      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", padding: "16px 0", borderTop: "2px solid #eee" }}>
        <button onClick={clearCart} style={{ color: "#f44336", background: "none", border: "1px solid #f44336", padding: "8px 16px", borderRadius: 4, cursor: "pointer" }}>
          Clear Cart
        </button>
        <div style={{ textAlign: "right" }}>
          <p style={{ fontSize: 20, fontWeight: 600 }}>Total: ${totalPrice().toFixed(2)}</p>
          <button style={{ marginTop: 8, padding: "12px 32px", background: "#4caf50", color: "#fff", border: "none", borderRadius: 6, fontSize: 16, cursor: "pointer" }}>
            Checkout
          </button>
        </div>
      </div>
    </div>
  );
}
```

---

## 5️⃣ Context API in Next.js

For simpler state, use React Context with a client-side provider:

```jsx
// context/ThemeContext.jsx
"use client";
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  return useContext(ThemeContext);
}
```

```jsx
// app/layout.jsx
import { ThemeProvider } from "@/context/ThemeContext";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </body>
    </html>
  );
}
```

```jsx
// components/ThemeToggle.jsx
"use client";
import { useTheme } from "@/context/ThemeContext";

export default function ThemeToggle() {
  const { theme, setTheme } = useTheme();

  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      {theme === "light" ? "🌙 Dark" : "☀️ Light"}
    </button>
  );
}
```

---

## 6️⃣ Combining Server Data + Zustand

Pass server data as props to Client Components that also use store:

```jsx
// app/products/page.jsx — Server Component
import prisma from "@/lib/db";
import ProductGrid from "@/components/ProductGrid";

export default async function ProductsPage() {
  const products = await prisma.product.findMany();

  return <ProductGrid products={products} />; // pass server data as props
}
```

```jsx
// components/ProductGrid.jsx — Client Component
"use client";
import { useCartStore } from "@/store/cart-store";

export default function ProductGrid({ products }) {
  const addItem = useCartStore((state) => state.addItem);

  return (
    <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
      {products.map((p) => (
        <div key={p.id}>
          <h3>{p.name}</h3>
          <p>${p.price}</p>
          <button onClick={() => addItem(p)}>Add to Cart</button>
        </div>
      ))}
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: When should you use Zustand vs Context API in Next.js?**

> Use Zustand for complex, frequently-updated global state (shopping cart, auth, notifications) — it's more performant (only re-renders components that use the specific slice of state) and easier to manage. Use Context API for simple, rarely-updated state (theme, locale, user preferences) where performance isn't a concern.

**Q2: Can you use Zustand directly in a Server Component?**

> No. Zustand is a client-side library — it uses React hooks (`useStore`) which are only available in Client Components. However, you can fetch data in Server Components and pass it as props to Client Components that use Zustand to manage UI interactions with that data.

**Q3: What does the `persist` middleware in Zustand do?**

> It automatically serializes the Zustand store to `localStorage` (or other storage) and rehydrates it when the page reloads. This keeps the cart or user preferences between page visits without a database.

**Q4: Why must Context providers in Next.js be Client Components?**

> Context uses React's component tree to pass values. The `createContext` and `useContext` hooks are not available in Server Components. The provider component needs to be a Client Component (with `"use client"`), but the children passed to it can still be Server Components — they render on the server and their output is passed through.

---

## 🏠 Home Task

Build a **Shopping Cart App** with Zustand:
1. Products page (`/products`) — Server Component fetching from Fake Store API
2. `AddToCartButton` — Client Component that calls `useCartStore.addItem`
3. Cart icon in Navbar showing item count from Zustand store
4. Cart page (`/cart`) — Client Component showing all items with qty controls
5. Remove item and clear cart functionality
6. Total price calculation
7. Use `persist` middleware to save cart to `localStorage`
8. Bonus: Add a toast notification (using Context API) when item is added
