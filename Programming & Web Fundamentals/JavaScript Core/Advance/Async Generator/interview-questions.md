# Async Generators Interview Questions

## Table of Contents

1. [What is an Async Generator in JavaScript](#1-what-is-an-async-generator-in-javascript)
2. [How do Async Generators differ from regular Generators](#2-how-do-async-generators-differ-from-regular-generators)
3. [What is the purpose of for await...of](#3-what-is-the-purpose-of-for-awaitof)
4. [What does generatornext return in an Async Generator](#4-what-does-generatornext-return-in-an-async-generator)
5. [How do yield and await interact inside Async Generators](#5-how-do-yield-and-await-interact-inside-async-generators)
6. [How can Async Generators be cancelled](#6-how-can-async-generators-be-cancelled)
7. [What problem do Async Generators solve in streaming systems](#7-what-problem-do-async-generators-solve-in-streaming-systems)
8. [How do Async Generators help with backpressure](#8-how-do-async-generators-help-with-backpressure)
9. [How can Async Generators be used to build data pipelines](#9-how-can-async-generators-be-used-to-build-data-pipelines)
10. [How do Async Generators interact with the JavaScript event loop](#10-how-do-async-generators-interact-with-the-javascript-event-loop)

---

## 1. What is an Async Generator in JavaScript

> What is an Async Generator and how is it defined in JavaScript?

- An **Async Generator** is a special type of function that allows producing asynchronous streams of values over time.
- It combines the behavior of **async functions** and **generator functions**.
- Async generators return an **AsyncIterator**, which can be consumed using `for await...of`.

Example:

```js
async function* generateNumbers() {
  yield 1
  yield 2
  yield 3
}

async function run() {
  for await (const n of generateNumbers()) {
    console.log(n)
  }
}

run()
```

- Each call to `next()` returns a **Promise** that resolves to `{ value, done }`.

---

## 2. How do Async Generators differ from regular Generators

> What are the main differences between a Generator and an Async Generator?

- The main differences are related to **asynchronous execution** and **return types**.

Comparison:

| Feature | Generator | Async Generator |
|------|------|------|
| Declaration | `function*` | `async function*` |
| next() return | `{value, done}` | `Promise<{value, done}>` |
| Supports await | No | Yes |
| Consumption | `for...of` | `for await...of` |

Example:

```js
function* syncGen() {
  yield 1
}

async function* asyncGen() {
  yield 1
}
```

- Async generators are used when **producing values asynchronously**, such as network streams or database cursors.

---

## 3. What is the purpose of for await...of

> What does the `for await...of` loop do in JavaScript?

- `for await...of` is used to iterate over **asynchronous iterables**.
- It waits for each `Promise` returned by the iterator before continuing.

Example:

```js
async function* numbers() {
  yield Promise.resolve(1)
  yield Promise.resolve(2)
}

async function run() {
  for await (const n of numbers()) {
    console.log(n)
  }
}
```

Execution flow:

```
iterator.next()
↓
Promise resolves
↓
loop continues
```

- This allows writing **sequential asynchronous iteration** using simple syntax.

---

## 4. What does generator.next return in an Async Generator

> What does the `next()` method return when called on an Async Generator?

- `next()` returns a **Promise**.

Structure:

```js
Promise<{
  value: any,
  done: boolean
}>
```

Example:

```js
async function* gen() {
  yield 10
}

const g = gen()

g.next().then(console.log)
```

Output:

```json
{ "value": 10, "done": false }
```

- This Promise resolves when the generator produces the next value.

---

## 5. How do yield and await interact inside Async Generators

> How do `yield` and `await` work together inside an Async Generator?

- `yield` pauses the generator and returns a value to the consumer.
- `await` pauses execution until a Promise resolves.

Example:

```js
async function* example() {
  const data = await fetchData()
  yield data
}
```

Execution order:

```
generator.next()
↓
await fetchData()
↓
Promise resolves
↓
yield data
```

- Async generators allow both **producing values** and **waiting for async operations** inside the same function.

---

## 6. How can Async Generators be cancelled

> How can you stop or cancel an Async Generator?

- Async generators can be cancelled using the `return()` method.

Example:

```js
async function* counter() {
  let i = 0
  while (true) {
    yield i++
  }
}

const gen = counter()

await gen.next()
await gen.return()
```

- `return()` forces the generator to terminate and returns:

```js
{ value: undefined, done: true }
```

- It also allows cleanup logic using `finally`.

Example:

```js
async function* gen() {
  try {
    yield 1
  } finally {
    console.log("cleanup")
  }
}
```

---

## 7. What problem do Async Generators solve in streaming systems

> What problem do Async Generators solve when dealing with streaming data?

- Async generators simplify **asynchronous data streams**.

Common problems solved:

- Handling paginated APIs
- Processing large files
- Streaming network responses
- Building async pipelines

Example:

```js
async function* fetchPages() {
  let page = 1

  while (true) {
    const res = await fetch(`/api?page=${page}`)
    const data = await res.json()

    if (!data.length) return

    yield* data
    page++
  }
}
```

- Consumers can process items one-by-one without loading everything into memory.

---

## 8. How do Async Generators help with backpressure

> How do Async Generators naturally support backpressure?

- Backpressure occurs when:

```
Producer speed > Consumer speed
```

- Async generators use a **pull model**, meaning the producer waits until the consumer asks for the next value.

Example:

```js
async function* producer() {
  while (true) {
    yield fetchData()
  }
}

for await (const item of producer()) {
  await process(item)
}
```

Execution flow:

```
consumer processes item
↓
consumer calls next()
↓
producer generates next item
```

- This prevents unbounded memory usage.

---

## 9. How can Async Generators be used to build data pipelines

> How can Async Generators be composed to build streaming pipelines?

Async generators can be chained to form **data transformation pipelines**.

Example:

```js
async function* map(iterable, fn) {
  for await (const item of iterable) {
    yield fn(item)
  }
}

async function* filter(iterable, predicate) {
  for await (const item of iterable) {
    if (predicate(item)) {
      yield item
    }
  }
}
```

Pipeline usage:

```js
async function* numbers() {
  for (let i = 1; i <= 5; i++) {
    yield i
  }
}

for await (const value of filter(map(numbers(), x => x * 2), x => x > 5)) {
  console.log(value)
}
```

Output:

```
6
8
10
```

---

## 10. How do Async Generators interact with the JavaScript event loop

> How do Async Generators interact with the JavaScript event loop and microtask queue?

- Each call to `next()` returns a **Promise**.
- When `await` is encountered, execution pauses and resumes via the **microtask queue**.

Execution flow:

```
consumer calls next()
↓
generator executes until await or yield
↓
Promise created
↓
microtask scheduled
↓
Promise resolves
↓
generator resumes
```

Example:

```js
async function* gen() {
  yield 1
  await Promise.resolve()
  yield 2
}
```

- The `await` causes the generator to pause and resume asynchronously via the event loop.

This allows async generators to integrate seamlessly with:

- Promises
- async/await
- the JavaScript runtime scheduler.
