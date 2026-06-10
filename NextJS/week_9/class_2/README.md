# 🔄 TanStack Query with Next.js

## 📚 Topics Covered
- Why use TanStack Query in Next.js
- Setting up `QueryClientProvider` in a Client Component
- Server-side prefetching with `prefetchQuery`
- `dehydrate` and `HydrationBoundary` — hydration pattern
- Using `useQuery` in Client Components
- `useMutation` with Server Actions
- When to use TanStack Query vs Server Components
- Complete example: Hydrated product list

---

## 1️⃣ Why TanStack Query in Next.js?

| Approach | Good For |
| -------- | -------- |
| Server Component + `fetch()` | Static/ISR data, SEO-critical pages |
| TanStack Query | Data that needs background refresh, caching on client, optimistic updates, infinite scroll |

TanStack Query shines when you need **client-side interactivity** with data:
- Search with live results
- Infinite scroll
- Optimistic updates (like, delete)
- Background refresh (polls every 30s)

---

## 2️⃣ Setup — QueryClientProvider

```bash
npm install @tanstack/react-query @tanstack/react-query-devtools
```

```jsx
// providers/query-provider.jsx
"use client"; // QueryClientProvider needs to be a Client Component

import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";
import { useState } from "react";

export default function QueryProvider({ children }) {
  // useState ensures each user gets their own QueryClient
  const [queryClient] = useState(
    () =>
      new QueryClient({
        defaultOptions: {
          queries: {
            staleTime: 60 * 1000,     // 1 minute
            refetchOnWindowFocus: true,
          },
        },
      })
  );

  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

```jsx
// app/layout.jsx
import QueryProvider from "@/providers/query-provider";

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <QueryProvider>
          {children}
        </QueryProvider>
      </body>
    </html>
  );
}
```

---

## 3️⃣ Basic `useQuery` in a Client Component

```jsx
// components/PostsList.jsx
"use client";
import { useQuery } from "@tanstack/react-query";

async function fetchPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=10");
  if (!res.ok) throw new Error("Failed to fetch posts");
  return res.json();
}

export default function PostsList() {
  const { data: posts, isLoading, isError, error, isFetching } = useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
    staleTime: 30 * 1000, // 30 seconds
  });

  if (isLoading) return <div style={{ padding: 40, textAlign: "center" }}>⏳ Loading posts...</div>;
  if (isError) return <div style={{ color: "red", padding: 24 }}>❌ {error.message}</div>;

  return (
    <div style={{ padding: 24 }}>
      {isFetching && <p style={{ color: "#999", fontSize: 13 }}>🔄 Refreshing...</p>}
      {posts.map((post) => (
        <div key={post.id} style={{ borderBottom: "1px solid #eee", padding: "12px 0" }}>
          <h3>{post.title}</h3>
          <p style={{ color: "#666" }}>{post.body.slice(0, 80)}...</p>
        </div>
      ))}
    </div>
  );
}
```

---

## 4️⃣ Server Prefetching — `HydrationBoundary`

Prefetch data on the server so the client doesn't need an initial loading state:

```jsx
// app/posts/page.jsx — Server Component
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from "@tanstack/react-query";
import PostsList from "@/components/PostsList";

async function getPosts() {
  const res = await fetch("https://jsonplaceholder.typicode.com/posts?_limit=10");
  return res.json();
}

export default async function PostsPage() {
  const queryClient = new QueryClient();

  // Prefetch on server
  await queryClient.prefetchQuery({
    queryKey: ["posts"],
    queryFn: getPosts,
  });

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      {/* PostsList gets pre-filled cache — no loading state! */}
      <PostsList />
    </HydrationBoundary>
  );
}
```

```jsx
// components/PostsList.jsx
"use client";
import { useQuery } from "@tanstack/react-query";

export default function PostsList() {
  const { data: posts, isFetching } = useQuery({
    queryKey: ["posts"],
    queryFn: () => fetch("https://jsonplaceholder.typicode.com/posts?_limit=10").then(r => r.json()),
  });

  // No isLoading check needed — data is pre-populated from server!
  return (
    <div>
      {isFetching && <p style={{ color: "#999" }}>Refreshing in background...</p>}
      {posts?.map((post) => (
        <div key={post.id} style={{ borderBottom: "1px solid #eee", padding: "12px 0" }}>
          <h3>{post.title}</h3>
        </div>
      ))}
    </div>
  );
}
```

---

## 5️⃣ `useMutation` with Next.js API Routes

```jsx
// components/CreatePostForm.jsx
"use client";
import { useState } from "react";
import { useMutation, useQueryClient } from "@tanstack/react-query";

async function createPost(data) {
  const res = await fetch("/api/posts", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error("Failed to create post");
  return res.json();
}

export default function CreatePostForm() {
  const [title, setTitle] = useState("");
  const [body, setBody] = useState("");
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: createPost,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["posts"] }); // refresh list
      setTitle("");
      setBody("");
    },
  });

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        mutation.mutate({ title, body });
      }}
      style={{ maxWidth: 480, marginBottom: 24 }}
    >
      <h2>Create Post</h2>
      <input
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        placeholder="Title"
        style={{ display: "block", width: "100%", padding: 8, marginBottom: 8 }}
      />
      <textarea
        value={body}
        onChange={(e) => setBody(e.target.value)}
        placeholder="Body"
        rows={4}
        style={{ display: "block", width: "100%", padding: 8, marginBottom: 8 }}
      />
      {mutation.isError && <p style={{ color: "red" }}>{mutation.error.message}</p>}
      {mutation.isSuccess && <p style={{ color: "green" }}>✅ Post created!</p>}
      <button type="submit" disabled={mutation.isPending} style={{ padding: "10px 24px", background: "#2196f3", color: "#fff", border: "none", borderRadius: 4 }}>
        {mutation.isPending ? "Creating..." : "Create Post"}
      </button>
    </form>
  );
}
```

---

## 6️⃣ Infinite Scroll with `useInfiniteQuery`

```jsx
// components/InfinitePosts.jsx
"use client";
import { useInfiniteQuery } from "@tanstack/react-query";
import { useCallback, useRef } from "react";

async function fetchPosts({ pageParam = 1 }) {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/posts?_page=${pageParam}&_limit=10`
  );
  const posts = await res.json();
  return { posts, nextPage: posts.length === 10 ? pageParam + 1 : undefined };
}

export default function InfinitePosts() {
  const { data, fetchNextPage, hasNextPage, isFetchingNextPage, isLoading } = useInfiniteQuery({
    queryKey: ["posts-infinite"],
    queryFn: fetchPosts,
    getNextPageParam: (lastPage) => lastPage.nextPage,
    initialPageParam: 1,
  });

  const observer = useRef();
  const lastRef = useCallback((node) => {
    if (isFetchingNextPage) return;
    if (observer.current) observer.current.disconnect();
    observer.current = new IntersectionObserver((entries) => {
      if (entries[0].isIntersecting && hasNextPage) fetchNextPage();
    });
    if (node) observer.current.observe(node);
  }, [isFetchingNextPage, hasNextPage, fetchNextPage]);

  const allPosts = data?.pages.flatMap((p) => p.posts) ?? [];

  if (isLoading) return <p>Loading...</p>;

  return (
    <div style={{ padding: 24 }}>
      {allPosts.map((post, index) => (
        <div
          key={post.id}
          ref={index === allPosts.length - 1 ? lastRef : null}
          style={{ padding: "12px 0", borderBottom: "1px solid #eee" }}
        >
          <h3>{post.title}</h3>
        </div>
      ))}
      {isFetchingNextPage && <p style={{ textAlign: "center" }}>⏳ Loading more...</p>}
      {!hasNextPage && <p style={{ textAlign: "center", color: "#999" }}>✅ All loaded</p>}
    </div>
  );
}
```

---

## 🎯 Interview Questions

**Q1: When should you use TanStack Query instead of Server Components in Next.js?**

> Use Server Components for initial page loads where data is static or infrequently changing — great SEO, no JS sent. Use TanStack Query when you need client-side interactivity: background polling, optimistic updates, infinite scroll, or when the user triggers new fetches (search, filters) after the page loads.

**Q2: What is the `HydrationBoundary` pattern?**

> It allows you to prefetch data on the server and "dehydrate" the cache into a serializable state. This state is sent to the client and "hydrated" into TanStack Query's cache. The result: Client Components using `useQuery` have data immediately on first render — no loading spinner, no extra network request.

**Q3: Why must `QueryClientProvider` be a Client Component?**

> It uses `createContext` to provide the `QueryClient` to the component tree, which is a React client-side feature. Additionally, `QueryClient` manages an in-memory cache that lives in the browser. For SSR, a new `QueryClient` is created per request (inside `useState`) to avoid sharing state between users.

**Q4: How does `invalidateQueries` work after a mutation?**

> After a successful mutation, calling `queryClient.invalidateQueries({ queryKey: ["posts"] })` marks all queries with the "posts" key as stale. TanStack Query then automatically refetches them in the background. Components using those queries will re-render with fresh data once the refetch completes.

---

## 🏠 Home Task

Build a **Posts App** using TanStack Query with Next.js:
1. Set up `QueryProvider` in the root layout
2. Create a Server Component that prefetches posts using `prefetchQuery` + `HydrationBoundary`
3. `PostsList` Client Component — `useQuery` (no loading spinner on first load due to hydration)
4. `CreatePostForm` — `useMutation` to POST to `/api/posts`, invalidate on success
5. Delete button on each post — `useMutation` to DELETE, with optimistic update
6. Add `useInfiniteQuery` to load more posts (10 per page) with infinite scroll
7. Open TanStack DevTools and observe the cache updating in real time
