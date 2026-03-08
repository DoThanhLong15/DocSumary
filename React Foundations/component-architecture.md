# React Component Architecture

Component architecture refers to **how components are structured, organized, and designed** in a React application to ensure scalability, maintainability, and reusability.

A good component architecture helps:

* Reduce code duplication
* Improve readability
* Increase reusability
* Simplify testing
* Separate concerns

This document covers three important concepts:

* Smart vs Dumb Components
* Reusable Components
* Composition Pattern

---

# 1. Smart vs Dumb Components

This is a common architectural pattern in React used to **separate logic from UI**.

## Smart Components (Container Components)

Smart components are responsible for **business logic and data management**.

Responsibilities:

* Fetch data from APIs
* Manage state
* Handle business logic
* Pass data to child components

Example:

```jsx
import { useState, useEffect } from "react";
import UserList from "./UserList";

function UserContainer() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("/api/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return <UserList users={users} />;
}
```

Here:

```text
UserContainer → Smart Component
```

---

## Dumb Components (Presentational Components)

Dumb components focus only on **displaying UI**.

Responsibilities:

* Receive data through props
* Render UI
* Emit events to parent components

Example:

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

Here:

```text
UserList → Dumb Component
```

---

## Benefits of This Pattern

| Smart Component | Dumb Component             |
| --------------- | -------------------------- |
| Handles logic   | Handles UI                 |
| Manages state   | Stateless or minimal state |
| Fetches data    | Receives props             |

Benefits:

* Better separation of concerns
* Easier testing
* More reusable UI components

---

# 2. Reusable Components

Reusable components are designed to be **used in multiple places across the application**.

Good reusable components should be:

* Generic
* Configurable via props
* Independent from specific business logic

---

## Example: Reusable Button Component

```jsx
function Button({ label, onClick, type = "button" }) {
  return (
    <button type={type} onClick={onClick}>
      {label}
    </button>
  );
}
```

Usage:

```jsx
<Button label="Save" onClick={handleSave} />
<Button label="Delete" onClick={handleDelete} />
```

---

## Example: Reusable Card Component

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div>{children}</div>
    </div>
  );
}
```

Usage:

```jsx
<Card title="Profile">
  <p>User information here</p>
</Card>
```

---

## Design Principles for Reusable Components

### 1. Keep Components Small

Small components are easier to reuse.

---

### 2. Use Props for Configuration

Example:

```jsx
<Input
  placeholder="Enter email"
  type="email"
/>
```

---

### 3. Avoid Business Logic

Reusable components should focus on **UI behavior**.

---

# 3. Composition Pattern

React encourages **composition over inheritance**.

Composition means **building complex components by combining smaller ones**.

---

## Example of Composition

```jsx
function Layout({ header, content, footer }) {
  return (
    <div>
      <header>{header}</header>
      <main>{content}</main>
      <footer>{footer}</footer>
    </div>
  );
}
```

Usage:

```jsx
<Layout
  header={<Header />}
  content={<Dashboard />}
  footer={<Footer />}
/>
```

---

## Using `children` for Composition

The most common composition pattern uses the `children` prop.

Example:

```jsx
function Modal({ children }) {
  return (
    <div className="modal">
      <div className="modal-content">
        {children}
      </div>
    </div>
  );
}
```

Usage:

```jsx
<Modal>
  <h2>Delete Item</h2>
  <p>Are you sure?</p>
</Modal>
```

---

## Compound Components Pattern

Sometimes components work together as a group.

Example:

```jsx
<Tabs>
  <Tabs.List>
    <Tabs.Tab>Home</Tabs.Tab>
    <Tabs.Tab>Profile</Tabs.Tab>
  </Tabs.List>

  <Tabs.Panel>Home Content</Tabs.Panel>
  <Tabs.Panel>Profile Content</Tabs.Panel>
</Tabs>
```

Benefits:

* Flexible structure
* Better developer experience
* Clean API

---

# 4. Example Component Architecture

Example structure in a real React project:

```text
components/
│
├── ui/
│   ├── Button.jsx
│   ├── Input.jsx
│   └── Card.jsx
│
├── layout/
│   ├── Header.jsx
│   ├── Sidebar.jsx
│   └── Layout.jsx
│
├── features/
│   ├── users/
│   │   ├── UserContainer.jsx
│   │   └── UserList.jsx
│
└── pages/
    ├── Dashboard.jsx
    └── Profile.jsx
```

Architecture roles:

```text
UI components → reusable
Feature components → business logic
Pages → composition of features
```

---

# 5. Best Practices

### 1. Prefer Composition over Inheritance

React is designed for composition.

---

### 2. Separate Logic and UI

Use smart/dumb component separation.

---

### 3. Keep Components Focused

A component should do **one thing well**.

---

### 4. Reuse UI Components

Create shared components such as:

* Button
* Input
* Modal
* Card
* Table

---

### 5. Organize by Feature

Instead of grouping by type:

Bad:

```text
components/
services/
reducers/
```

Better:

```text
features/
users/
products/
orders/
```

---

# 6. Summary

React component architecture focuses on building scalable UI systems.

Key ideas:

| Concept             | Purpose                                |
| ------------------- | -------------------------------------- |
| Smart Components    | Handle data and logic                  |
| Dumb Components     | Handle UI rendering                    |
| Reusable Components | Generic UI building blocks             |
| Composition Pattern | Build complex UI from small components |

A well-designed component architecture leads to:

* Better scalability
* Cleaner code
* Easier maintenance
* More reusable components
