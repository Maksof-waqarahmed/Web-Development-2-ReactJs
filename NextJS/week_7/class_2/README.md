# 🔍 Metadata API & SEO

## 📚 Topics Covered
- What is SEO and why it matters
- Static metadata — `export const metadata`
- Dynamic metadata — `generateMetadata()`
- `title.template` — consistent title format
- `openGraph` — social media sharing cards
- `twitter` — Twitter cards
- `robots` — search engine crawling rules
- `canonical` URL
- `icons` — favicon and app icons
- Metadata inheritance through layouts

---

## 1️⃣ Why SEO Matters

SEO (Search Engine Optimization) determines how your pages rank in Google search results.

For a React (CSR) app:
- Browser downloads empty HTML → downloads JS → renders page
- Googlebot may not wait → **page not indexed** properly

For a Next.js (SSR/SSG) app:
- Server sends full HTML → Googlebot reads it immediately → **great indexing**

The Metadata API makes it easy to set page titles, descriptions, and social sharing info.

---

## 2️⃣ Static Metadata

Export a `metadata` object from any `page.jsx` or `layout.jsx`:

```jsx
// app/about/page.jsx
export const metadata = {
  title: "About Us — My Company",
  description: "Learn about our mission, team, and values.",
  keywords: ["about", "company", "team"],
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

---

## 3️⃣ Title Template — Consistent Formatting

```jsx
// app/layout.jsx (root layout)
export const metadata = {
  title: {
    default: "My App",                    // used if no page title set
    template: "%s | My App",              // %s = page title
  },
  description: "The best app ever built.",
};
```

```jsx
// app/about/page.jsx
export const metadata = {
  title: "About Us",  // renders as: "About Us | My App"
};

// app/contact/page.jsx
export const metadata = {
  title: "Contact",   // renders as: "Contact | My App"
};

// app/page.jsx (home)
// No title set → uses default: "My App"
```

---

## 4️⃣ OpenGraph — Social Media Cards

When someone shares your URL on WhatsApp, Facebook, or LinkedIn, the OpenGraph tags control the preview card.

```jsx
// app/blog/[slug]/page.jsx
export const metadata = {
  title: "How to Build with Next.js",
  description: "A complete guide to building modern web apps with Next.js.",
  openGraph: {
    title: "How to Build with Next.js",
    description: "A complete guide to building modern web apps with Next.js.",
    url: "https://myapp.com/blog/nextjs-guide",
    siteName: "My Blog",
    images: [
      {
        url: "https://myapp.com/og/nextjs-guide.png",
        width: 1200,
        height: 630,
        alt: "How to Build with Next.js",
      },
    ],
    locale: "en_US",
    type: "article",
  },
};
```

---

## 5️⃣ Twitter Cards

```jsx
export const metadata = {
  twitter: {
    card: "summary_large_image",    // large image card
    title: "How to Build with Next.js",
    description: "A complete guide.",
    creator: "@yourtwitterhandle",
    images: ["https://myapp.com/og/nextjs-guide.png"],
  },
};
```

---

## 6️⃣ Dynamic Metadata — `generateMetadata()`

For pages with dynamic data (like product pages), generate metadata based on the data:

```jsx
// app/products/[id]/page.jsx
async function getProduct(id) {
  const res = await fetch(`https://fakestoreapi.com/products/${id}`);
  return res.json();
}

// Dynamic metadata based on product data
export async function generateMetadata({ params }) {
  const product = await getProduct(params.id);

  if (!product) {
    return {
      title: "Product Not Found",
    };
  }

  return {
    title: product.title,
    description: product.description,
    openGraph: {
      title: product.title,
      description: product.description.slice(0, 160),
      images: [{ url: product.image, width: 500, height: 500 }],
    },
  };
}

export default async function ProductPage({ params }) {
  const product = await getProduct(params.id);
  return <h1>{product.title}</h1>;
}
```

> Next.js deduplicates the `getProduct` fetch — called twice (for metadata + page) but only one HTTP request is made.

---

## 7️⃣ `robots` — Crawling Rules

```jsx
// app/admin/page.jsx
export const metadata = {
  robots: {
    index: false,   // don't add to Google index
    follow: false,  // don't follow links on this page
  },
};
```

```jsx
// app/blog/page.jsx
export const metadata = {
  robots: {
    index: true,    // include in index
    follow: true,   // follow links
    googleBot: {
      index: true,
      follow: true,
      "max-image-preview": "large",
    },
  },
};
```

### Global robots.txt — `app/robots.js`

```js
// app/robots.js
export default function robots() {
  return {
    rules: {
      userAgent: "*",
      allow: "/",
      disallow: ["/admin/", "/api/"],
    },
    sitemap: "https://myapp.com/sitemap.xml",
  };
}
```

---

## 8️⃣ Sitemap — `app/sitemap.js`

```js
// app/sitemap.js
export default async function sitemap() {
  const products = await fetch("https://fakestoreapi.com/products").then(r => r.json());

  const staticPages = [
    { url: "https://myapp.com", lastModified: new Date(), changeFrequency: "monthly", priority: 1 },
    { url: "https://myapp.com/about", lastModified: new Date(), changeFrequency: "yearly", priority: 0.8 },
    { url: "https://myapp.com/products", lastModified: new Date(), changeFrequency: "daily", priority: 0.9 },
  ];

  const productPages = products.map((p) => ({
    url: `https://myapp.com/products/${p.id}`,
    lastModified: new Date(),
    changeFrequency: "weekly",
    priority: 0.7,
  }));

  return [...staticPages, ...productPages];
}
```

---

## 9️⃣ Icons — Favicon and App Icons

Place files in the `app/` folder and Next.js auto-detects them:

```
app/
├── favicon.ico          → /favicon.ico
├── icon.png             → used as app icon
├── apple-icon.png       → iOS home screen icon
└── opengraph-image.png  → default OG image
```

Or set programmatically:

```jsx
export const metadata = {
  icons: {
    icon: "/icon.png",
    shortcut: "/shortcut-icon.png",
    apple: "/apple-icon.png",
  },
};
```

---

## 🎯 Interview Questions

**Q1: What is the difference between static and dynamic metadata?**

> Static metadata uses `export const metadata = {...}` and is evaluated at build time. Dynamic metadata uses `generateMetadata({ params })` — an async function that can fetch data to build metadata based on the URL params (e.g., product title and image for each product page).

**Q2: What is OpenGraph metadata?**

> OpenGraph (`og:`) are HTML meta tags that control how a URL appears when shared on social media (Facebook, WhatsApp, LinkedIn, etc.). They define the preview title, description, and thumbnail image. Without them, social media platforms show a poor or no preview.

**Q3: How does `title.template` work?**

> Defined in the root layout's metadata, `template: "%s | My App"` automatically appends `| My App` to any child page's title. The `%s` is replaced by the page's own `title`. This ensures consistent branding without repeating the site name in every page file.

**Q4: Why does Next.js deduplicate the fetch in `generateMetadata`?**

> `generateMetadata` and the page component often fetch the same data. Next.js automatically memoizes (deduplicates) `fetch()` calls with the same URL within a single render. So even if you call `getProduct(id)` twice, only one HTTP request is made — the second call returns the cached result.

---

## 🏠 Home Task

Add complete SEO to a **Product Catalog App**:
1. Root layout: `title.template: "%s | My Shop"` and default description
2. Home page: `title: "Home"` → renders as "Home | My Shop"
3. Products page: static metadata with description
4. Product detail page: `generateMetadata` that fetches product and sets title, description, og:image
5. Admin pages: `robots: { index: false, follow: false }`
6. Create `app/robots.js` — disallow `/admin` and `/api`
7. Create `app/sitemap.js` — include all product pages
8. Test OG tags with Facebook Sharing Debugger or LinkedIn Post Inspector
