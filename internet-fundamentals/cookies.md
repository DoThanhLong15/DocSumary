# Cookies – Overview and Key Concepts

## 1. What are Cookies?

**Cookies** are small pieces of data stored in a user's browser by a website. They are used to **remember information about the user** between HTTP requests.

Since the **HTTP protocol is stateless**, cookies help websites maintain **stateful interactions**.

Example uses:

* User authentication
* Session management
* User preferences
* Tracking and analytics
* Shopping cart storage

Example cookie:

```http
Set-Cookie: sessionId=abc123xyz; Path=/; HttpOnly
```

---

# 2. Why Cookies are Needed

HTTP requests are **independent of each other**.

Example:

```
Request 1 → Login
Request 2 → View profile
Request 3 → Add item to cart
```

Without cookies, the server would not know that these requests come from the **same user**.

Cookies allow the browser to send identifying information with every request.

---

# 3. How Cookies Work

### Step-by-step flow

1. User visits a website
2. Server responds with a **Set-Cookie header**
3. Browser stores the cookie
4. Browser automatically sends the cookie with future requests

Example flow:

### Server response

```http
HTTP/1.1 200 OK
Set-Cookie: sessionId=abc123; Path=/; HttpOnly
```

### Browser stores

```
sessionId=abc123
```

### Future request

```http
GET /dashboard HTTP/1.1
Host: example.com
Cookie: sessionId=abc123
```

The server then identifies the user using the cookie.

---

# 4. Cookie Structure

A cookie consists of:

```
name=value; attributes
```

Example:

```http
Set-Cookie: userId=12345; Path=/; Max-Age=3600; Secure; HttpOnly
```

Breakdown:

| Part              | Description                      |
| ----------------- | -------------------------------- |
| name              | Cookie name                      |
| value             | Stored value                     |
| Path              | URL path where cookie is valid   |
| Domain            | Domain allowed to receive cookie |
| Max-Age / Expires | Cookie expiration                |
| Secure            | Only sent over HTTPS             |
| HttpOnly          | Not accessible via JavaScript    |
| SameSite          | Controls cross-site sending      |

---

# 5. Cookie Attributes

## 1. Expires

Defines when the cookie expires.

Example:

```http
Set-Cookie: token=abc123; Expires=Wed, 21 Oct 2026 07:28:00 GMT
```

---

## 2. Max-Age

Defines cookie lifetime in seconds.

Example:

```http
Set-Cookie: token=abc123; Max-Age=3600
```

Meaning:

```
Cookie expires in 1 hour
```

---

## 3. Domain

Specifies which domain can receive the cookie.

Example:

```http
Set-Cookie: id=123; Domain=example.com
```

This cookie will be sent to:

```
example.com
api.example.com
blog.example.com
```

---

## 4. Path

Specifies the URL path where the cookie is valid.

Example:

```http
Set-Cookie: theme=dark; Path=/dashboard
```

The cookie will only be sent to:

```
/dashboard
/dashboard/settings
```

---

## 5. Secure

Ensures cookies are sent **only over HTTPS**.

Example:

```http
Set-Cookie: sessionId=abc123; Secure
```

---

## 6. HttpOnly

Prevents JavaScript from accessing the cookie.

Example:

```http
Set-Cookie: sessionId=abc123; HttpOnly
```

Protects against **XSS attacks**.

---

## 7. SameSite

Controls whether cookies are sent with cross-site requests.

Options:

| Value  | Meaning                              |
| ------ | ------------------------------------ |
| Strict | Only same-site requests              |
| Lax    | Some cross-site requests allowed     |
| None   | Cross-site allowed (requires Secure) |

Example:

```http
Set-Cookie: sessionId=abc123; SameSite=Strict
```

---

# 6. Types of Cookies

## 1. Session Cookies

Temporary cookies stored in memory.

Characteristics:

* Deleted when browser closes
* Used for authentication sessions

Example:

```http
Set-Cookie: sessionId=abc123
```

---

## 2. Persistent Cookies

Stored on disk until expiration.

Example:

```http
Set-Cookie: userPref=dark; Max-Age=2592000
```

Meaning:

```
Stored for 30 days
```

---

## 3. First-Party Cookies

Created by the **domain the user is visiting**.

Example:

```
example.com → sets cookie for example.com
```

Used for:

* Login sessions
* Preferences

---

## 4. Third-Party Cookies

Created by **external domains embedded in a website**.

Example:

```
example.com loads script from ads.com
ads.com sets cookie
```

Used for:

* Advertising
* Cross-site tracking

Many browsers now **block third-party cookies**.

---

# 7. Cookie Storage Limits

Browsers limit cookie size and quantity.

Typical limits:

| Limit              | Value |
| ------------------ | ----- |
| Max cookie size    | ~4 KB |
| Cookies per domain | ~50   |
| Total cookies      | ~3000 |

Large data should not be stored in cookies.

---

# 8. Cookies vs Local Storage vs Session Storage

| Feature                | Cookies               | LocalStorage | SessionStorage |
| ---------------------- | --------------------- | ------------ | -------------- |
| Sent with HTTP request | Yes                   | No           | No             |
| Storage size           | ~4 KB                 | ~5–10 MB     | ~5–10 MB       |
| Expiration             | Configurable          | Persistent   | Session        |
| Accessible by JS       | Yes (unless HttpOnly) | Yes          | Yes            |

Cookies are mainly used for **server communication**.

---

# 9. Security Risks with Cookies

## 1. XSS (Cross-Site Scripting)

Attackers steal cookies via JavaScript.

Example:

```javascript
document.cookie
```

Protection:

```
HttpOnly
```

---

## 2. CSRF (Cross-Site Request Forgery)

Attackers send requests using the victim's cookies.

Protection:

```
SameSite
CSRF Tokens
```

---

## 3. Session Hijacking

Attackers steal session cookies.

Protection:

* HTTPS
* Secure cookies
* Short session expiration

---

# 10. Best Practices for Cookies

### 1. Use Secure Cookies

```http
Set-Cookie: sessionId=abc123; Secure
```

---

### 2. Use HttpOnly

```http
Set-Cookie: sessionId=abc123; HttpOnly
```

---

### 3. Use SameSite Protection

```http
Set-Cookie: sessionId=abc123; SameSite=Lax
```

---

### 4. Avoid Storing Sensitive Data

Never store:

```
passwords
credit cards
private tokens
```

---

### 5. Use Short Expiration

Shorter lifetimes reduce risk.

---

# 11. Example Cookie Usage (Login System)

### User logs in

Server response:

```http
HTTP/1.1 200 OK
Set-Cookie: sessionId=xyz789; HttpOnly; Secure; SameSite=Lax
```

Browser stores:

```
sessionId=xyz789
```

---

### User visits dashboard

Browser request:

```http
GET /dashboard HTTP/1.1
Cookie: sessionId=xyz789
```

Server checks:

```
sessionId → user session
```

User is authenticated.

---

# 12. Summary

Cookies are small pieces of data stored in the browser that allow websites to **maintain state across HTTP requests**.

Key concepts:

* Cookie structure (`name=value`)
* Cookie attributes (Secure, HttpOnly, SameSite)
* Session vs Persistent cookies
* First-party vs Third-party cookies
* Security risks and protections

Cookies are essential for modern web applications, especially for **authentication, session management, and personalization**.
