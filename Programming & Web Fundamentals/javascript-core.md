# JavaScript Core – Key Concepts

This document summarizes some **core JavaScript concepts** that every developer should understand. These concepts are fundamental for understanding how JavaScript works internally and for writing efficient, maintainable code.

Topics covered:

* Scope
* Closures
* Prototype
* Promises
* Async / Await
* Event Loop
* Modules

---

# 1. Scope

## What is Scope?

**Scope** defines where variables are accessible in a program.

In JavaScript, scope determines **the visibility and lifetime of variables**.

Example:

```javascript
let message = "Hello";

function greet() {
  console.log(message);
}

greet();
```

The variable `message` is accessible inside the function because it exists in an outer scope.

---

## Types of Scope

### 1. Global Scope

Variables declared outside functions belong to the **global scope**.

```javascript
let appName = "MyApp";

function printName() {
  console.log(appName);
}
```

Global variables can be accessed anywhere in the program.

---

### 2. Function Scope

Variables declared inside a function are **only accessible inside that function**.

```javascript
function test() {
  let value = 10;
}

console.log(value); // Error
```

---

### 3. Block Scope

Variables declared using `let` and `const` are **block-scoped**.

```javascript
if (true) {
  let number = 5;
}

console.log(number); // Error
```

---

## Scope Chain

JavaScript uses a **scope chain** to resolve variables.

Example:

```javascript
let a = 1;

function outer() {
  let b = 2;

  function inner() {
    let c = 3;
    console.log(a, b, c);
  }

  inner();
}

outer();
```

Lookup order:

```
inner → outer → global
```

---

# 2. Closures

## What is a Closure?

A **closure** is a function that **remembers variables from its outer scope even after the outer function has finished executing**.

Example:

```javascript
function createCounter() {
  let count = 0;

  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();

console.log(counter()); // 1
console.log(counter()); // 2
```

The inner function keeps access to `count`.

---

## Why Closures Are Useful

Closures are used for:

* Data encapsulation
* Private variables
* Function factories
* Event handlers

Example:

```javascript
function multiplyBy(x) {
  return function (y) {
    return x * y;
  };
}

const double = multiplyBy(2);
console.log(double(5)); // 10
```

---

# 3. Prototype

## What is Prototype?

JavaScript uses **prototype-based inheritance**.

Every JavaScript object has an internal property called:

```
[[Prototype]]
```

This property links objects together in a **prototype chain**.

---

## Example

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log("Hello " + this.name);
};

const user = new Person("Alice");

user.sayHello();
```

Explanation:

```
user → Person.prototype → Object.prototype
```

If a property is not found on the object, JavaScript searches the prototype chain.

---

## Prototype Chain

Example:

```javascript
const obj = {};

console.log(obj.toString());
```

Even though `toString()` is not defined on `obj`, it exists in:

```
Object.prototype
```

---

# 4. Promises

## What is a Promise?

A **Promise** represents the result of an asynchronous operation.

A promise has three states:

| State     | Meaning                |
| --------- | ---------------------- |
| Pending   | Operation not finished |
| Fulfilled | Operation succeeded    |
| Rejected  | Operation failed       |

---

## Example

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Success");
  }, 1000);
});
```

Handling the promise:

```javascript
promise
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

---

# 5. Async / Await

`async/await` is syntactic sugar for working with promises.

It makes asynchronous code **look like synchronous code**.

---

## Example

Using promises:

```javascript
fetch("/api/data")
  .then(response => response.json())
  .then(data => console.log(data));
```

Using async/await:

```javascript
async function getData() {
  const response = await fetch("/api/data");
  const data = await response.json();
  console.log(data);
}
```

Benefits:

* Cleaner code
* Easier error handling

---

## Error Handling

```javascript
async function loadData() {
  try {
    const response = await fetch("/api/data");
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}
```

---

# 6. Event Loop

JavaScript is **single-threaded**, but it can handle asynchronous operations using the **event loop**.

The event loop coordinates:

* Call Stack
* Web APIs
* Callback Queue
* Microtask Queue

---

## Execution Flow

Example:

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timeout");
}, 0);

console.log("End");
```

Output:

```
Start
End
Timeout
```

Explanation:

1. `Start` runs
2. `setTimeout` is registered
3. `End` runs
4. Timeout callback goes to queue
5. Event loop executes it later

---

## Microtasks vs Macrotasks

### Microtasks

Examples:

```
Promise.then
queueMicrotask
MutationObserver
```

### Macrotasks

Examples:

```
setTimeout
setInterval
setImmediate
```

Microtasks run **before** macrotasks.

Example:

```javascript
console.log("1");

setTimeout(() => console.log("2"));

Promise.resolve().then(() => console.log("3"));

console.log("4");
```

Output:

```
1
4
3
2
```

---

# 7. Modules

JavaScript modules allow code to be **split into reusable files**.

Modules improve:

* Code organization
* Maintainability
* Dependency management

---

## Export

Export functions or variables from a module.

```javascript
export function add(a, b) {
  return a + b;
}
```

---

## Import

Import from another module.

```javascript
import { add } from "./math.js";

console.log(add(2, 3));
```

---

## Default Export

A module can have one default export.

```javascript
export default function greet() {
  console.log("Hello");
}
```

Import:

```javascript
import greet from "./greet.js";
```

---

# 8. Summary

Core JavaScript concepts are essential for understanding how the language works.

| Concept     | Purpose                            |
| ----------- | ---------------------------------- |
| Scope       | Controls variable visibility       |
| Closures    | Functions remember outer variables |
| Prototype   | Object inheritance mechanism       |
| Promises    | Handle asynchronous operations     |
| Async/Await | Simplified async code              |
| Event Loop  | Enables asynchronous behavior      |
| Modules     | Organize and reuse code            |

Mastering these concepts is critical for building **modern JavaScript applications**, especially when working with frameworks like React, Node.js, or Next.js.
