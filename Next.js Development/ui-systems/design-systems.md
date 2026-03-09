# Design Systems

## Overview

A **Design System** is a collection of **standards, reusable components, design tokens, and guidelines** that help teams build consistent user interfaces across products.

It acts as a **single source of truth** for both designers and developers, ensuring that every part of the application follows the same visual and interaction patterns.

A mature design system improves:

* UI consistency
* Development speed
* Scalability of large applications
* Collaboration between designers and engineers

---

# Why Design Systems Matter

In large applications, UI problems commonly occur:

* inconsistent colors
* inconsistent spacing
* duplicated components
* different UI patterns for the same functionality

Example problem:

```id="p1"
Button A → blue, rounded  
Button B → green, square  
Button C → red, different padding
```

A **design system solves this** by defining **standardized UI rules**.

Example:

```id="p2"
Primary Button
Secondary Button
Danger Button
```

All buttons share the same base styling and behavior.

---

# Core Layers of a Design System

A design system is usually built in layers.

```
Design Tokens
      ↓
Foundations
      ↓
Components
      ↓
Patterns
      ↓
Pages
```

---

# 1. Design Tokens

## Concept

**Design tokens** are the smallest building blocks of a design system.
They store values such as colors, spacing, typography, and shadows.

Instead of hardcoding values:

```css id="p3"
color: #3b82f6;
```

You use tokens:

```css id="p4"
color: var(--color-primary);
```

---

## Common Design Tokens

### Colors

```id="p5"
--color-primary
--color-secondary
--color-background
--color-text
```

Example:

```css id="p6"
:root {
  --color-primary: #3b82f6;
  --color-background: #ffffff;
}
```

---

### Spacing

Spacing tokens create consistent layout spacing.

```id="p7"
--spacing-xs
--spacing-sm
--spacing-md
--spacing-lg
--spacing-xl
```

Example:

```css id="p8"
--spacing-md: 16px;
```

---

### Typography

Typography tokens define font rules.

```id="p9"
--font-family-base
--font-size-sm
--font-size-md
--font-size-lg
```

Example:

```css id="p10"
--font-size-md: 16px;
```

---

### Border Radius

```id="p11"
--radius-sm
--radius-md
--radius-lg
```

Example:

```css id="p12"
--radius-md: 8px;
```

---

# 2. Foundations

Foundations define the **core visual language** of the product.

Examples:

* Color system
* Typography scale
* Spacing scale
* Grid system
* Elevation (shadows)
* Motion and animation

Example typography scale:

```
Heading 1 → 36px
Heading 2 → 30px
Heading 3 → 24px
Body → 16px
Caption → 12px
```

These rules ensure consistent UI.

---

# 3. UI Components

Components are **reusable building blocks** of the interface.

Examples:

* Button
* Input
* Card
* Modal
* Dropdown
* Tooltip
* Table

Example component:

```tsx id="p13"
export function Button({ children }) {
  return (
    <button className="px-4 py-2 rounded-md bg-primary text-white">
      {children}
    </button>
  )
}
```

A design system defines:

* variants
* sizes
* states

Example button variants:

```
Primary
Secondary
Outline
Ghost
Danger
```

Example button states:

```
Default
Hover
Active
Disabled
Loading
```

---

# 4. Patterns

Patterns are **combinations of components used to solve common UI problems**.

Examples:

* Login form
* Search bar
* Navigation header
* Dashboard layout
* Comment section

Example pattern:

```
Login Form
 ├── Input (email)
 ├── Input (password)
 ├── Button (submit)
 └── Link (forgot password)
```

Patterns ensure **consistent UX flows**.

---

# 5. Page Templates

Templates define **page-level layouts**.

Examples:

* Landing page
* Dashboard
* Settings page
* Profile page

Example dashboard layout:

```
Dashboard
 ├── Sidebar
 ├── Navbar
 └── Content Area
```

Templates make building new pages faster.

---

# Design System Architecture (Frontend)

Typical design system folder structure:

```
src/
 ├── design-system/
 │   ├── tokens/
 │   │   ├── colors.ts
 │   │   ├── spacing.ts
 │   │   └── typography.ts
 │   │
 │   ├── foundations/
 │   │   ├── grid.ts
 │   │   └── shadows.ts
 │   │
 │   ├── components/
 │   │   ├── button
 │   │   ├── input
 │   │   └── card
 │   │
 │   └── patterns/
 │       ├── forms
 │       └── navigation
 │
 └── app/
```

---

# Example: Design System with Tailwind

Tailwind can act as a **design system implementation**.

Example Tailwind configuration:

```javascript id="p14"
theme: {
  extend: {
    colors: {
      primary: "#3b82f6",
      secondary: "#9333ea"
    },
    borderRadius: {
      md: "8px"
    }
  }
}
```

Usage:

```html id="p15"
<button class="bg-primary text-white rounded-md px-4 py-2">
  Save
</button>
```

---

# Design System vs UI Library

| Feature       | Design System         | UI Library         |
| ------------- | --------------------- | ------------------ |
| Purpose       | Define design rules   | Provide components |
| Scope         | Entire product design | UI components only |
| Ownership     | Product team          | External library   |
| Customization | Fully customizable    | Limited            |

Example:

```
Design System
   ├── Colors
   ├── Typography
   ├── Components
   └── UX guidelines
```

---

# Famous Design Systems

Several large companies maintain their own design systems.

Examples:

| Company | Design System              |
| ------- | -------------------------- |
| Google  | Material Design            |
| Apple   | Human Interface Guidelines |
| IBM     | Carbon Design System       |
| Shopify | Polaris                    |

These systems help maintain consistency across thousands of screens.

---

# Benefits of Design Systems

## Consistency

Every page follows the same design language.

## Faster Development

Developers reuse components instead of building from scratch.

## Scalability

Large teams can build UI without creating inconsistencies.

## Better Collaboration

Designers and developers share the same system.

---

# Best Practices

## Start Small

Do not build a full design system immediately.

Start with:

* colors
* spacing
* buttons
* inputs

---

## Create Reusable Components

Avoid duplicating UI logic.

Bad:

```
LoginButton
RegisterButton
SubmitButton
```

Good:

```
Button (variant="primary")
```

---

## Document the System

Provide documentation for:

* component usage
* design tokens
* interaction patterns

Tools commonly used:

* Storybook
* Zeroheight
* Notion

---

## Version the Design System

Treat it like a product.

Maintain:

* versioning
* changelog
* migration guides

---

# Design Systems in Next.js Projects

In modern React / Next.js projects, design systems are often implemented with:

* Tailwind CSS
* shadcn/ui
* Storybook
* Radix UI

Example structure:

```
src/
 ├── components/
 │   ├── ui/
 │   └── layout/
 │
 ├── design-system/
 │   ├── tokens
 │   └── foundations
 │
 └── app/
```

---

# Summary

A **Design System** is a structured approach to building user interfaces using:

* **Design Tokens** → colors, spacing, typography
* **Foundations** → grid, typography scale, shadows
* **Components** → buttons, inputs, cards
* **Patterns** → login forms, dashboards
* **Templates** → page layouts

When implemented correctly, design systems help teams build **consistent, scalable, and maintainable UI across large applications**.
