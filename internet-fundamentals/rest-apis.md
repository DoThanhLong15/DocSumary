# REST APIs – Overview and Key Concepts

## 1. What is a REST API?

A **REST API (Representational State Transfer API)** is a type of web API that follows the architectural principles of **REST** to allow communication between clients and servers over HTTP.

REST APIs enable systems to **exchange data using standard HTTP methods**.

Example interaction:

```http
GET /users/1
```

Response:

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

REST APIs are widely used in:

* Web applications
* Mobile applications
* Microservices
* Cloud services

---

# 2. REST Principles

REST is an **architectural style** defined by several constraints.

A system must follow these principles to be considered RESTful.

## 1. Client-Server Architecture

The system is divided into two parts:

```text
Client → sends requests
Server → processes requests and returns responses
```

This separation allows:

* Independent development
* Scalability
* Better maintainability

Example:

```text
Browser / Mobile App → API Server → Database
```

---

## 2. Statelessness

Each request from the client must contain **all information needed to process the request**.

The server **does not store client session state**.

Example request:

```http
GET /orders
Authorization: Bearer token123
```

The server processes the request using the data in the request itself.

Benefits:

* Better scalability
* Easier load balancing
* Simpler server design

---

## 3. Cacheability

Responses should indicate whether they can be **cached**.

Example response headers:

```http
Cache-Control: max-age=3600
```

Benefits:

* Reduced server load
* Faster response time
* Improved performance

---

## 4. Uniform Interface

REST APIs follow a **consistent and standardized interface**.

Key aspects:

* Resources identified by URLs
* Standard HTTP methods
* Standard status codes
* Representation of resources

Example resource:

```text
/users/1
```

---

## 5. Layered System

Clients do not know whether they are communicating directly with the server or through intermediaries.

Possible layers:

```text
Client
   ↓
API Gateway
   ↓
Application Server
   ↓
Database
```

Benefits:

* Scalability
* Security
* Load balancing

---

# 3. Resources in REST

In REST, everything is treated as a **resource**.

A resource is an object or data entity.

Examples:

```text
users
products
orders
posts
comments
```

Each resource has a **unique URI**.

Examples:

```text
/users
/users/1
/orders/123
/products/45
```

---

# 4. HTTP Methods in REST

REST APIs use **HTTP methods** to perform operations on resources.

| Method | Purpose           | Example         |
| ------ | ----------------- | --------------- |
| GET    | Retrieve resource | GET /users      |
| POST   | Create resource   | POST /users     |
| PUT    | Update resource   | PUT /users/1    |
| PATCH  | Partial update    | PATCH /users/1  |
| DELETE | Delete resource   | DELETE /users/1 |

---

## GET

Retrieves data from the server.

Example:

```http
GET /users/1
```

Response:

```json
{
  "id": 1,
  "name": "Alice"
}
```

GET should **not modify data**.

---

## POST

Creates a new resource.

Example:

```http
POST /users
```

Request body:

```json
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

Response:

```json
{
  "id": 10,
  "name": "Alice"
}
```

---

## PUT

Replaces an existing resource.

Example:

```http
PUT /users/1
```

Request:

```json
{
  "name": "Alice Updated",
  "email": "alice@example.com"
}
```

---

## PATCH

Updates **part of a resource**.

Example:

```http
PATCH /users/1
```

Request:

```json
{
  "email": "newemail@example.com"
}
```

---

## DELETE

Deletes a resource.

Example:

```http
DELETE /users/1
```

Response:

```text
204 No Content
```

---

# 5. HTTP Status Codes

REST APIs use HTTP status codes to indicate results.

| Code                      | Meaning                          |
| ------------------------- | -------------------------------- |
| 200 OK                    | Successful request               |
| 201 Created               | Resource created                 |
| 204 No Content            | Successful with no response body |
| 400 Bad Request           | Client error                     |
| 401 Unauthorized          | Authentication required          |
| 403 Forbidden             | Access denied                    |
| 404 Not Found             | Resource not found               |
| 500 Internal Server Error | Server error                     |

Example:

```http
HTTP/1.1 201 Created
```

---

# 6. REST API URL Design

Good REST APIs follow consistent URL patterns.

### Use nouns instead of verbs

Good:

```text
/users
/orders
/products
```

Bad:

```text
/getUsers
/createUser
/deleteProduct
```

---

### Use hierarchical structure

Example:

```text
/users/1/orders
```

Meaning:

```text
Orders belonging to user 1
```

---

### Use plural resource names

Example:

```text
/users
/products
/posts
```

---

# 7. Request and Response Format

REST APIs commonly use **JSON**.

Example request:

```http
POST /products
Content-Type: application/json
```

Body:

```json
{
  "name": "Laptop",
  "price": 1200
}
```

Response:

```json
{
  "id": 5,
  "name": "Laptop",
  "price": 1200
}
```

Other formats sometimes used:

* XML
* YAML
* Protocol Buffers

---

# 8. REST API Authentication

Common authentication methods:

## 1. API Keys

Example:

```http
GET /data
x-api-key: abc123
```

---

## 2. Bearer Tokens (JWT)

Example:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

---

## 3. OAuth 2.0

Used for third-party authentication.

Example:

```text
Login with Google
Login with Facebook
```

---

# 9. Pagination

APIs often return large datasets.

Pagination helps limit response size.

Example:

```http
GET /products?page=1&limit=10
```

Response:

```json
{
  "page": 1,
  "limit": 10,
  "total": 200,
  "data": [...]
}
```

---

# 10. Filtering and Sorting

REST APIs support filtering and sorting using query parameters.

Example:

```http
GET /products?category=laptop&sort=price
```

---

# 11. Versioning

APIs often use **versioning** to avoid breaking clients.

Common methods:

### URL versioning

```text
/api/v1/users
/api/v2/users
```

---

### Header versioning

```http
Accept: application/vnd.api.v1+json
```

---

# 12. REST API Example

Example API for a **User system**.

### Get all users

```http
GET /users
```

---

### Get one user

```http
GET /users/1
```

---

### Create user

```http
POST /users
```

Request:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

---

### Update user

```http
PUT /users/1
```

---

### Delete user

```http
DELETE /users/1
```

---

# 13. Best Practices for REST APIs

### 1. Use Proper HTTP Methods

Use:

```text
GET → read
POST → create
PUT/PATCH → update
DELETE → remove
```

---

### 2. Return Meaningful Status Codes

Example:

```http
201 Created
404 Not Found
400 Bad Request
```

---

### 3. Use Consistent Naming

Example:

```text
/users
/orders
/products
```

---

### 4. Implement Pagination

Avoid returning huge datasets.

---

### 5. Secure APIs

Use:

* HTTPS
* Authentication tokens
* Rate limiting

---

# 14. REST vs GraphQL

| Feature        | REST               | GraphQL          |
| -------------- | ------------------ | ---------------- |
| Endpoints      | Multiple endpoints | Single endpoint  |
| Data retrieval | Fixed structure    | Flexible queries |
| Over-fetching  | Possible           | Reduced          |
| Complexity     | Simpler            | More complex     |

REST is still the **most widely used API architecture**.

---

# 15. Summary

REST APIs are a standard way for systems to **communicate over HTTP using resources and standard methods**.

Key concepts:

* Resources identified by URLs
* HTTP methods (GET, POST, PUT, DELETE)
* Stateless communication
* JSON responses
* Proper status codes
* Authentication and versioning

REST APIs are the **foundation of modern web and mobile applications**.