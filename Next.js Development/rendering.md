# Next.js Rendering Strategies

This document explains the main **rendering strategies in Next.js** and
how they are used to build scalable and high‑performance web
applications.

Covered topics:

-   SSR (Server-Side Rendering)
-   SSG (Static Site Generation)
-   ISR (Incremental Static Regeneration)
-   CSR (Client-Side Rendering)
-   React Server Components

This guide assumes **Next.js 13+ / 14+ with the App Router**.

------------------------------------------------------------------------

# 1. Overview of Rendering in Next.js

Rendering determines **where and when HTML is generated** for a web
page.

There are several strategies:

  Strategy                  Where HTML is generated   When
  ------------------------- ------------------------- ----------------------------
  SSR                       Server                    On every request
  SSG                       Build server              During build time
  ISR                       Server                    Re-generated periodically
  CSR                       Browser                   After JS loads
  React Server Components   Server                    During component rendering

Choosing the right strategy affects:

-   Performance
-   SEO
-   Scalability
-   Server cost
-   User experience

------------------------------------------------------------------------

# 2. SSR (Server-Side Rendering)

## Definition

**Server-Side Rendering (SSR)** generates HTML **on the server for every
request**.

The server runs the page code, fetches data, and returns fully rendered
HTML.

------------------------------------------------------------------------

## How SSR Works

Request flow:

1.  Browser sends request
2.  Server fetches data
3.  Server renders React components
4.  Server returns HTML
5.  Browser hydrates the page

------------------------------------------------------------------------

## Example (App Router)

``` tsx
async function getData() {
  const res = await fetch("https://api.example.com/posts", {
    cache: "no-store"
  })

  return res.json()
}

export default async function Page() {
  const data = await getData()

  return (
    <div>
      {data.map((post: any) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}
```

`cache: "no-store"` forces **SSR behavior**.

------------------------------------------------------------------------

## Advantages

-   Always fresh data
-   Good SEO
-   Works well for dynamic pages

Examples:

-   Dashboards
-   User-specific pages
-   E-commerce carts

------------------------------------------------------------------------

## Disadvantages

-   Slower than static pages
-   Server load increases
-   Higher infrastructure cost

------------------------------------------------------------------------

# 3. SSG (Static Site Generation)

## Definition

**Static Site Generation (SSG)** generates HTML **during build time**.

The page becomes a **static file** served by a CDN.

------------------------------------------------------------------------

## How SSG Works

Build flow:

1.  Build process runs
2.  Data is fetched
3.  HTML files are generated
4.  Files are deployed to CDN

Request flow:

1.  User requests page
2.  CDN returns static HTML instantly

------------------------------------------------------------------------

## Example

``` tsx
async function getData() {
  const res = await fetch("https://api.example.com/posts", {
    cache: "force-cache"
  })

  return res.json()
}

export default async function Page() {
  const posts = await getData()

  return (
    <div>
      {posts.map((post: any) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}
```

`force-cache` enables **static generation**.

------------------------------------------------------------------------

## Advantages

-   Extremely fast
-   CDN friendly
-   Very scalable
-   Minimal server cost

------------------------------------------------------------------------

## Disadvantages

-   Data becomes outdated until next build
-   Not ideal for frequently changing data

------------------------------------------------------------------------

## Best Use Cases

-   Blogs
-   Documentation
-   Marketing websites
-   Landing pages

------------------------------------------------------------------------

# 4. ISR (Incremental Static Regeneration)

## Definition

**ISR** combines **SSG performance** with **dynamic updates**.

Pages are generated statically but **re-generated periodically**.

------------------------------------------------------------------------

## How ISR Works

1.  Page is built statically
2.  User requests page
3.  If cache expired:
    -   Next.js regenerates page in background
4.  New version replaces old one

------------------------------------------------------------------------

## Example

``` tsx
async function getData() {
  const res = await fetch("https://api.example.com/posts", {
    next: { revalidate: 60 }
  })

  return res.json()
}

export default async function Page() {
  const posts = await getData()

  return (
    <div>
      {posts.map((post: any) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}
```

This regenerates the page **every 60 seconds**.

------------------------------------------------------------------------

## Advantages

-   Fast like static pages
-   Data eventually updates
-   Lower server cost than SSR

------------------------------------------------------------------------

## Disadvantages

-   Data may be slightly stale
-   Requires caching logic

------------------------------------------------------------------------

## Best Use Cases

-   E-commerce product pages
-   News sites
-   Blog updates
-   Content platforms

------------------------------------------------------------------------

# 5. CSR (Client-Side Rendering)

## Definition

**Client-Side Rendering (CSR)** renders the page **in the browser using
JavaScript**.

The server sends minimal HTML and the browser fetches data.

------------------------------------------------------------------------

## How CSR Works

1.  Browser loads JS bundle
2.  React runs in browser
3.  API calls fetch data
4.  UI renders dynamically

------------------------------------------------------------------------

## Example

``` tsx
"use client"

import { useEffect, useState } from "react"

export default function Page() {
  const [posts, setPosts] = useState([])

  useEffect(() => {
    fetch("/api/posts")
      .then(res => res.json())
      .then(data => setPosts(data))
  }, [])

  return (
    <div>
      {posts.map((post: any) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}
```

------------------------------------------------------------------------

## Advantages

-   Highly interactive
-   Reduces server workload
-   Great for real-time apps

------------------------------------------------------------------------

## Disadvantages

-   Poor SEO
-   Slower first load
-   Requires more client JS

------------------------------------------------------------------------

## Best Use Cases

-   Dashboards
-   Admin panels
-   Real-time applications

------------------------------------------------------------------------

# 6. React Server Components

## Definition

**React Server Components (RSC)** run **on the server** and send
**serialized UI to the client**.

They allow heavy logic and data fetching to stay on the server.

------------------------------------------------------------------------

## Key Characteristics

-   No JavaScript sent to client
-   Direct database/API access
-   Faster page load
-   Smaller bundle size

------------------------------------------------------------------------

## Example

``` tsx
export default async function Page() {
  const res = await fetch("https://api.example.com/posts")
  const posts = await res.json()

  return (
    <div>
      {posts.map((post: any) => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  )
}
```

This component runs **only on the server**.

------------------------------------------------------------------------

## Client Components

To enable interactivity, you must mark a component:

``` tsx
"use client"
```

Example:

``` tsx
"use client"

import { useState } from "react"

export default function Counter() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  )
}
```

------------------------------------------------------------------------

# 7. Rendering Strategy Comparison

  Feature       SSR         SSG          ISR         CSR
  ------------- ----------- ------------ ----------- ----------
  SEO           Excellent   Excellent    Excellent   Poor
  Performance   Medium      Very Fast    Very Fast   Medium
  Fresh Data    Always      Build Time   Periodic    Always
  Server Load   High        Very Low     Low         Very Low
  Complexity    Medium      Low          Medium      Low

------------------------------------------------------------------------

# 8. Recommended Best Practices

## 1. Prefer Static Rendering When Possible

Use **SSG** for:

-   Blogs
-   Documentation
-   Marketing pages

------------------------------------------------------------------------

## 2. Use ISR for Dynamic Content

Good balance between:

-   Performance
-   Fresh data

------------------------------------------------------------------------

## 3. Use SSR for Personalized Pages

Examples:

-   Dashboards
-   Authenticated pages
-   Real-time user data

------------------------------------------------------------------------

## 4. Use CSR for Interactivity

Examples:

-   Forms
-   Dashboards
-   Live data updates

------------------------------------------------------------------------

## 5. Use React Server Components by Default

Benefits:

-   Smaller bundles
-   Faster load times
-   Better performance

------------------------------------------------------------------------

# Summary

Next.js provides **multiple rendering strategies** so developers can
optimize for:

-   Performance
-   SEO
-   Scalability
-   User experience

Modern Next.js applications typically combine:

-   **SSG + ISR for content**
-   **SSR for dynamic pages**
-   **CSR for interactivity**
-   **React Server Components for performance**
