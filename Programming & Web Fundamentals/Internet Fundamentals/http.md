# HTTP / HTTPS and Related Concepts

## 1. What is HTTP?

**HTTP (HyperText Transfer Protocol)** is an **Application Layer protocol** used to transfer data between a **client** (browser, mobile app, API client) and a **server** on the Internet.

HTTP works based on the following model:

```
Client → Request → Server
Client ← Response ← Server
```

Example:

1. A user enters a URL in the browser
2. The browser sends an HTTP Request to the server
3. The server processes the request and returns an HTTP Response
4. The browser renders the content

---

# 2. What is HTTPS?

**HTTPS (HyperText Transfer Protocol Secure)** is the **secure version of HTTP**.

HTTPS uses:

```
HTTP + TLS/SSL
```

to encrypt the data transmitted between the client and the server.

### Security Goals of HTTPS

HTTPS ensures three important security properties:

1. **Confidentiality**
   Data is encrypted so that third parties cannot read it.

2. **Integrity**
   Data cannot be modified during transmission.

3. **Authentication**
   The server is verified through an **SSL Certificate**.

---

# 3. HTTP vs HTTPS

| Feature      | HTTP   | HTTPS         |
| ------------ | ------ | ------------- |
| Security     | No     | Yes           |
| Encryption   | No     | Yes (TLS/SSL) |
| Default Port | 80     | 443           |
| SEO          | Lower  | Better        |
| Attack Risk  | Higher | Lower         |

---

# 4. How HTTP Works

Basic HTTP flow:

```
Browser → DNS Lookup → TCP Connection → HTTP Request → Server Response
```

### Steps

1. The user enters a URL
2. The browser performs a **DNS Lookup**
3. A **TCP Connection** is established
4. The browser sends an **HTTP Request**
5. The server processes the request
6. The server sends back an **HTTP Response**

---

# 5. HTTP Request

An HTTP Request consists of:

```
Request Line
Headers
Body (optional)
```

Example:

```
GET /users HTTP/1.1
Host: example.com
User-Agent: Chrome
Accept: application/json
```

---

### Request Methods

| Method | Description    |
| ------ | -------------- |
| GET    | Retrieve data  |
| POST   | Create data    |
| PUT    | Update data    |
| PATCH  | Partial update |
| DELETE | Delete data    |

---

### Request Headers

Headers provide additional information.

Example:

```
Content-Type: application/json
Authorization: Bearer token
User-Agent: Chrome
```

---

### Request Body

The body contains data sent to the server.

Example:

```json
{
  "username": "long",
  "password": "123456"
}
```

---

# 6. HTTP Response

An HTTP Response consists of:

```
Status Line
Headers
Body
```

Example:

```
HTTP/1.1 200 OK
Content-Type: application/json
```

---

### HTTP Status Codes

Status codes are divided into five groups:

| Code | Meaning       |
| ---- | ------------- |
| 1xx  | Informational |
| 2xx  | Success       |
| 3xx  | Redirection   |
| 4xx  | Client Error  |
| 5xx  | Server Error  |

---

### Common Status Codes

| Code | Meaning               |
| ---- | --------------------- |
| 200  | OK                    |
| 201  | Created               |
| 204  | No Content            |
| 301  | Moved Permanently     |
| 302  | Redirect              |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 500  | Internal Server Error |

---

# 7. Stateless Protocol

HTTP is a **stateless protocol**.

This means:

```
Each request is independent
The server does not remember previous requests
```

Example:

```
Request 1 → Login
Request 2 → Get profile
```

The server **does not remember the login state** unless a mechanism is used to store session data.

---

# 8. Cookies

A **Cookie** is a small piece of data sent by the server and stored in the browser.

Example:

```
Set-Cookie: sessionId=abc123
```

The browser sends the cookie back in subsequent requests:

```
Cookie: sessionId=abc123
```

---

# 9. Session

A **Session** is a mechanism for storing user state on the server.

Flow:

```
User logs in
Server creates a session
Server sends sessionId to the browser (cookie)
Browser sends sessionId in subsequent requests
```

---

# 10. Token Authentication

Modern systems (especially REST APIs) often use **tokens instead of sessions**.

Example:

```
Authorization: Bearer JWT_TOKEN
```

Advantages:

* Stateless
* Easier to scale
* Suitable for microservices

---

# 11. SSL / TLS

**SSL (Secure Socket Layer)** and **TLS (Transport Layer Security)** are encryption protocols.

TLS is the modern successor of SSL.

They provide:

```
Encryption
Authentication
Data Integrity
```

---

# 12. SSL Certificate

An **SSL Certificate** is used to verify the identity of a server.

Example:

```
example.com
```

The browser verifies:

* Whether the certificate is valid
* Whether it is issued by a trusted CA
* Whether the domain matches the certificate

---

# 13. HTTPS Handshake

HTTPS connection establishment process:

```
Client → Client Hello
Server → Server Hello + Certificate
Client → Verify Certificate
Client → Generate Session Key
Server ↔ Client → Encrypted Communication
```

---

# 14. HTTP/1.1 vs HTTP/2 vs HTTP/3

### HTTP/1.1

Characteristics:

* Text-based protocol
* One request per connection
* Head-of-line blocking

---

### HTTP/2

Improvements:

* Binary protocol
* Multiplexing
* Header compression
* Server push

---

### HTTP/3

Uses **QUIC protocol (UDP)** instead of TCP.

Advantages:

* Faster connection establishment
* Reduced latency
* Better performance on unstable networks

---

# 15. Related Concepts

## REST API

REST uses HTTP as its foundation.

Example:

```
GET /users
POST /users
PUT /users/1
DELETE /users/1
```

---

## CORS (Cross-Origin Resource Sharing)

CORS allows:

```
Frontend domain A
to access API domain B
```

Example header:

```
Access-Control-Allow-Origin: *
```

---

## Content-Type

Defines the type of data being transmitted.

Examples:

```
application/json
text/html
multipart/form-data
application/xml
```

---

## HTTP Caching

HTTP supports caching to reduce server load.

Examples:

```
Cache-Control: max-age=3600
ETag
Last-Modified
```

---

# 16. Summary

HTTP is the core protocol of the Web used for communication between clients and servers.

HTTPS is the secure version of HTTP that uses TLS/SSL encryption.

Important related concepts include:

* HTTP Request / Response
* HTTP Methods
* Status Codes
* Headers
* Cookies
* Sessions
* Token Authentication
* SSL/TLS
* CORS
* Caching
* HTTP Versions

Understanding these concepts is essential when building:

* Web applications
* REST APIs
* Microservices
* Cloud-based systems
