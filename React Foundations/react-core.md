# React Core – Key Concepts

React is a **JavaScript library for building user interfaces**, especially single-page applications (SPAs). It allows developers to build complex UIs using **reusable components**.

Modern React applications primarily use **functional components and hooks**.

Topics covered:

* Functional Components
* JSX
* Props
* State
* Hooks
* Important Hooks (`useState`, `useEffect`, `useMemo`, `useCallback`, `useRef`)

---

# 1. Functional Components

## What are Functional Components?

A **functional component** is a JavaScript function that returns JSX describing the UI.

Example:

```jsx
function Welcome() {
  return <h1>Hello, React</h1>;
}
```

Usage:

```jsx
<Welcome />
```

---

## Arrow Function Components

Functional components are commonly written using arrow functions.

Example:

```jsx
const Welcome = () => {
  return <h1>Hello React</h1>;
};
```

---

## Benefits of Functional Components

* Simpler syntax
* Easier to test
* Better performance with hooks
* Replaces class components in modern React

---

# 2. JSX

## What is JSX?

**JSX (JavaScript XML)** is a syntax extension that allows writing HTML-like code inside JavaScript.

Example:

```jsx
const element = <h1>Hello World</h1>;
```

JSX is compiled into JavaScript.

Example:

```javascript
React.createElement("h1", null, "Hello World");
```

---

## JSX Rules

### 1. Return a single parent element

```jsx
return (
  <div>
    <h1>Hello</h1>
    <p>Welcome</p>
  </div>
);
```

Or use fragments:

```jsx
return (
  <>
    <h1>Hello</h1>
    <p>Welcome</p>
  </>
);
```

---

### 2. Use `className` instead of `class`

```jsx
<div className="container"></div>
```

---

### 3. Use `{}` for JavaScript expressions

Example:

```jsx
const name = "Alice";

return <h1>Hello {name}</h1>;
```

---

# 3. Props

## What are Props?

**Props (properties)** are used to pass data from a parent component to a child component.

Example:

```jsx
function Greeting(props) {
  return <h1>Hello {props.name}</h1>;
}
```

Usage:

```jsx
<Greeting name="Alice" />
```

---

## Props Destructuring

A cleaner way to use props.

Example:

```jsx
function Greeting({ name }) {
  return <h1>Hello {name}</h1>;
}
```

---

## Props Are Read-Only

Props **cannot be modified** by the child component.

Incorrect:

```jsx
props.name = "Bob"; // Not allowed
```

---

# 4. State

## What is State?

**State** is data managed inside a component that can change over time.

When state changes, React **re-renders the component**.

Example:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

Explanation:

```text
count → current state value
setCount → function to update state
```

---

# 5. Hooks

## What are Hooks?

**Hooks** allow functional components to use React features such as:

* State
* Lifecycle logic
* Context
* Performance optimization

Rules of hooks:

1. Only call hooks at the **top level**
2. Only call hooks inside **React functions**

Example:

```jsx
import { useState } from "react";
```

---

# 6. useState

`useState` allows components to **store and update state**.

Example:

```jsx
const [count, setCount] = useState(0);
```

Updating state:

```jsx
setCount(count + 1);
```

Example component:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

# 7. useEffect

`useEffect` handles **side effects**.

Examples of side effects:

* API requests
* Subscriptions
* Timers
* DOM manipulation

Example:

```jsx
import { useEffect } from "react";

useEffect(() => {
  console.log("Component mounted");
}, []);
```

---

## Dependency Array

Controls when the effect runs.

### Run once

```jsx
useEffect(() => {
  fetchData();
}, []);
```

---

### Run when dependency changes

```jsx
useEffect(() => {
  console.log(count);
}, [count]);
```

---

### Cleanup function

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

---

# 8. useMemo

`useMemo` **memoizes expensive calculations**.

Example:

```jsx
import { useMemo } from "react";

const expensiveValue = useMemo(() => {
  return calculateExpensiveValue(data);
}, [data]);
```

This prevents recalculating on every render.

Use case:

```text
Heavy computations
Large lists
Performance optimization
```

---

# 9. useCallback

`useCallback` **memoizes functions**.

Example:

```jsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

Useful when passing functions to child components to **prevent unnecessary re-renders**.

Example:

```jsx
<ChildComponent onClick={handleClick} />
```

---

# 10. useRef

`useRef` stores a **mutable value that does not trigger re-render**.

Example:

```jsx
const inputRef = useRef(null);
```

Access DOM element:

```jsx
<input ref={inputRef} />
```

Example:

```jsx
function FocusInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

---

## useRef for storing values

Example:

```jsx
const renderCount = useRef(0);

renderCount.current++;
```

This value persists across renders.

---

# 11. React Component Example

Example combining multiple concepts:

```jsx
import { useState, useEffect } from "react";

function UserList() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("/api/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

# 12. Summary

React core concepts focus on **component-based UI development**.

| Concept               | Purpose                         |
| --------------------- | ------------------------------- |
| Functional Components | Reusable UI components          |
| JSX                   | HTML-like syntax in JavaScript  |
| Props                 | Pass data between components    |
| State                 | Manage dynamic data             |
| Hooks                 | Use React features in functions |

Important hooks:

| Hook        | Purpose                            |
| ----------- | ---------------------------------- |
| useState    | Manage component state             |
| useEffect   | Handle side effects                |
| useMemo     | Memoize expensive values           |
| useCallback | Memoize functions                  |
| useRef      | Access DOM or store mutable values |

Understanding these fundamentals is essential for building **modern React applications**.
