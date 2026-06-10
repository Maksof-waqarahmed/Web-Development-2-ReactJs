# 🎨 Tailwind CSS — Utility-First Styling in React

## 📚 Topics Covered
- What is Tailwind CSS and utility-first approach
- Installing Tailwind in a Vite + React project
- Core utility classes — spacing, colors, typography, flex, grid
- Responsive design with breakpoints (`sm:`, `md:`, `lg:`)
- Dark mode with `dark:` variant
- Hover, focus, active states
- `cn()` helper for conditional classes
- Reusable component patterns with Tailwind
- `clsx` and `tailwind-merge` libraries

---

## 🔹 What is Tailwind CSS?

Tailwind is a **utility-first CSS framework** — instead of writing custom CSS, you apply small pre-built classes directly in your JSX.

```jsx
// ❌ Traditional CSS approach
<button className="my-button">Click</button>
// CSS file: .my-button { background: blue; color: white; padding: 8px 16px; border-radius: 4px; }

// ✅ Tailwind approach — everything inline
<button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
  Click
</button>
```

---

## 🔹 Installation in Vite + React

```bash
npm install -D tailwindcss @tailwindcss/vite
```

```js
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

```css
/* src/index.css */
@import "tailwindcss";
```

Done! No `tailwind.config.js` needed with Tailwind v4.

---

## 🔹 Core Utility Classes

### Spacing (padding, margin)

```jsx
<div className="p-4">        {/* padding: 1rem (16px) */}
<div className="px-6 py-2">  {/* padding x: 24px, y: 8px */}
<div className="mt-8 mb-4">  {/* margin top: 32px, bottom: 16px */}
<div className="mx-auto">    {/* margin left+right: auto (center) */}
```

### Colors

```jsx
<div className="bg-blue-500">     {/* background: blue-500 */}
<p className="text-gray-700">     {/* text color */}
<div className="border-red-300">  {/* border color */}
```

### Typography

```jsx
<h1 className="text-3xl font-bold">    {/* 30px, bold */}
<p className="text-sm text-gray-500">  {/* small, gray */}
<p className="leading-relaxed">        {/* line-height: 1.625 */}
<p className="tracking-wide">          {/* letter-spacing */}
```

### Flexbox

```jsx
<div className="flex items-center justify-between gap-4">
  <span>Left</span>
  <span>Right</span>
</div>

<div className="flex flex-col gap-2">
  <p>Item 1</p>
  <p>Item 2</p>
</div>
```

### Grid

```jsx
<div className="grid grid-cols-3 gap-4">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
</div>

{/* Responsive grid */}
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
```

### Borders & Rounded

```jsx
<div className="border border-gray-200 rounded-lg shadow-md">
<div className="rounded-full w-12 h-12">    {/* circle */}
<div className="border-2 border-blue-500">
```

---

## 🔹 Responsive Design — Breakpoints

Tailwind is **mobile-first** — default = mobile, prefix for larger screens:

| Prefix | Min-width | Device |
|--------|-----------|--------|
| (none) | 0px | Mobile |
| `sm:` | 640px | Small tablet |
| `md:` | 768px | Tablet |
| `lg:` | 1024px | Desktop |
| `xl:` | 1280px | Wide desktop |

```jsx
<div className="
  flex flex-col         {/* mobile: stack vertically */}
  md:flex-row           {/* tablet+: side by side */}
  gap-4
  p-4 md:p-8           {/* mobile: small padding, desktop: more */}
">

<p className="text-sm md:text-base lg:text-lg">
  Responsive text
</p>

<div className="w-full md:w-1/2 lg:w-1/3">
  Responsive width
</div>
```

---

## 🔹 Hover, Focus, Active States

```jsx
<button className="
  bg-blue-500
  hover:bg-blue-600        {/* darker on hover */}
  active:bg-blue-700       {/* even darker when clicked */}
  focus:outline-none
  focus:ring-2
  focus:ring-blue-300      {/* ring on focus for accessibility */}
  transition-colors        {/* smooth color transition */}
  duration-200
">
  Button
</button>

<input className="
  border border-gray-300
  focus:border-blue-500
  focus:ring-2
  focus:ring-blue-100
  rounded-md px-3 py-2
  outline-none
  transition-all
" />
```

---

## 🔹 Dark Mode

```jsx
// Add 'dark' class to <html> to activate dark mode
// In React, toggle it with JavaScript:

function toggleDark() {
  document.documentElement.classList.toggle("dark");
}

// Usage in components
<div className="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <h1 className="text-2xl font-bold">Hello</h1>
  <p className="text-gray-600 dark:text-gray-400">Subtitle</p>
</div>
```

---

## 🔹 Conditional Classes with `clsx` + `tailwind-merge`

```bash
npm install clsx tailwind-merge
```

```jsx
import { clsx } from "clsx";
import { twMerge } from "tailwind-merge";

// cn() helper — standard pattern in all modern React projects
function cn(...classes) {
  return twMerge(clsx(classes));
}

// Usage — no more string concatenation headaches!
function Button({ variant = "primary", disabled, children }) {
  return (
    <button
      disabled={disabled}
      className={cn(
        "px-4 py-2 rounded font-medium transition-colors",
        variant === "primary" && "bg-blue-500 text-white hover:bg-blue-600",
        variant === "outline" && "border-2 border-blue-500 text-blue-500 hover:bg-blue-50",
        variant === "danger" && "bg-red-500 text-white hover:bg-red-600",
        disabled && "opacity-50 cursor-not-allowed"
      )}
    >
      {children}
    </button>
  );
}
```

---

## 🔹 Complete Component: Product Card

```jsx
function ProductCard({ product, onAddToCart }) {
  const [added, setAdded] = useState(false);

  const handleAdd = () => {
    onAddToCart(product);
    setAdded(true);
    setTimeout(() => setAdded(false), 1500);
  };

  return (
    <div className="bg-white dark:bg-gray-800 rounded-xl shadow-md hover:shadow-lg transition-shadow duration-300 overflow-hidden border border-gray-100 dark:border-gray-700">
      <img
        src={product.image}
        alt={product.name}
        className="w-full h-48 object-cover"
      />
      <div className="p-4">
        <span className="text-xs font-semibold text-blue-500 uppercase tracking-wide">
          {product.category}
        </span>
        <h3 className="text-lg font-bold text-gray-900 dark:text-white mt-1">
          {product.name}
        </h3>
        <p className="text-gray-500 dark:text-gray-400 text-sm mt-1 line-clamp-2">
          {product.description}
        </p>
        <div className="flex items-center justify-between mt-4">
          <span className="text-2xl font-bold text-gray-900 dark:text-white">
            ${product.price}
          </span>
          <button
            onClick={handleAdd}
            className={cn(
              "px-4 py-2 rounded-lg font-medium text-sm transition-all duration-200",
              added
                ? "bg-green-500 text-white"
                : "bg-blue-500 hover:bg-blue-600 text-white"
            )}
          >
            {added ? "✓ Added!" : "Add to Cart"}
          </button>
        </div>
      </div>
    </div>
  );
}
```

---

## 🔹 Complete Layout: Responsive Dashboard

```jsx
function Dashboard() {
  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      {/* Navbar */}
      <nav className="bg-white dark:bg-gray-800 shadow-sm border-b border-gray-200 dark:border-gray-700">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
          <span className="text-xl font-bold text-blue-600">MyApp</span>
          <div className="flex items-center gap-4">
            <button className="text-gray-500 hover:text-gray-700 dark:text-gray-400">
              🔔
            </button>
            <div className="w-8 h-8 bg-blue-500 rounded-full flex items-center justify-center text-white text-sm font-bold">
              A
            </div>
          </div>
        </div>
      </nav>

      <div className="max-w-7xl mx-auto px-4 py-8">
        {/* Stats Grid */}
        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
          {[
            { label: "Total Users", value: "12,430", change: "+12%", color: "blue" },
            { label: "Revenue", value: "$54,230", change: "+8%", color: "green" },
            { label: "Orders", value: "1,893", change: "+3%", color: "purple" },
            { label: "Conversion", value: "3.2%", change: "-1%", color: "orange" },
          ].map((stat) => (
            <div key={stat.label} className="bg-white dark:bg-gray-800 rounded-xl p-6 shadow-sm border border-gray-100 dark:border-gray-700">
              <p className="text-sm text-gray-500 dark:text-gray-400">{stat.label}</p>
              <p className="text-2xl font-bold text-gray-900 dark:text-white mt-1">{stat.value}</p>
              <p className={cn("text-sm mt-1", stat.change.startsWith("+") ? "text-green-500" : "text-red-500")}>
                {stat.change} from last month
              </p>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What is utility-first CSS?**

> Using small, single-purpose CSS classes directly in HTML/JSX rather than writing custom CSS. Tailwind provides thousands of utility classes — `flex`, `p-4`, `text-blue-500` — each doing one thing. You compose them to build any design.

**Q2: How does Tailwind handle responsive design?**

> Mobile-first — default styles apply to all sizes. Prefixes (`sm:`, `md:`, `lg:`) apply styles only at that breakpoint and above: `text-sm md:text-base lg:text-lg`.

**Q3: What is the purpose of `tailwind-merge` and `clsx`?**

> `clsx` handles conditional class logic cleanly. `tailwind-merge` resolves Tailwind class conflicts — e.g., `p-4 p-2` would both apply without it, but `tailwind-merge` keeps only the last one. Together they're the standard `cn()` helper pattern.

**Q4: How do you implement dark mode in Tailwind?**

> Add `dark:` prefix variant to any class: `bg-white dark:bg-gray-900`. Toggle dark mode by adding/removing the `dark` class on `<html>`. Tailwind v3 uses `darkMode: 'class'` in config; v4 uses it automatically.

---

## 🏠 Home Task

Build a **Responsive SaaS Landing Page** with Tailwind:
1. Navbar — logo, links, CTA button (responsive: hamburger menu on mobile)
2. Hero section — heading, subtext, two buttons, illustration
3. Features section — 3-column grid (1 col mobile, 3 col desktop)
4. Pricing section — 3 cards with "Popular" badge on middle card
5. Footer — links, social icons, copyright
6. Dark/Light mode toggle button
7. Smooth hover transitions on all interactive elements
