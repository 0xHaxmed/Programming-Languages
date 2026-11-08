# 04 - Advanced Functions: Currying, Composition & Rate-Limiting

> 🌐 **Language / اللغة:** [English 🇬🇧](./04-advanced-functions.md) | [العربية 🇸🇦](../ar/04-advanced-functions.md)

---

## 🎯 1. Overview & Learning Objectives

Functional programming paradigms allow engineers to build software out of small, deterministic, composable building blocks. In enterprise architectures, functions are orchestrated into data-processing pipelines, pre-configured via partial application, and rate-limited to protect performance.

By the end of this lesson, you will master:
1. Writing **Pure Functions** and eliminating side effects.
2. Building data-transformation pipelines with **`pipe()`** and **`compose()`**.
3. Converting multi-argument functions into unary sequences with **Currying**.
4. The mechanics and implementation of **Debouncing** (delaying execution until activity ceases).
5. The mechanics and implementation of **Throttling** (capping execution frequency).

---

## 🧼 2. Pure Functions & Immutability

A **Pure Function** satisfies two strict criteria:
1. **Deterministic:** Given the identical inputs, it will **always** return the exact same output.
2. **Zero Side Effects:** It does not mutate external state, modify input arguments, or trigger observable I/O (like network requests or DOM writes).

```javascript
// ❌ IMPURE: Mutates external variable and depends on mutable time state
let minimumAge = 18;
function canVote(user) {
  user.checkedAt = Date.now(); // Side Effect: Mutates argument!
  return user.age >= minimumAge; // Impure: Depends on external minimumAge!
}

// ✅ PURE: Self-contained, immutable, deterministic
function canVotePure(userAge, requiredAge = 18) {
  return userAge >= requiredAge;
}
```

---

## 🔗 3. Function Composition (`pipe` & `compose`)

Function composition combines multiple single-purpose functions to create a new, complex transformation. Data flows seamlessly through each stage of the pipeline:

```
Input Data  ---> [ Function 1 ] ---> [ Function 2 ] ---> [ Function 3 ] ---> Output Data
```

### Building a `pipe()` Helper:
`pipe()` processes functions from **left to right** (natural reading order):

```javascript
// High-performance pipe utility using Array.prototype.reduce:
const pipe = (...functions) => (initialValue) => {
  return functions.reduce((currentValue, currentFunction) => {
    return currentFunction(currentValue);
  }, initialValue);
};

// Pipeline building blocks:
const trimString = (str) => str.trim();
const normalizeUsername = (str) => str.toLowerCase();
const addPrefix = (str) => `@${str}`;

// Compose into a single transformation pipeline:
const formatHandle = pipe(
  trimString,
  normalizeUsername,
  addPrefix
);

console.log(formatHandle('   ALEX_DEV99   ')); // "@alex_dev99"
```

---

## 🍛 4. Currying & Partial Application

**Currying** is the technique of converting a function that takes multiple arguments `fn(a, b, c)` into a sequence of nested unary functions that each take one argument: `fn(a)(b)(c)`.

### Practical Currying Example: Configurable Logger
```javascript
// Uncurried version:
// function log(level, timestamp, message) { ... }

// Curried version:
const log = (level) => (prefix) => (message) => {
  console.log(`[${level}] [${prefix}]: ${message}`);
};

// Specialize functions by locking in arguments early:
const errorLog = log('ERROR');
const authErrorLog = errorLog('AUTH_SERVICE');

// Final invocation:
authErrorLog('Invalid token signature'); 
// Output: "[ERROR] [AUTH_SERVICE]: Invalid token signature"
```

### Generic Auto-Curry Utility:
```javascript
function curry(fn) {
  return function curried(...args) {
    // If sufficient arguments have been passed, invoke the underlying function:
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    // Otherwise, return a function collecting remaining arguments:
    return (...nextArgs) => curried.apply(this, [...args, ...nextArgs]);
  };
}

const addThreeNumbers = (a, b, c) => a + b + c;
const curriedAdd = curry(addThreeNumbers);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
```

---

## ⏱️ 5. Rate-Limiting: Debounce vs Throttle

High-frequency events (like `scroll`, `resize`, or `keyup`) fire hundreds of times per second. Uncontrolled handling crashes browser performance.

```
EVENT STREAM:  |||||||||||||||||||||||||||||||| (Rapid events)

DEBOUNCE:                                    |   (Fires ONCE after user stops)
THROTTLE:      |       |       |       |       |   (Fires AT REGULAR INTERVALS)
```

### A. Debounce: "Wait until the user pauses"
Ideal for: **Search autocomplete inputs**, form autosave, window resize recalculations.

```javascript
function debounce(callback, delayMs = 300) {
  let timerId = null;

  return function (...args) {
    // Reset timer on every keystroke:
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      callback.apply(this, args);
    }, delayMs);
  };
}

// Usage in live search:
const handleSearch = debounce((query) => {
  console.log('Dispatching API search for:', query);
}, 400);

// Typing "JavaScript" triggers handleSearch only once 400ms after user stops typing!
```

### B. Throttle: "Execute at most once every X milliseconds"
Ideal for: **Infinite scrolling**, scroll-position progress bars, mouse-move canvas tracking.

```javascript
function throttle(callback, limitMs = 250) {
  let inThrottle = false;

  return function (...args) {
    if (!inThrottle) {
      callback.apply(this, args);
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false;
      }, limitMs);
    }
  };
}

// Usage in scroll listener:
window.addEventListener('scroll', throttle(() => {
  console.log('Checking scroll position for infinite loading...');
}, 200));
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Impure function with hidden dependencies
let taxRate = 0.15;
function calculateInvoiceTotal(subtotal) {
  return subtotal + (subtotal * taxRate); // Hidden external variable dependency!
}

// ✅ GOOD: Explicit, pure function configuration
function calculateInvoiceTotal(subtotal, taxRate = 0.15) {
  return subtotal + (subtotal * taxRate);
}

// ❌ BAD: Hand-rolling custom timer state inline in UI components
input.addEventListener('keyup', (e) => {
  clearTimeout(window.searchTimer);
  window.searchTimer = setTimeout(() => sendQuery(e.target.value), 300);
});

// ✅ GOOD: Using clean, reusable debounce utility
input.addEventListener('input', debounce((e) => sendQuery(e.target.value), 300));
```

---

## 📝 7. Summary & Next Steps
- **Pure Functions** are deterministic and have zero side effects.
- Use **`pipe()`** to build clean data processing pipelines.
- **Currying** transforms multi-parameter functions into sequences of unary functions for reusable specialization.
- Use **`debounce()`** when you want execution to wait until activity pauses.
- Use **`throttle()`** to limit the maximum execution frequency of continuous streams.
- In the next lesson, we will master **Advanced Asynchronous JavaScript: Combinators, Timeouts & Pipeline Controls**.
