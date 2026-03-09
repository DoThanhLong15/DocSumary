# Next.js Routing Guide (App Router)

This document explains the **routing system in Next.js (App Router)**
including:

-   App Router
-   File-based routing
-   Dynamic routes
-   Layouts
-   Middleware

This guide assumes **Next.js 13+ / 14+ with the App Router**.

------------------------------------------------------------------------

# 1. App Router

## What is App Router?

The **App Router** is the modern routing system introduced in **Next.js
13**.\
It replaces the older **Pages Router** and enables advanced capabilities
like:

-   Nested layouts
-   Server Components
-   Streaming
-   Colocation of data fetching
-   Route-based code splitting

Routing is handled inside the **`app/` directory**.

Example project structure:

    app/
     ├── page.tsx
     ├── layout.tsx
     ├── dashboard/
     │    ├── page.tsx
     │    └── settings/
     │         └── page.tsx
     └── api/
          └── users/
               └── route.ts

Resulting routes:

    /               -> app/page.tsx
    /dashboard      -> app/dashboard/page.tsx
    /dashboard/settings -> app/dashboard/settings/page.tsx
    /api/users      -> app/api/users/route.ts

------------------------------------------------------------------------

## Key Characteristics

  Feature              Description
  -------------------- ------------------------------------
  Server Components    Default rendering model
  Nested Layouts       Layout hierarchy supported
  File-based routing   Routes defined by folder structure
  Route Handlers       Backend API inside `app/api`
  Streaming            Progressive UI rendering

------------------------------------------------------------------------

# 2. File-Based Routing

Next.js automatically creates routes based on **files and folders inside
`app/`**.

## Basic Route

    app/about/page.tsx

Accessible at:

    /about

Example:

``` tsx
export default function AboutPage() {
  return <h1>About Page</h1>
}
```

------------------------------------------------------------------------

## Nested Routes

Folder nesting becomes URL nesting.

    app/blog/page.tsx
    app/blog/post/page.tsx

Routes:

    /blog
    /blog/post

------------------------------------------------------------------------

## Special Files

Next.js routing uses **special filenames**.

  File              Purpose
  ----------------- -------------------
  `page.tsx`        Defines a route
  `layout.tsx`      Shared UI layout
  `loading.tsx`     Loading UI
  `error.tsx`       Error boundary
  `not-found.tsx`   Custom 404
  `route.ts`        API route handler

Example:

    app/dashboard/
     ├── page.tsx
     ├── loading.tsx
     └── error.tsx

------------------------------------------------------------------------

# 3. Dynamic Routes

Dynamic routes allow URLs with **parameters**.

Example:

    /blog/123
    /blog/nextjs-routing

## Folder Structure

    app/blog/[slug]/page.tsx

`[slug]` is a **dynamic segment**.

------------------------------------------------------------------------

## Accessing Route Parameters

``` tsx
export default function BlogPost({
  params,
}: {
  params: { slug: string }
}) {
  return <h1>Post: {params.slug}</h1>
}
```

If the URL is:

    /blog/nextjs-guide

Then:

    params.slug = "nextjs-guide"

------------------------------------------------------------------------

## Multiple Parameters

    app/shop/[category]/[product]/page.tsx

URL:

    /shop/shoes/nike-air

Params:

``` ts
params.category = "shoes"
params.product = "nike-air"
```

------------------------------------------------------------------------

## Catch-All Routes

### Syntax

    [...slug]

Example:

    app/docs/[...slug]/page.tsx

URLs:

    /docs/a
    /docs/a/b
    /docs/a/b/c

Params:

``` ts
params.slug = ["a", "b", "c"]
```

------------------------------------------------------------------------

## Optional Catch-All

    [[...slug]]

Matches:

    /docs
    /docs/a
    /docs/a/b

------------------------------------------------------------------------

# 4. Layouts

Layouts allow **shared UI across routes**.

Example use cases:

-   Navbar
-   Sidebar
-   Footer
-   Page shell

------------------------------------------------------------------------

## Root Layout

    app/layout.tsx

Example:

``` tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html>
      <body>
        <nav>Navbar</nav>
        {children}
      </body>
    </html>
  )
}
```

All pages will render inside `{children}`.

------------------------------------------------------------------------

## Nested Layouts

Layouts can be **nested per route segment**.

Example structure:

    app/
     ├── layout.tsx
     ├── dashboard/
     │    ├── layout.tsx
     │    └── page.tsx

Rendering hierarchy:

    RootLayout
       └ DashboardLayout
            └ DashboardPage

Example:

``` tsx
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <div>
      <aside>Sidebar</aside>
      <main>{children}</main>
    </div>
  )
}
```

------------------------------------------------------------------------

## Layout Benefits

-   Avoid repeating UI
-   Better performance
-   Persistent UI state
-   Component reuse

------------------------------------------------------------------------

# 5. Middleware

Middleware allows code to run **before a request is completed**.

Use cases:

-   Authentication
-   Authorization
-   Redirects
-   Logging
-   Rate limiting
-   Localization

------------------------------------------------------------------------

## Middleware File

Create at the project root:

    middleware.ts

Example:

``` ts
import { NextResponse } from "next/server"
import type { NextRequest } from "next/server"

export function middleware(request: NextRequest) {
  const token = request.cookies.get("token")

  if (!token) {
    return NextResponse.redirect(new URL("/login", request.url))
  }

  return NextResponse.next()
}
```

------------------------------------------------------------------------

## Matcher Configuration

Limit middleware to specific routes.

``` ts
export const config = {
  matcher: ["/dashboard/:path*"],
}
```

This applies middleware only to:

    /dashboard
    /dashboard/settings
    /dashboard/profile

------------------------------------------------------------------------

## Example: Auth Protection

    middleware.ts

``` ts
export function middleware(req: NextRequest) {
  const token = req.cookies.get("auth")

  if (!token) {
    return NextResponse.redirect(new URL("/login", req.url))
  }

  return NextResponse.next()
}
```

Protected routes:

    /dashboard
    /profile
    /settings

------------------------------------------------------------------------

# Summary

  Feature              Description
  -------------------- --------------------------------------
  App Router           Modern routing system in Next.js
  File-based routing   Routes defined by folder structure
  Dynamic routes       URL parameters via `[param]`
  Layouts              Shared UI across pages
  Middleware           Request interception before response

------------------------------------------------------------------------

# Recommended Best Practices

### 1. Organize Routes by Feature

Good structure:

    app/
     ├── dashboard/
     │    ├── analytics/
     │    ├── settings/
     │    └── page.tsx

------------------------------------------------------------------------

### 2. Use Layouts for Shared UI

Avoid repeating components like:

-   Navbar
-   Sidebar
-   Footer

------------------------------------------------------------------------

### 3. Protect Routes via Middleware

Use middleware for:

-   Auth checks
-   Role-based access

------------------------------------------------------------------------

### 4. Prefer Server Components

Server components improve:

-   Performance
-   SEO
-   Data fetching

------------------------------------------------------------------------

### 5. Keep API and UI Together

    app/
     ├── users/
     │    ├── page.tsx
     │    └── route.ts

This **colocates backend logic with UI routes**.

------------------------------------------------------------------------

# Final Architecture Example

    app/
     ├── layout.tsx
     ├── page.tsx
     ├── login/
     │    └── page.tsx
     ├── dashboard/
     │    ├── layout.tsx
     │    ├── page.tsx
     │    ├── analytics/
     │    │     └── page.tsx
     │    └── settings/
     │          └── page.tsx
     └── api/
          └── auth/
               └── route.ts

------------------------------------------------------------------------

This routing system enables **scalable, production-ready Next.js
applications**.
