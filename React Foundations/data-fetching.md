# Data Fetching in React

Data fetching is the process of **retrieving data from external sources**, usually APIs, and displaying it in a React application.

In modern React applications, fetching data is often handled using specialized libraries instead of manual `fetch` calls.

One of the most popular libraries for this is:

**TanStack Query (formerly React Query)**

This library simplifies:

* API data fetching
* Caching
* Background updates
* Synchronizing server state with UI

---

# 1. What is TanStack Query (React Query)?

TanStack Query is a **data synchronization library for React** that manages **server state** efficiently.

Server state refers to:

* Data stored on a remote server
* Data retrieved through APIs
* Data that can change independently from the UI

Examples:

* User profiles
* Product lists
* Dashboard analytics
* Notifications

TanStack Query helps by handling:

* Fetching data
* Caching responses
* Updating stale data
* Managing loading and error states

---

# 2. Installing TanStack Query

```bash
npm install @tanstack/react-query
```

---

# 3. Setting Up Query Client

First, create a **Query Client** and wrap your application with `QueryClientProvider`.

```jsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}
```

The **Query Client** manages caching and query behavior.

---

# 4. Fetching Data with `useQuery`

The `useQuery` hook is used to fetch and cache data.

Example:

```jsx
import { useQuery } from "@tanstack/react-query";

async function fetchUsers() {
  const res = await fetch("/api/users");
  return res.json();
}

function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error loading users</p>;

  return (
    <ul>
      {data.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

Key parts:

| Property    | Purpose                         |
| ----------- | ------------------------------- |
| `queryKey`  | Unique identifier for the query |
| `queryFn`   | Function that fetches data      |
| `data`      | Returned API data               |
| `isLoading` | Loading state                   |
| `error`     | Error state                     |

---

# 5. Caching

Caching means **storing fetched data locally** so it can be reused without making another network request.

Without caching:

```
Component Mount → API Request
Component Remount → API Request again
```

With caching:

```
Component Mount → API Request
Component Remount → Read from Cache
```

TanStack Query automatically caches responses based on the **query key**.

Example:

```jsx
useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers
});
```

The `"users"` key identifies the cached data.

---

# 6. Stale Time

`staleTime` controls **how long cached data is considered fresh**.

Default behavior:

```
staleTime = 0
```

Meaning:

* Data becomes **stale immediately**
* React Query may refetch it in the background

Example:

```jsx
useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
  staleTime: 1000 * 60 * 5
});
```

This means:

```
Data remains fresh for 5 minutes
```

During this time:

* No refetch occurs
* Cached data is used

---

# 7. Cache Time

Cache time determines **how long unused cache remains in memory**.

Example:

```jsx
useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
  cacheTime: 1000 * 60 * 10
});
```

Meaning:

```
Unused cache stays for 10 minutes
```

After that:

```
Cache is garbage collected
```

---

# 8. Query Invalidation

Invalidation forces React Query to **refetch data because it might be outdated**.

This commonly happens after **mutations** like:

* Creating data
* Updating data
* Deleting data

Example:

```jsx
import { useQueryClient } from "@tanstack/react-query";

const queryClient = useQueryClient();

queryClient.invalidateQueries(["users"]);
```

This tells React Query:

```
The "users" data is outdated → refetch it
```

---

# 9. Mutations (Updating Server Data)

Mutations handle operations like:

* POST
* PUT
* DELETE

Example:

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: (newUser) =>
    fetch("/api/users", {
      method: "POST",
      body: JSON.stringify(newUser)
    }),

  onSuccess: () => {
    queryClient.invalidateQueries(["users"]);
  }
});
```

Flow:

```
Create User → Mutation → Invalidate Query → Refetch Data
```

---

# 10. Background Refetching

React Query automatically refetches data when:

* Window regains focus
* Network reconnects
* Query becomes stale

Example behavior:

```
User switches tab → returns → data refetches automatically
```

This ensures **fresh data without manual logic**.

---

# 11. Query Keys

Query keys identify cached queries.

Example:

```jsx
["users"]
["user", userId]
["posts", { page: 1 }]
```

Good query keys are:

* Unique
* Predictable
* Structured

Example:

```jsx
useQuery({
  queryKey: ["user", id],
  queryFn: () => fetchUser(id)
});
```

---

# 12. Best Practices

## Use Query Keys Properly

Bad:

```
["data"]
```

Better:

```
["users"]
["users", id]
["posts", page]
```

---

## Separate API Layer

Example:

```
api/
  users.js
  posts.js
```

Example:

```jsx
export async function fetchUsers() {
  const res = await fetch("/api/users");
  return res.json();
}
```

---

## Use Mutations for Updates

Never update cached data manually unless necessary.

Prefer:

```
mutation → invalidateQueries
```

---

## Keep Queries Focused

Each query should fetch **one type of data**.

Example:

```
users
posts
comments
```

---

# 13. Example Project Structure

```
src/
│
├── api/
│   └── usersApi.ts
│
├── hooks/
│   └── useUsers.ts
│
├── components/
│   └── UserList.tsx
│
└── queryClient.ts
```

Example custom hook:

```jsx
export function useUsers() {
  return useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers
  });
}
```

---

# 14. Summary

TanStack Query simplifies **server data management in React applications**.

Key concepts:

| Concept            | Description                                |
| ------------------ | ------------------------------------------ |
| Data Fetching      | Retrieving API data                        |
| Caching            | Store data locally to avoid extra requests |
| Stale Time         | How long cached data is fresh              |
| Cache Time         | How long unused cache remains              |
| Query Invalidation | Mark data as outdated and refetch          |

Benefits of TanStack Query:

* Automatic caching
* Background refetching
* Built-in loading and error states
* Simplified server state management
* Improved performance
