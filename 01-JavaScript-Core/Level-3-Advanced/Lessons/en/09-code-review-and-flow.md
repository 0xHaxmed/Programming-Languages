# 09 - Code Review Architecture: Data Flow, Refactoring & Code Smells

> 🌐 **Language / اللغة:** [English 🇬🇧](./09-code-review-and-flow.md) | [العربية 🇸🇦](../ar/09-code-review-and-flow.md)

---

## 🎯 1. Overview & Learning Objectives

Writing code that merely functions is only the baseline in software engineering. In enterprise teams, longevity, maintainability, auditability, and cognitive load dictate system success. Professional code reviews systematically trace data flow from untrusted boundaries, reduce cyclomatic complexity, and excise anti-patterns before code merges into production.

By the end of this lesson, you will master:
1. Conducting **Static Data Flow Analysis** (tracing data from untrusted sources to critical sinks).
2. Applying core architectural patterns: **Single Responsibility (SRP)** and **Separation of Concerns (SoC)** in modern JavaScript.
3. Measuring and dramatically reducing **Cyclomatic Complexity**.
4. Identifying and surgically refactoring classic **JavaScript Code Smells** (God Functions, Primitive Obsession, Boolean Flag Arguments, and Mutation Leaks).
5. Executing an enterprise **Airbnb-Style Code Review Checklist**.

---

## 🌊 2. Static Data Flow Analysis: Sources to Sinks

Data flow analysis tracks how information enters a program, transforms across layers, and finally lands at a destination:

```
[ UNTRUSTED SOURCE ]           [ TRANSFORMATION PIPELINE ]              [ CRITICAL SINK ]
User Input / URL query  --->  Sanitization -> Parsing -> Validation  --->  DOM / Database / API
(Potential Poison)                  (Defensive Boundary)                  (High-Impact Action)
```

### The Three Rules of Secure & Clean Data Flow:
1. **Never trust data at boundaries:** Any payload received from HTTP query parameters, form inputs, `postMessage`, or WebSockets is inherently untrusted.
2. **Validate immediately, parse strictly:** Validate shapes using schemas (e.g., Zod or strict validator functions) right at the edge before internal logic consumes it.
3. **Keep sinks explicit and isolated:** Keep data persistence (e.g., `localStorage.setItem`, database queries) behind dedicated repository interfaces rather than scattered throughout UI event listeners.

---

## 👃 3. Classic JavaScript Code Smells & Refactoring

### A. Code Smell: The "God Function" (Violating SRP)
A function that validates input, calls an API, updates local storage, manipulates the DOM, and sends analytics.

```javascript
// ❌ BAD: God Function doing 5 distinct responsibilities
async function handleUserSignup(e) {
  e.preventDefault();
  const email = document.getElementById('email').value;
  const password = document.getElementById('pass').value;

  if (!email.includes('@') || password.length < 8) {
    alert('Invalid inputs');
    return;
  }

  const res = await fetch('/api/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  const data = await res.json();

  localStorage.setItem('user_token', data.token);
  document.getElementById('welcome').innerText = `Welcome, ${data.user.name}`;
  analytics.track('signup_success', { userId: data.user.id });
}
```

```javascript
// ✅ GOOD: Decoupled into small, testable, single-purpose units
// 1. Validation logic (Pure)
function validateSignupForm(email, password) {
  if (!email || !email.includes('@')) return { valid: false, error: 'Invalid email' };
  if (!password || password.length < 8) return { valid: false, error: 'Password too short' };
  return { valid: true };
}

// 2. Network Client (Data access)
async function signupUserApi(credentials) {
  const res = await fetch('/api/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(credentials)
  });
  if (!res.ok) throw new Error('Signup failed');
  return res.json();
}

// 3. Orchestration controller (Wires UI to Domain)
async function onSignupSubmitted(formElements) {
  const { email, password } = formElements;
  const validation = validateSignupForm(email, password);
  if (!validation.valid) return ui.showError(validation.error);

  try {
    const session = await signupUserApi({ email, password });
    authStorage.setToken(session.token);
    ui.renderWelcome(session.user.name);
    analytics.track('signup_success', { userId: session.user.id });
  } catch (err) {
    ui.showError(err.message);
  }
}
```

---

### B. Code Smell: Boolean Flag Arguments
Passing raw booleans (`fn(user, true, false, true)`) creates unreadable call sites where reviewers have no idea what `true` or `false` controls without inspecting function definitions.

```javascript
// ❌ BAD: What do true and false mean here?
createUser('Sam', true, false, true);

// ✅ GOOD: Refactor to a descriptive Options Object with defaults
function createUser(name, { isAdmin = false, sendWelcomeEmail = true, requireMfa = false } = {}) {
  // ...
}

// Crystal clear at call site:
createUser('Sam', {
  isAdmin: true,
  sendWelcomeEmail: false,
  requireMfa: true
});
```

---

### C. Code Smell: Hidden In-Place Object Mutation
Functions that mutate input arguments cause subtle bugs across concurrent or asynchronous callers.

```javascript
// ❌ BAD: Mutates input argument in place
function applyDiscount(cart, discountPercent) {
  cart.items.forEach((item) => {
    item.price = item.price * (1 - discountPercent); // In-place mutation!
  });
  cart.discountApplied = true;
  return cart;
}

// ✅ GOOD: Returns a new immutable object
function applyDiscountPure(cart, discountPercent) {
  const multiplier = 1 - discountPercent;
  return {
    ...cart,
    discountApplied: true,
    items: cart.items.map((item) => ({
      ...item,
      price: Math.round(item.price * multiplier * 100) / 100
    }))
  };
}
```

---

## 📉 4. Taming Cyclomatic Complexity (Guard Clauses)

**Cyclomatic complexity** measures the number of linearly independent paths through program source code. High complexity (> 10) directly correlates with high defect rates and low testability.

### The Arrow Anti-Pattern (Deep Nested `if/else`):
```javascript
// ❌ BAD: Deep nested pyramid of doom (High cyclomatic complexity)
function processPayment(user, order) {
  if (user) {
    if (user.isActive) {
      if (order) {
        if (order.total > 0) {
          if (user.balance >= order.total) {
            executeCharge(user, order);
            return { success: true };
          } else {
            return { success: false, reason: 'Insufficient funds' };
          }
        } else {
          return { success: false, reason: 'Invalid total' };
        }
      } else {
        return { success: false, reason: 'Missing order' };
      }
    } else {
      return { success: false, reason: 'Inactive account' };
    }
  } else {
    return { success: false, reason: 'Missing user' };
  }
}
```

### The Solution: Guard Clauses (Early Return):
```javascript
// ✅ GOOD: Flat, readable, easy to test each branch individually
function processPayment(user, order) {
  if (!user) return { success: false, reason: 'Missing user' };
  if (!user.isActive) return { success: false, reason: 'Inactive account' };
  if (!order) return { success: false, reason: 'Missing order' };
  if (order.total <= 0) return { success: false, reason: 'Invalid total' };
  if (user.balance < order.total) return { success: false, reason: 'Insufficient funds' };

  executeCharge(user, order);
  return { success: true };
}
```

---

## 📋 5. Enterprise Airbnb-Style Code Review Checklist

When reviewing pull requests, follow this 4-pillar evaluation:

### 1. Correctness & Architecture
- [ ] Does every function adhere to the Single Responsibility Principle?
- [ ] Are external dependencies injected or mocked rather than hardcoded?
- [ ] Is asynchronous code using `try/catch` and handling rejection paths?
- [ ] Are inputs validated before being consumed by business logic?

### 2. Readability & Maintainability
- [ ] Are variable and function names intentions-revealing (e.g., `isUserAuthenticated` vs `chk`)?
- [ ] Are "magic numbers" extracted to named constants (`const MAX_LOGIN_ATTEMPTS = 5`)?
- [ ] Are multi-argument functions converted to named option objects?
- [ ] Are nested conditions replaced with early guard clauses?

### 3. Performance & Memory
- [ ] Are event listeners properly removed when components unmount?
- [ ] Are heavy loops or scroll handlers throttled or debounced?
- [ ] Does the code avoid continuous object allocations inside hot loops?

### 4. Security & Defensive Rigor
- [ ] Is all dynamic HTML insertion sanitized (avoiding raw `innerHTML`)?
- [ ] Are secrets, private keys, or API tokens absent from source files?
- [ ] Is input bounded to prevent ReDoS or infinite loops?

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Magic numbers and cryptic variable names
if (u.st === 1 && d.diff > 86400000) {
  p(u.id);
}

// ✅ GOOD: Expressive constants and descriptive naming
const MILLISECONDS_IN_A_DAY = 24 * 60 * 60 * 1000;
const USER_STATUS_ACTIVE = 1;

const isSubscriptionOverdue = user.status === USER_STATUS_ACTIVE && timeDifference > MILLISECONDS_IN_A_DAY;
if (isSubscriptionOverdue) {
  promptAccountRenewal(user.id);
}
```

---

## 📝 7. Summary & Next Steps

- Analyze data flow from **sources** (inputs, APIs) to **sinks** (DOM, storage, database).
- Eliminate **God Functions** by strictly separating validation, network data access, and UI orchestration.
- Replace confusing boolean parameters with **Options Objects**.
- Avoid in-place object mutations; prefer immutable object transformations with spread operators.
- Flatten nested `if/else` ladders into clean, readable **Guard Clauses**.
- Use the 4-pillar code review checklist (Correctness, Readability, Performance, Security) to maintain pristine repository health.
- In the next lesson, we will dive into **Testing Architectures: Assertions, Mocking & the Built-in Node.js Test Runner**.
