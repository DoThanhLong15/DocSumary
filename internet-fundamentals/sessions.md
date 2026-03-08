# Sessions – Overview and Key Concepts

## 1. What is a Session?

A **session** is a mechanism used by web applications to **maintain user state across multiple HTTP requests**.

Since the **HTTP protocol is stateless**, the server cannot automatically remember previous interactions with a user. Sessions allow the server to store information about a user during their interaction with the website.

Example information stored in a session:

* User authentication status
* User ID
* Shopping cart contents
* Temporary user preferences

Example session data on server:

```text
sessionId: abc123
userId: 42
isAuthenticated: true
cartItems: [product1, product2]
```

---

# 2. Why Sessions Are Needed

HTTP requests are **independent**.

Example interaction:

```text
Request 1 → Login
Request 2 → View dashboard
Request 3 → Add item to cart
Request 4 → Checkout
```

Without sessions, the server would not know that these requests belong to the **same user**.

Sessions solve this problem by storing user data on the server and identifying the user using a **session identifier**.

---

# 3. How Sessions Work

Sessions typically work together with **cookies**.

### Step-by-step flow

1. User sends login request
2. Server authenticates the user
3. Server creates a **session**
4. Server generates a **session ID**
5. Server sends session ID to browser as a cookie
6. Browser sends session ID with future requests
7. Server retrieves session data using that ID

Example flow:

### Server creates session

```text
sessionId = xyz789
sessionData = {
  userId: 123,
  loggedIn: true
}
```

### Server response

```http
Set-Cookie: sessionId=xyz789; HttpOnly; Secure
```

### Browser request later

```http
GET /dashboard HTTP/1.1
Cookie: sessionId=xyz789
```

Server then loads session data using `xyz789`.

---

# 4. Session Components

A typical session system contains three components:

### 1. Session ID

A **unique identifier** for each session.

Example:

```text
xyz789abc123
```

The session ID is sent between client and server.

---

### 2. Session Storage

Session data is stored **server-side**.

Example storage options:

* Memory (in-process)
* Database
* Redis
* Distributed cache

Example session object:

```json
{
  "sessionId": "xyz789",
  "userId": 42,
  "cart": ["item1", "item2"],
  "createdAt": "2026-03-08"
}
```

---

### 3. Session Cookie

The browser stores the **session ID in a cookie**.

Example:

```http
Set-Cookie: sessionId=xyz789; HttpOnly; Secure
```

The cookie does **not store session data**, only the session ID.

---

# 5. Session Lifecycle

Sessions go through several stages.

```text
Session Creation → Active Usage → Expiration → Destruction
```

### 1. Creation

Occurs when:

* User logs in
* Server initializes session

Example:

```text
createSession(userId)
```

---

### 2. Usage

User sends requests and server retrieves session data.

Example:

```text
GET /profile
sessionId → retrieve session
```

---

### 3. Expiration

Sessions expire after **inactivity** or after a defined time.

Example:

```text
sessionTimeout = 30 minutes
```

---

### 4. Destruction

Session is deleted when:

* User logs out
* Session expires
* Server restarts (for memory sessions)

Example:

```text
destroySession(sessionId)
```

---

# 6. Session Storage Strategies

## 1. In-Memory Sessions

Sessions stored in server memory.

Example:

```text
Node.js memory store
Java HttpSession
```

Advantages:

* Very fast

Disadvantages:

* Lost when server restarts
* Not scalable across multiple servers

---

## 2. Database Sessions

Sessions stored in a database.

Example table:

| session_id | user_id | data | expires_at |
| ---------- | ------- | ---- | ---------- |

Advantages:

* Persistent
* Works across servers

Disadvantages:

* Slower than memory

---

## 3. Redis / Cache Sessions

Sessions stored in **Redis or distributed cache**.

Advantages:

* Very fast
* Scalable
* Shared across servers

Common in **large production systems**.

---

# 7. Session vs Cookies

| Feature   | Session              | Cookie                |
| --------- | -------------------- | --------------------- |
| Storage   | Server               | Client (browser)      |
| Data size | Large                | ~4KB limit            |
| Security  | Safer                | More exposed          |
| Lifetime  | Controlled by server | Controlled by browser |

Sessions typically **use cookies to store session IDs**.

---

# 8. Session vs JWT

Modern applications sometimes replace sessions with **JWT (JSON Web Tokens)**.

| Feature     | Session | JWT    |
| ----------- | ------- | ------ |
| Storage     | Server  | Client |
| Stateless   | No      | Yes    |
| Scalability | Harder  | Easier |
| Revocation  | Easy    | Hard   |

Example JWT:

```text
header.payload.signature
```

Sessions are commonly used in **traditional web applications**.

JWT is common in **APIs and microservices**.

---

# 9. Session Security Risks

## 1. Session Hijacking

Attackers steal a session ID and impersonate the user.

Example:

```text
sessionId=xyz789
```

Protection:

* HTTPS
* Secure cookies
* HttpOnly cookies

---

## 2. Session Fixation

Attacker forces a user to use a known session ID.

Protection:

```text
Regenerate session ID after login
```

---

## 3. CSRF (Cross-Site Request Forgery)

Attacker tricks a user into sending requests with valid session cookies.

Protection:

* CSRF tokens
* SameSite cookies

---

# 10. Best Practices for Sessions

### 1. Use Secure Cookies

```http
Set-Cookie: sessionId=xyz789; Secure
```

---

### 2. Use HttpOnly Cookies

```http
Set-Cookie: sessionId=xyz789; HttpOnly
```

---

### 3. Regenerate Session After Login

Prevents session fixation.

Example:

```text
regenerateSessionId()
```

---

### 4. Use Short Session Timeout

Example:

```text
15–30 minutes inactivity timeout
```

---

### 5. Store Sessions in Redis for Scalability

Production architecture:

```text
Load Balancer
      │
 ┌────┴────┐
 Server1  Server2
      │
      ▼
     Redis
```

All servers share session storage.

---

# 11. Example Session Flow (Login System)

### Step 1 – User logs in

Request:

```http
POST /login
```

Server validates credentials.

---

### Step 2 – Server creates session

```text
sessionId = abc999
userId = 42
```

---

### Step 3 – Server sends cookie

```http
Set-Cookie: sessionId=abc999; HttpOnly; Secure
```

---

### Step 4 – Browser sends cookie

Next request:

```http
GET /dashboard
Cookie: sessionId=abc999
```

---

### Step 5 – Server retrieves session

```text
sessionId → session store → user data
```

User is authenticated.

---

# 12. Summary

Sessions allow web applications to **maintain user state across multiple HTTP requests**.

Key ideas:

* Sessions store user data **on the server**
* Browsers store only the **session ID**
* Session IDs are usually stored in **cookies**
* Sessions have a lifecycle: create → use → expire → destroy
* Production systems often store sessions in **Redis or databases**

Sessions are widely used for **authentication, shopping carts, and user state management** in traditional web applications.
