# 12 - Asynchronous Foundations: Promises & async/await

> 🌐 **Language / اللغة:** [English 🇬🇧](./12-async-basics.md) | [العربية 🇸🇦](../ar/12-async-basics.md)

---

## 🎯 1. Overview & Learning Objectives

JavaScript is a single-threaded language with a non-blocking event loop. Handling operations that take time—such as network queries, file reading, or timers—requires an asynchronous programming model.

Historically handled by nested callbacks, modern JavaScript uses **Promises** and the syntactic elegance of **`async` / `await`** to write asynchronous logic that reads like synchronous code.

By the end of this lesson, you will master:
1. The 3 lifecycle states of a **Promise** (`Pending`, `Fulfilled`, `Rejected`).
2. Creating and chaining Promises with `.then()`, `.catch()`, and `.finally()`.
3. Writing readable, non-blocking asynchronous code using `async` and `await`.
4. Robust error handling using `try...catch` blocks.
5. Optimizing performance: **Sequential execution** vs **Parallel execution** with `Promise.all()` and `Promise.allSettled()`.

---

## ⏳ 2. What is a Promise?

A **Promise** is a JavaScript object representing the eventual completion (or failure) of an asynchronous operation and its resulting value.

```
                     +---------------------------------------+
                     |                PROMISE                |
                     |           [ State: Pending ]          |
                     +-------------------+-------------------+
                                         |
                       +-----------------+-----------------+
                       |                                   |
                       v (Operation Succeeds)              v (Operation Fails)
             +--------------------+              +--------------------+
             | [ State: Fulfilled]|              | [ State: Rejected ]|
             | Value: Result Data |              | Value: Error Object|
             +--------------------+              +--------------------+
                       |                                   |
                       v                                   v
                    .then()                             .catch()
```

### The 3 Promise States:
1. **`Pending`:** Initial state; neither fulfilled nor rejected.
2. **`Fulfilled`:** The operation completed successfully (`resolve(data)` was called).
3. **`Rejected`:** The operation failed (`reject(error)` was called).

---

## 🛠️ 3. Creating & Consuming Promises

### Creating a Promise:
```javascript
function delay(ms) {
  return new Promise((resolve, reject) => {
    if (ms < 0) {
      reject(new Error('Delay must be a positive number'));
      return;
    }
    setTimeout(() => {
      resolve(`Waited ${ms}ms successfully`);
    }, ms);
  });
}
```

### Consuming with `.then()`, `.catch()`, `.finally()`:
```javascript
delay(1000)
  .then(message => {
    console.log('Success:', message);
    return delay(500); // Returning another Promise chains cleanly!
  })
  .then(secondMessage => {
    console.log('Second step complete:', secondMessage);
  })
  .catch(error => {
    console.error('Error encountered anywhere in chain:', error.message);
  })
  .finally(() => {
    console.log('Finished (runs regardless of success or failure)');
  });
```

---

## ✨ 4. Modern `async` and `await`

Introduced in ES2017, `async` and `await` provide syntactic sugar on top of Promises, eliminating `.then()` chains entirely.

### 1. The `async` Keyword:
Placed before a function declaration. It guarantees that the function **always returns a Promise**:
```javascript
async function getStatus() {
  return 'System Operational'; // Automatically wrapped in Promise.resolve()
}
getStatus().then(console.log); // "System Operational"
```

### 2. The `await` Keyword:
Can only be used inside an `async` function. It pauses execution of that specific function until the awaited Promise settles, unpacking its resolved value:
```javascript
async function runSequence() {
  try {
    console.log('Starting...');
    const result1 = await delay(1000);
    console.log(result1);

    const result2 = await delay(500);
    console.log(result2);

    console.log('Sequence complete!');
  } catch (error) {
    console.error('Caught error via try/catch:', error.message);
  } finally {
    console.log('Cleanup code here');
  }
}

runSequence();
```

---

## 🚀 5. Performance: Sequential vs Parallel Execution

A frequent junior mistake is using `await` sequentially on independent tasks, doubling or tripling load times:

### A. The Slow Way: Sequential Execution (Waterfall)
```javascript
// ❌ SLOW: Each request waits for the previous one to finish completely
async function loadDashboardSlow() {
  const user = await fetch('/api/user');       // Takes 1 second
  const feed = await fetch('/api/feed');       // Takes 1 second
  const metrics = await fetch('/api/metrics'); // Takes 1 second
  // Total Time: ~3 seconds!
}
```

### B. The Fast Way: Parallel Execution with `Promise.all()`
If the operations do not depend on each other, trigger them concurrently:
```javascript
// ✅ FAST: All requests fire simultaneously!
async function loadDashboardFast() {
  // Promise.all runs all promises in parallel:
  const [userRes, feedRes, metricsRes] = await Promise.all([
    fetch('/api/user'),
    fetch('/api/feed'),
    fetch('/api/metrics')
  ]);
  // Total Time: ~1 second (the duration of the slowest single request)!
}
```

> [!WARNING]
> **`Promise.all` Fails Fast:**  
> If **any single** Promise rejects, the entire `Promise.all` immediately rejects.  
> If you need all results even if some fail, use **`Promise.allSettled()`**:

```javascript
const results = await Promise.allSettled([
  fetch('/api/reliable-service'),
  fetch('/api/flaky-service')
]);

results.forEach(res => {
  if (res.status === 'fulfilled') {
    console.log('Success value:', res.value);
  } else {
    console.warn('Failed reason:', res.reason);
  }
});
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Mixing raw .then() with async/await
async function getUserData() {
  const user = await fetchUser().then(res => res.json()); // Inconsistent and messy!
  return user;
}

// ✅ GOOD: Consistent async/await throughout
async function getUserData() {
  const response = await fetchUser();
  const user = await response.json();
  return user;
}

// ❌ BAD: Forgetting try/catch around await calls (Uncaught Promise Rejection!)
async function deleteItem(id) {
  const res = await apiDelete(id); // Crashes if network fails!
}

// ✅ GOOD: Clean error boundary
async function deleteItem(id) {
  try {
    const res = await apiDelete(id);
    return res;
  } catch (err) {
    logger.error('Failed to delete item', { id, error: err });
    throw err; // Re-throw or handle gracefully
  }
}
```

---

## 📝 7. Summary & Next Steps
- A **Promise** has 3 states: `Pending`, `Fulfilled`, and `Rejected`.
- `async` marks a function as asynchronous; `await` unpacks a Promise's resolved value.
- Always wrap `await` calls in **`try...catch`** to handle rejections cleanly.
- Use **`Promise.all()`** for independent concurrent operations to dramatically improve performance.
- Use **`Promise.allSettled()`** when you want to inspect both successes and failures.
- In the next lesson, we will master client-side storage: **localStorage, sessionStorage, and Cookies**.
