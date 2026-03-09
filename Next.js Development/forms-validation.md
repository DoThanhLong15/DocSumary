# Forms & Validation in React (Next.js)

This document explains how to implement **forms and validation** in
modern React / Next.js applications using:

-   React Hook Form
-   Zod

These tools are widely used in **production-grade React applications**
because they provide:

-   High performance
-   Type-safe validation
-   Minimal re-renders
-   Excellent developer experience

------------------------------------------------------------------------

# 1. Why Use Form Libraries?

Managing forms in React using only `useState` can become complex
quickly.

Common problems:

-   Too many state variables
-   Re-renders on every input change
-   Difficult validation logic
-   Poor scalability

Form libraries solve these problems.

  Feature              React Hook Form
  -------------------- -----------------
  Performance          Very high
  Re-renders           Minimal
  Bundle size          Small
  TypeScript support   Excellent

------------------------------------------------------------------------

# 2. React Hook Form

## What is React Hook Form?

React Hook Form is a **lightweight form library** that uses
**uncontrolled components** and **React hooks** to manage forms
efficiently.

Benefits:

-   Minimal re-renders
-   Easy integration with validation libraries
-   Works well with TypeScript
-   Small bundle size

------------------------------------------------------------------------

## Installation

``` bash
npm install react-hook-form
```

------------------------------------------------------------------------

## Basic Example

``` tsx
"use client"

import { useForm } from "react-hook-form"

type FormData = {
  email: string
  password: string
}

export default function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm<FormData>()

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("email", { required: "Email is required" })} />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register("password", { required: "Password is required" })}
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit">Login</button>
    </form>
  )
}
```

------------------------------------------------------------------------

## Important Hooks

  Hook               Purpose
  ------------------ ---------------------
  `useForm()`        Initialize form
  `register()`       Register inputs
  `handleSubmit()`   Handle submission
  `watch()`          Watch field changes
  `setValue()`       Update field value
  `reset()`          Reset form

------------------------------------------------------------------------

# 3. Zod

## What is Zod?

Zod is a **TypeScript-first schema validation library**.

It allows developers to define **validation rules and TypeScript types
in one place**.

Benefits:

-   Type-safe validation
-   Reusable schemas
-   Clear error messages
-   Works perfectly with React Hook Form

------------------------------------------------------------------------

## Installation

``` bash
npm install zod
```

For React Hook Form integration:

``` bash
npm install @hookform/resolvers
```

------------------------------------------------------------------------

# 4. Zod Schema Example

``` ts
import { z } from "zod"

export const loginSchema = z.object({
  email: z
    .string()
    .email("Invalid email address"),

  password: z
    .string()
    .min(6, "Password must be at least 6 characters")
})
```

Type inference:

``` ts
export type LoginSchema = z.infer<typeof loginSchema>
```

------------------------------------------------------------------------

# 5. Integrating React Hook Form with Zod

React Hook Form can use **Zod as a validation resolver**.

------------------------------------------------------------------------

## Example

``` tsx
"use client"

import { useForm } from "react-hook-form"
import { zodResolver } from "@hookform/resolvers/zod"
import { z } from "zod"

const schema = z.object({
  email: z.string().email("Invalid email"),
  password: z.string().min(6, "Password too short")
})

type FormData = z.infer<typeof schema>

export default function LoginForm() {

  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm<FormData>({
    resolver: zodResolver(schema)
  })

  const onSubmit = (data: FormData) => {
    console.log(data)
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>

      <input {...register("email")} />
      {errors.email && <p>{errors.email.message}</p>}

      <input type="password" {...register("password")} />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit">Submit</button>

    </form>
  )
}
```

------------------------------------------------------------------------

# 6. Advanced Validation with Zod

## Password Confirmation

``` ts
const registerSchema = z
  .object({
    email: z.string().email(),
    password: z.string().min(6),
    confirmPassword: z.string()
  })
  .refine(data => data.password === data.confirmPassword, {
    message: "Passwords do not match",
    path: ["confirmPassword"]
  })
```

------------------------------------------------------------------------

## Optional Fields

``` ts
z.string().optional()
```

------------------------------------------------------------------------

## Default Values

``` ts
z.string().default("guest")
```

------------------------------------------------------------------------

# 7. Handling Server Errors

Sometimes validation happens **on the server**.

Example:

``` tsx
setError("email", {
  type: "server",
  message: "Email already exists"
})
```

------------------------------------------------------------------------

# 8. Form Best Practices

## 1. Use Schema Validation

Prefer:

-   Zod
-   Yup

Instead of manual validation.

------------------------------------------------------------------------

## 2. Keep Form Logic Separate

Good structure:

    schemas/
       auth.schema.ts

    components/
       forms/
          login-form.tsx

------------------------------------------------------------------------

## 3. Validate on Both Client and Server

Client:

-   Faster UX

Server:

-   Security

------------------------------------------------------------------------

## 4. Use Controlled Components Only When Necessary

React Hook Form performs best with:

**Uncontrolled inputs**

------------------------------------------------------------------------

# 9. Production Folder Structure

Example for a Next.js project:

    src/

      components/
        forms/
          login-form.tsx
          register-form.tsx

      schemas/
        auth.schema.ts

      lib/
        validators.ts

------------------------------------------------------------------------

# Summary

Modern React applications typically use:

  Tool              Purpose
  ----------------- ----------------------------------
  React Hook Form   Form state management
  Zod               Schema validation
  Resolver          Integrates validation with forms

This stack provides:

-   High performance
-   Type safety
-   Clean validation logic
-   Scalable form architecture
