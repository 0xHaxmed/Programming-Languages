# 04 - Callbacks & Foundational Asynchronous Concurrency

> 🌐 **Language / اللغة:** [English 🇬🇧](./04-callbacks.md) | [العربية 🇸🇦](../ar/04-callbacks.md)

---

## 🎯 1. Overview & Learning Objectives

In JavaScript, functions are **First-Class Citizens**: they can be stored in variables, passed into other functions as arguments, and returned from functions as values.

A function passed into another function to be invoked at a later time is known as a **Callback Function**. Callbacks form the historical and architectural backbone of JavaScript's non-blocking, event-driven runtime.

By the end of this lesson, you will master:
1. The mechanics of **Higher-Order Functions** and callback execution.
2. The critical difference between **Synchronous** and **Asynchronous** callbacks.
3. Managing delayed execution with `setTimeout()` and `setInterval()`.
4. Implementing the industry-standard **Error-First Callback Pattern**.
5. Diagnosing and refactoring **Callback Hell (Pyramid of Doom)**.

---

## 🎭 2. First-Class Functions & Higher-Order Functions

A **Higher-Order Function** is any function that:
- Accepts another function as an argument, OR
- Returns a function as its output.

```javascript
// 1. Passing a function as an argument:
function executeWithLogging(operationFn, a, b) {
  console.log(`[LOG] Initiating operation with arguments: ${a}, ${b}`);
  const result = operationFn(a, b);
  console.log(`[LOG] Result: ${result}`);
  return result;
}

const add = (x, y) => x + y;
const multiply = (x, y) => x * y;

executeWithLogging(add, 5, 10);      // Logs and returns 15
executeWithLogging(multiply, 4, 3); // Logs and returns 12
```

---

## ⏱️ 3. Synchronous vs Asynchronous Callbacks

### A. Synchronous Callbacks
Execute immediately during the execution of the higher-order function, blocking further code until finished:
```javascript
console.log('Step 1: Start');

[1, 2, 3].forEach((num) => {
  console.log(`Processing: ${num}`); // Executes synchronously!
});

console.log('Step 2: End');
// Output Order: Step 1 -> Processing 1, 2, 3 -> Step 2
```

### B. Asynchronous Callbacks
Scheduled to execute later after the current call stack clears and an external event (timer, I/O, user click) completes:
```javascript
console.log('Step 1: Start');

// Defers execution by 1000ms:
setTimeout(() => {
  console.log('Step 2: Timeout Complete (Async)');
}, 1000);

console.log('Step 3: End of Script');
// Output Order: Step 1 -> Step 3 -> Step 2 (after 1 second)
```

> [!IMPORTANT]
> Even with a delay of `0` milliseconds (`setTimeout(fn, 0)`), the callback runs **asynchronously** after the main synchronous script has completely finished executing.

---

## 🔄 4. Timers: `setTimeout` & `setInterval`

Browser and Node.js runtimes provide timing primitives:

### `setTimeout(callback, delayMs, ...args)`
Executes the callback **once** after the specified delay:
```javascript
const timerId = setTimeout((user) => {
  console.log(`Session expired for: ${user}`);
}, 3000, 'Alice');

// Cancel the timer before it fires:
clearTimeout(timerId);
```

### `setInterval(callback, intervalMs, ...args)`
Executes the callback **repeatedly** every interval until cleared:
```javascript
let counter = 1;

const intervalId = setInterval(() => {
  console.log(`Tick ${counter}`);
  counter++;

  if (counter > 3) {
    clearInterval(intervalId); // Essential: prevents infinite background loops!
  }
}, 1000);
```

---

## 🛡️ 5. The Error-First Callback Pattern

Before Promises became standard, the JavaScript ecosystem adopted the **Node.js Error-First Convention**:
- The **first argument** passed to the callback is reserved for an `error` object (or `null` if successful).
- The subsequent arguments contain the successful `data`.

```javascript
function fetchUserData(userId, callback) {
  // Simulate network/database lookup:
  setTimeout(() => {
    if (userId <= 0) {
      callback(new Error('Invalid user ID: Must be positive'), null);
      return;
    }

    const mockUser = { id: userId, username: 'dev_alex' };
    callback(null, mockUser); // No error, pass data
  }, 500);
}

// Consuming the error-first callback:
fetchUserData(10, (err, user) => {
  if (err) {
    console.error('Operation failed:', err.message);
    return;
  }
  console.log('User retrieved successfully:', user.username);
});
```

---

## 🌪️ 6. Callback Hell (The Pyramid of Doom)

When multiple asynchronous operations depend on the outcome of previous ones, nested callbacks grow horizontally into an unreadable "Pyramid of Doom":

```javascript
// ❌ CALLBACK HELL: Deep nesting, difficult error handling, fragile flow
getUser(1, function(err, user) {
  if (err) return handleError(err);
  getOrders(user.id, function(err, orders) {
    if (err) return handleError(err);
    getOrderDetails(orders[0].id, function(err, details) {
      if (err) return handleError(err);
      getShippingStatus(details.shippingId, function(err, status) {
        if (err) return handleError(err);
        console.log('Final Status:', status);
      });
    });
  });
});
```

### Mitigating Callback Hell:
1. **Modularization:** Extract nested callbacks into independent, named functions.
2. **Modern Transition:** In Lesson 12, we will replace this pattern entirely with **Promises** and **`async/await`**.

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Anonymous inline callback nesting
setTimeout(function() {
  doTaskA(function() {
    doTaskB(function() {
      console.log('Done');
    });
  });
}, 1000);

// ✅ GOOD: Decomposing into named, testable handlers
function handleTaskB() {
  console.log('Done');
}

function handleTaskA() {
  doTaskB(handleTaskB);
}

setTimeout(() => doTaskA(handleTaskA), 1000);
```

---

## 📝 8. Summary & Next Steps
- Functions can be passed as **arguments** and invoked later as **callbacks**.
- Synchronous callbacks block execution; asynchronous callbacks run after the call stack empties.
- Always clear repeating intervals with **`clearInterval()`** to prevent memory leaks.
- Always check the **first argument (`err`)** in error-first callbacks.
- In the next lesson, we will step into **The Browser Environment: Window, Document, and Web APIs**.
