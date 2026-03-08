# TypeScript – Core Concepts

TypeScript is a **statically typed superset of JavaScript** that adds a powerful type system on top of JavaScript. It helps developers write **type-safe, maintainable, and scalable applications**.

TypeScript compiles into **plain JavaScript**, meaning it can run anywhere JavaScript runs.

Topics covered:

* Types
* Interfaces
* Generics
* Utility Types
* Advanced Types
* Type Inference

---

# 1. Types

## What are Types?

Types define the **shape of data** and help catch errors during development.

Example:

```typescript
let age: number = 25;
let username: string = "Alice";
let isActive: boolean = true;
```

If you assign the wrong type, TypeScript will produce an error.

```typescript
let age: number = "twenty"; // Error
```

---

## Basic Types

Common primitive types in TypeScript:

| Type      | Example     |
| --------- | ----------- |
| string    | `"Hello"`   |
| number    | `42`        |
| boolean   | `true`      |
| null      | `null`      |
| undefined | `undefined` |
| symbol    | `Symbol()`  |
| bigint    | `123n`      |

Example:

```typescript
let name: string = "John";
let score: number = 90;
let isAdmin: boolean = false;
```

---

## Arrays

Arrays can be typed in two ways.

Example:

```typescript
let numbers: number[] = [1, 2, 3];
```

Or:

```typescript
let numbers: Array<number> = [1, 2, 3];
```

---

## Tuples

Tuples allow fixed-length arrays with specific types.

Example:

```typescript
let user: [string, number];

user = ["Alice", 25];
```

---

## Enums

Enums define a set of named constants.

Example:

```typescript
enum Role {
  Admin,
  User,
  Guest
}

let userRole: Role = Role.Admin;
```

---

## Any

`any` disables type checking.

Example:

```typescript
let data: any = "hello";
data = 42;
data = true;
```

Using `any` should be **avoided whenever possible**.

---

## Unknown

`unknown` is safer than `any`.

Example:

```typescript
let value: unknown = "hello";

if (typeof value === "string") {
  console.log(value.toUpperCase());
}
```

---

# 2. Interfaces

## What is an Interface?

An **interface** defines the structure of an object.

Example:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}
```

Usage:

```typescript
const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com"
};
```

---

## Optional Properties

Properties can be optional.

Example:

```typescript
interface Product {
  id: number;
  name: string;
  description?: string;
}
```

---

## Readonly Properties

Readonly properties cannot be modified.

Example:

```typescript
interface Config {
  readonly apiUrl: string;
}
```

---

## Interface for Functions

Interfaces can describe function types.

Example:

```typescript
interface AddFunction {
  (a: number, b: number): number;
}

const add: AddFunction = (x, y) => x + y;
```

---

# 3. Generics

## What are Generics?

Generics allow writing **reusable and type-safe code**.

Example:

```typescript
function identity<T>(value: T): T {
  return value;
}
```

Usage:

```typescript
identity<string>("hello");
identity<number>(10);
```

---

## Generic Arrays

Example:

```typescript
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

getFirst([1, 2, 3]);
getFirst(["a", "b", "c"]);
```

---

## Generic Interfaces

Example:

```typescript
interface ApiResponse<T> {
  data: T;
  success: boolean;
}
```

Usage:

```typescript
const response: ApiResponse<string[]> = {
  data: ["a", "b"],
  success: true
};
```

---

# 4. Utility Types

TypeScript provides **built-in utility types** to manipulate existing types.

---

## Partial

Makes all properties optional.

Example:

```typescript
interface User {
  id: number;
  name: string;
}

type PartialUser = Partial<User>;
```

Equivalent to:

```typescript
{
  id?: number;
  name?: string;
}
```

---

## Required

Makes all properties required.

Example:

```typescript
type RequiredUser = Required<User>;
```

---

## Pick

Select specific properties.

Example:

```typescript
type UserPreview = Pick<User, "id" | "name">;
```

---

## Omit

Remove properties from a type.

Example:

```typescript
type UserWithoutId = Omit<User, "id">;
```

---

## Record

Creates an object type with specific keys.

Example:

```typescript
type RolePermissions = Record<string, boolean>;

const permissions: RolePermissions = {
  admin: true,
  editor: true,
  viewer: false
};
```

---

# 5. Advanced Types

## Union Types

A value can be one of multiple types.

Example:

```typescript
let id: string | number;

id = "123";
id = 123;
```

---

## Intersection Types

Combine multiple types.

Example:

```typescript
type User = {
  name: string;
};

type Admin = {
  permissions: string[];
};

type AdminUser = User & Admin;
```

---

## Type Aliases

Define reusable types.

Example:

```typescript
type ID = string | number;
```

---

## Type Guards

Type guards narrow types.

Example:

```typescript
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}
```

---

# 6. Type Inference

TypeScript can automatically **infer types** without explicit annotations.

Example:

```typescript
let message = "Hello";
```

TypeScript infers:

```typescript
string
```

---

## Function Return Type Inference

Example:

```typescript
function add(a: number, b: number) {
  return a + b;
}
```

TypeScript infers:

```typescript
number
```

---

## Contextual Typing

TypeScript determines types based on context.

Example:

```typescript
const numbers = [1, 2, 3];

numbers.forEach(num => {
  console.log(num);
});
```

TypeScript knows `num` is a `number`.

---

# 7. Benefits of TypeScript

TypeScript provides several advantages:

### 1. Early Error Detection

Errors are caught during development.

---

### 2. Better Code Documentation

Types act as **self-documenting code**.

---

### 3. Improved IDE Support

Features include:

* Autocomplete
* Refactoring
* Type checking
* Navigation

---

### 4. Safer Refactoring

Type checking prevents breaking changes.

---

# 8. Example TypeScript Program

```typescript
interface User {
  id: number;
  name: string;
}

function getUserName(user: User): string {
  return user.name;
}

const user: User = {
  id: 1,
  name: "Alice"
};

console.log(getUserName(user));
```

---

# 9. Summary

TypeScript extends JavaScript with a **powerful static type system**.

Key concepts include:

| Concept        | Purpose                          |
| -------------- | -------------------------------- |
| Types          | Define data structure            |
| Interfaces     | Describe object shapes           |
| Generics       | Create reusable typed components |
| Utility Types  | Transform existing types         |
| Advanced Types | Build complex type relationships |
| Type Inference | Automatically detect types       |

### Expected Outcomes

After learning these concepts, developers should be able to:

* Write **type-safe TypeScript code**
* Understand and apply the **TypeScript type system**
* Build more **reliable and maintainable applications**
