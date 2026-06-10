# 🖼️ next/image & next/font — Performance Optimization

## 📚 Topics Covered
- `next/image` — `<Image>` component
- Automatic WebP conversion and resizing
- `width`, `height`, `fill`, `priority` props
- Remote images — `remotePatterns` config
- `next/font` — Google Fonts with zero layout shift
- Local fonts
- Font variables with Tailwind CSS
- Why these optimizations matter

---

## 1️⃣ Why Optimize Images?

Images are often the largest files on a page. Problems without optimization:
- Large file size → slow page load
- Wrong size for the device → wasted bandwidth
- No lazy loading → images load even when off-screen
- Layout shift → page jumps when image loads (bad UX)

`next/image` solves ALL of these automatically.

---

## 2️⃣ `<Image>` Component — Basic Usage

```jsx
import Image from "next/image";

// ✅ Local image (from public/ folder)
export default function HeroSection() {
  return (
    <Image
      src="/hero.jpg"         // file is at public/hero.jpg
      alt="Hero image"
      width={800}
      height={400}
      priority                // load immediately (above the fold)
    />
  );
}
```

```jsx
// ✅ Remote image
import Image from "next/image";

export default function ProductCard({ product }) {
  return (
    <div>
      <Image
        src={product.image}           // e.g., https://fakestoreapi.com/img/...
        alt={product.title}
        width={300}
        height={300}
        style={{ objectFit: "contain" }}
      />
      <h3>{product.title}</h3>
    </div>
  );
}
```

---

## 3️⃣ Configuring Remote Images

For security, you must whitelist remote domains in `next.config.mjs`:

```js
// next.config.mjs
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "fakestoreapi.com",
      },
      {
        protocol: "https",
        hostname: "images.unsplash.com",
      },
      {
        protocol: "https",
        hostname: "**.example.com", // wildcard subdomain
      },
    ],
  },
};

export default nextConfig;
```

---

## 4️⃣ `fill` — Responsive Images

Use `fill` when you want the image to fill its container (no fixed width/height):

```jsx
import Image from "next/image";

export default function Banner() {
  return (
    <div style={{ position: "relative", width: "100%", height: 400 }}>
      <Image
        src="/banner.jpg"
        alt="Banner"
        fill                          // fills the parent div
        style={{ objectFit: "cover" }} // cover, contain, fill, etc.
        priority
      />
    </div>
  );
}
```

> When using `fill`, the parent must have `position: relative` and a defined height.

---

## 5️⃣ `sizes` — Responsive Loading

Tell the browser what size the image will be at different screen widths:

```jsx
<Image
  src="/hero.jpg"
  alt="Hero"
  fill
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  style={{ objectFit: "cover" }}
/>
```

This helps the browser download the right-sized image for the current viewport — saves bandwidth.

---

## 6️⃣ Complete Product Cards Example

```jsx
// app/products/page.jsx
import Image from "next/image";
import prisma from "@/lib/db";

async function getProducts() {
  const res = await fetch("https://fakestoreapi.com/products");
  return res.json();
}

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <div style={{ padding: 24 }}>
      <h1>Products</h1>
      <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(200px, 1fr))", gap: 20 }}>
        {products.map((product) => (
          <div key={product.id} style={{ border: "1px solid #eee", borderRadius: 8, overflow: "hidden" }}>
            <div style={{ position: "relative", height: 200, background: "#fafafa" }}>
              <Image
                src={product.image}
                alt={product.title}
                fill
                sizes="(max-width: 768px) 50vw, 25vw"
                style={{ objectFit: "contain", padding: 12 }}
              />
            </div>
            <div style={{ padding: 12 }}>
              <h3 style={{ fontSize: 14, marginBottom: 4 }}>{product.title.slice(0, 40)}...</h3>
              <p style={{ color: "#2196f3", fontWeight: 600 }}>${product.price}</p>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 7️⃣ `next/font` — Google Fonts (Zero Layout Shift)

Without `next/font`, Google Fonts cause a **layout shift** — the page renders with a system font, then jumps when the Google Font loads. `next/font` downloads fonts at build time and serves them from your own domain.

```jsx
// app/layout.jsx
import { Inter, Roboto_Mono, Playfair_Display } from "next/font/google";

// Configure the font
const inter = Inter({
  subsets: ["latin"],
  variable: "--font-inter",  // CSS variable name
  display: "swap",
});

const playfair = Playfair_Display({
  subsets: ["latin"],
  weight: ["400", "700"],
  variable: "--font-playfair",
  display: "swap",
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={`${inter.variable} ${playfair.variable}`}>
      <body style={{ fontFamily: "var(--font-inter)" }}>
        {children}
      </body>
    </html>
  );
}
```

```jsx
// app/page.jsx
export default function HomePage() {
  return (
    <div>
      {/* Uses Inter (applied to body) */}
      <p>This text uses Inter font.</p>

      {/* Uses Playfair Display */}
      <h1 style={{ fontFamily: "var(--font-playfair)" }}>
        Playfair Display Heading
      </h1>
    </div>
  );
}
```

---

## 8️⃣ Local Fonts

```jsx
// app/layout.jsx
import localFont from "next/font/local";

const myFont = localFont({
  src: [
    {
      path: "../public/fonts/MyFont-Regular.woff2",
      weight: "400",
      style: "normal",
    },
    {
      path: "../public/fonts/MyFont-Bold.woff2",
      weight: "700",
      style: "normal",
    },
  ],
  variable: "--font-my-font",
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={myFont.variable}>
      <body style={{ fontFamily: "var(--font-my-font)" }}>
        {children}
      </body>
    </html>
  );
}
```

---

## 🎯 Interview Questions

**Q1: What does `next/image` do automatically?**

> It automatically converts images to WebP format (smaller file size), lazy loads images by default (only loads when in viewport), serves correctly sized images based on device (via srcset), and prevents Cumulative Layout Shift (CLS) by reserving space. All of this happens with zero configuration.

**Q2: When should you use the `priority` prop?**

> Use `priority` on images that are visible above the fold (in the initial viewport) — like hero images, logo, and featured product images. These should load immediately. Without `priority`, images are lazy-loaded, which causes a blank space briefly on hero images.

**Q3: Why does `next/font` eliminate layout shift?**

> Traditional Google Fonts load from an external CDN after the page loads, causing a flash of unstyled text (FOUT) and layout shift. `next/font` downloads the font at build time and self-hosts it. The font is included in the initial page load with `font-display: swap` and size-adjust, preventing any layout shift.

**Q4: What is the difference between `objectFit: "cover"` and `objectFit: "contain"`?**

> `cover` fills the entire container, cropping the image if needed (no empty space, but parts may be cut). `contain` shows the entire image within the container, potentially showing empty space on the sides (like letterboxing). Use `cover` for banners and backgrounds; `contain` for product images.

---

## 🏠 Home Task

Build an **Optimized Gallery App**:
1. Add `next/image` to a product listing page from Fake Store API
2. Use `fill` + `objectFit: "contain"` for product thumbnails
3. Add `priority` to the first 4 products (above the fold)
4. Configure `remotePatterns` for `fakestoreapi.com`
5. Add `sizes` attribute to all images
6. Set up Google Fonts: use `Inter` for body text and `Playfair Display` for headings
7. In DevTools → Network tab: compare image sizes before vs after (WebP)
