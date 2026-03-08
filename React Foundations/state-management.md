# React State Management

State management is the process of **storing, updating, and sharing application data** across components.

In small React apps, state can be managed locally using hooks like `useState`.
However, as applications grow, managing state across many components becomes complex. This is where **state management libraries** are used.

Two popular libraries:

* Zustand
* Redux Toolkit

---

# 1. What is State Management?

State represents **data that changes over time** and affects the UI.

Examples of state:

* User authentication status
* Shopping cart items
* Theme settings
* API data
* Form inputs

Example with local state:

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

Problems appear when:

* Many components need the same state
* State must persist across pages
* Data flow becomes complex

This is where **global state management** helps.

---

# 2. Global vs Local State

## Local State

State managed inside a component.

Example:

```jsx
const [isOpen, setIsOpen] = useState(false);
```

Use for:

* UI state
* Temporary values
* Component-specific data

Examples:

* Modal open/close
* Input fields
* Toggle switches

---

## Global State

State shared across many components.

Examples:

* Authentication
* Theme
* Shopping cart
* User profile

Global state is typically stored in a **central store**.

---

# 3. Zustand

Zustand is a **lightweight state management library** designed for simplicity.

Advantages:

* Very small bundle size
* Minimal boilerplate
* Simple API
* Uses hooks directly
* No provider required

---

## Installing Zustand

```bash
npm install zustand
```

---

## Creating a Store

Example:

```jsx
import { create } from "zustand";

const useCounterStore = create((set) => ({
  count: 0,

  increment: () => set((state) => ({
    count: state.count + 1
  })),

  decrement: () => set((state) => ({
    count: state.count - 1
  }))
}));
```

---

## Using the Store

```jsx
function Counter() {
  const { count, increment } = useCounterStore();

  return (
    <button onClick={increment}>
      Count: {count}
    </button>
  );
}
```

Zustand automatically triggers re-renders when state changes.

---

## Zustand Store Structure

Example:

```jsx
const useUserStore = create((set) => ({
  user: null,

  setUser: (user) => set({ user }),

  logout: () => set({ user: null })
}));
```

Usage:

```jsx
const user = useUserStore((state) => state.user);
```

---

## Why Developers Like Zustand

Benefits:

* No reducers
* No actions boilerplate
* Easy to learn
* Great for small to medium apps

---

# 4. Redux Toolkit

Redux Toolkit (RTK) is the **official recommended way to use Redux**.

It simplifies traditional Redux by reducing boilerplate.

Traditional Redux required:

* Actions
* Reducers
* Dispatch
* Store setup
* Middleware configuration

Redux Toolkit simplifies this process.

---

## Installing Redux Toolkit

```bash
npm install @reduxjs/toolkit react-redux
```

---

## Creating a Slice

A slice contains:

* State
* Reducers
* Actions

Example:

```jsx
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",

  initialState: {
    value: 0
  },

  reducers: {
    increment: (state) => {
      state.value += 1;
    },

    decrement: (state) => {
      state.value -= 1;
    }
  }
});

export const { increment, decrement } = counterSlice.actions;

export default counterSlice.reducer;
```

---

## Creating the Store

```jsx
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer
  }
});
```

---

## Connecting React to Redux

Wrap the application with `Provider`.

```jsx
import { Provider } from "react-redux";
import { store } from "./store";

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

---

## Using Redux State

```jsx
import { useSelector, useDispatch } from "react-redux";
import { increment } from "./counterSlice";

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <button onClick={() => dispatch(increment())}>
      Count: {count}
    </button>
  );
}
```

---

# 5. Redux Toolkit vs Zustand

| Feature        | Zustand     | Redux Toolkit   |
| -------------- | ----------- | --------------- |
| Complexity     | Very simple | More structured |
| Boilerplate    | Minimal     | Moderate        |
| Learning curve | Easy        | Medium          |
| DevTools       | Basic       | Excellent       |
| Middleware     | Limited     | Powerful        |
| Ecosystem      | Smaller     | Very large      |

---

# 6. When to Use Zustand

Use Zustand when:

* The application is small or medium
* You want minimal setup
* You want simple global state
* You don't need complex middleware

Examples:

* Dashboards
* Small SaaS apps
* UI state management

---

# 7. When to Use Redux Toolkit

Use Redux Toolkit when:

* The application is large
* Many developers work on the project
* You need predictable architecture
* Advanced middleware is required
* Debugging tools are important

Examples:

* Large enterprise apps
* Complex data flows
* Applications with many state updates

---

# 8. Best Practices

### 1. Keep State Minimal

Store only necessary data.

Avoid duplicating data.

---

### 2. Separate UI State and Server State

UI State:

* modals
* dropdowns
* theme

Server State:

* API data
* caching

Libraries like **React Query** often manage server state better.

---

### 3. Organize Stores by Feature

Example:

```
store/
│
├── userStore.ts
├── cartStore.ts
└── themeStore.ts
```

---

### 4. Avoid Overusing Global State

Not everything needs global state.

Prefer:

* `useState`
* `useReducer`
* `context`

when possible.

---

# 9. Summary

State management helps manage **shared application data** efficiently.

Key ideas:

| Concept       | Purpose                                  |
| ------------- | ---------------------------------------- |
| Local State   | Component-specific data                  |
| Global State  | Shared across many components            |
| Zustand       | Lightweight state management             |
| Redux Toolkit | Structured and scalable state management |

Modern React apps often use:

* Zustand for simplicity
* Redux Toolkit for large applications
* React Query for server state
