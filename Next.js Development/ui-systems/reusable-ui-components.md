# Reusable UI Components

## Overview

Reusable UI components are **modular, self-contained interface elements** that can be reused across multiple parts of an application. Instead of rebuilding UI elements repeatedly, developers create components once and reuse them wherever needed.

Reusable components are a fundamental principle of **modern frontend architecture**, especially in frameworks like **React** and **Next.js**.

They help achieve:

* consistent UI
* faster development
* maintainable code
* scalable frontend architecture

---

# Why Reusable Components Matter

Without reusable components, UI code often becomes repetitive.

Example without reuse:

```tsx
<button className="px-4 py-2 bg-blue-600 text-white rounded-md">
  Save
</button>

<button className="px-4 py-2 bg-blue-600 text-white rounded-md">
  Submit
</button>
```

If the button design changes, every instance must be updated manually.

With reusable components:

```tsx
<Button>Save</Button>
<Button>Submit</Button>
```

The change is made **in one place only**.

---

# Characteristics of Good Reusable Components

A well-designed reusable component should be:

### Reusable

Used in multiple places across the application.

### Configurable

Support customization via **props**.

### Isolated

Encapsulate its own UI logic.

### Composable

Can be combined with other components.

### Accessible

Follow accessibility standards (ARIA, keyboard support).

---

# Component Granularity

Components should follow a hierarchy based on complexity.

```
Atoms
 ↓
Molecules
 ↓
Organisms
 ↓
Templates
 ↓
Pages
```

This concept is known as **Atomic Design**.

---

# 1. Atoms

Atoms are the **smallest UI elements**.

Examples:

* Button
* Input
* Label
* Icon
* Avatar

Example:

```tsx
type ButtonProps = {
  children: React.ReactNode
}

export function Button({ children }: ButtonProps) {
  return (
    <button className="px-4 py-2 rounded-md bg-blue-600 text-white">
      {children}
    </button>
  )
}
```

Atoms contain **minimal logic**.

---

# 2. Molecules

Molecules combine multiple atoms to create small UI units.

Example:

Login form field.

```
Label + Input + Error Message
```

Example component:

```tsx
type InputFieldProps = {
  label: string
  placeholder?: string
}

export function InputField({ label, placeholder }: InputFieldProps) {
  return (
    <div className="space-y-2">
      <label className="text-sm font-medium">{label}</label>
      <input
        className="border rounded-md px-3 py-2"
        placeholder={placeholder}
      />
    </div>
  )
}
```

---

# 3. Organisms

Organisms are **complex UI sections** composed of multiple molecules.

Example:

```
Login Form
 ├── InputField (email)
 ├── InputField (password)
 └── Submit Button
```

Example:

```tsx
export function LoginForm() {
  return (
    <form className="space-y-4">
      <InputField label="Email" />
      <InputField label="Password" />
      <Button>Login</Button>
    </form>
  )
}
```

---

# 4. Templates

Templates define the **structure of a page**.

Example dashboard layout:

```
Dashboard Layout
 ├── Sidebar
 ├── Navbar
 └── Content Area
```

Example:

```tsx
export function DashboardLayout({ children }) {
  return (
    <div className="flex">
      <Sidebar />
      <main>{children}</main>
    </div>
  )
}
```

---

# 5. Pages

Pages combine all components to form complete screens.

Example:

```tsx
export default function LoginPage() {
  return (
    <div className="flex justify-center items-center h-screen">
      <LoginForm />
    </div>
  )
}
```

---

# Key Design Principles

## 1. Single Responsibility

Each component should handle **one responsibility**.

Bad:

```
UserProfileCard + API fetching + form logic
```

Good:

```
UserProfileCard (UI)
useUserProfile (data hook)
```

---

## 2. Composition over Inheritance

React components should be composed rather than extended.

Example:

```tsx
<Card>
  <CardHeader />
  <CardContent />
</Card>
```

This approach improves flexibility.

---

## 3. Configurable via Props

Reusable components should support customization.

Example:

```tsx
type ButtonProps = {
  variant?: "primary" | "secondary"
  size?: "sm" | "md" | "lg"
}
```

Example usage:

```tsx
<Button variant="primary" size="lg">
  Submit
</Button>
```

---

# Component Variants

Variants allow one component to support multiple visual styles.

Example button variants:

```
Primary
Secondary
Outline
Ghost
Danger
```

Example implementation:

```tsx
const variants = {
  primary: "bg-blue-600 text-white",
  secondary: "bg-gray-200 text-black",
  outline: "border border-gray-300"
}
```

---

# Component States

Reusable components should support UI states.

Common states:

```
Default
Hover
Active
Disabled
Loading
Error
```

Example:

```tsx
<button disabled={loading}>
  {loading ? "Loading..." : "Submit"}
</button>
```

---

# Folder Structure for Reusable Components

A scalable structure in Next.js:

```
src/
 ├── components/
 │
 │   ├── ui/          # generic reusable components
 │   │   ├── button
 │   │   ├── input
 │   │   └── card
 │
 │   ├── forms/
 │   │   └── login-form
 │
 │   ├── layout/
 │   │   ├── navbar
 │   │   └── sidebar
 │
 │   └── features/
 │       └── auth
 │
 └── app/
```

---

# UI Components vs Feature Components

| Type               | Description                             |
| ------------------ | --------------------------------------- |
| UI Components      | Generic, reusable across the entire app |
| Feature Components | Specific to a business feature          |

Example:

```
UI Component
components/ui/button.tsx

Feature Component
components/auth/login-button.tsx
```

---

# Performance Considerations

Reusable components should avoid unnecessary re-renders.

Strategies:

### Memoization

```tsx
export default React.memo(Button)
```

### Stable Props

Avoid passing new object references unnecessarily.

Bad:

```tsx
<Button style={{ margin: 10 }} />
```

Better:

```tsx
const style = { margin: 10 }
<Button style={style} />
```

---

# Accessibility

Reusable components must support accessibility features.

Examples:

* ARIA attributes
* keyboard navigation
* proper labels

Example:

```tsx
<button aria-label="Close dialog">
```

Accessible components improve usability for all users.

---

# Documentation

Reusable components should be documented.

Popular documentation tools:

* Storybook
* Ladle
* Styleguidist

Example documentation:

```
Button
 ├── Variants
 ├── Sizes
 ├── Props
 └── Examples
```

---

# Example Real Component

Example reusable **Card component**.

```tsx
type CardProps = {
  title: string
  children: React.ReactNode
}

export function Card({ title, children }: CardProps) {
  return (
    <div className="border rounded-lg p-4 shadow">
      <h2 className="text-lg font-semibold">{title}</h2>
      <div className="mt-2">{children}</div>
    </div>
  )
}
```

Usage:

```tsx
<Card title="Profile">
  <p>User information here</p>
</Card>
```

---

# Common Mistakes

### Over-abstracting components

Bad:

```
SuperGenericUniversalButton
```

Too complex to maintain.

---

### Too many props

Components with too many props become difficult to use.

---

### Business logic inside UI components

Keep UI components focused on presentation.

---

# Best Practices

* Keep components **small and focused**
* Prefer **composition**
* Use **variants instead of multiple components**
* Separate **UI and business logic**
* Maintain **consistent naming conventions**

---

# Summary

Reusable UI components are the foundation of scalable frontend systems.

They enable:

* faster development
* consistent UI
* maintainable codebases
* scalable architectures

In modern **Next.js applications**, reusable components are often organized into:

```
ui components
layout components
feature components
```

When combined with **design systems and tools like Tailwind and shadcn/ui**, reusable components help teams build **large, production-ready user interfaces efficiently**.
