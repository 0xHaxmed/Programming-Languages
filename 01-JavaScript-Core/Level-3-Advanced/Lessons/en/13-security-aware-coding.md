# 13 - Security-Aware Coding: Sinks, Sanitization & Defensive Engineering

> 🌐 **Language / اللغة:** [English 🇬🇧](./13-security-aware-coding.md) | [العربية 🇸🇦](../ar/13-security-aware-coding.md)

---

## 🎯 1. Overview & Learning Objectives

Security is not an afterthought to be retrofitted onto completed software; it is a fundamental architectural discipline. As client-side JavaScript applications handle sensitive user state, authentication tokens, payment processing, and cryptographic keys, developers must write code that is secure by default.

By the end of this capstone lesson, you will master:
1. Banning and eliminating **Dynamic Code Evaluation Sinks** (`eval`, `new Function`, string-based timers).
2. Defending against **DOM-based Cross-Site Scripting (DOM XSS)** using safe properties and the modern **W3C Trusted Types API**.
3. Hardening objects and recursive merges against **Prototype Pollution**.
4. Securing cross-origin communication with strict **`postMessage`** validation.
5. Leveraging the **Web Crypto API** for cryptographically secure randomness and token operations.

---

## 🚫 2. Eliminating Dynamic Code Evaluation Sinks

Dynamic evaluation converts arbitrary strings into executable code. If an attacker controls any portion of that string, they achieve instant arbitrary code execution.

### Dangerous Sinks to Ban:
```javascript
// ❌ CRITICAL SINK: eval executes string as code
eval(userInput);

// ❌ CRITICAL SINK: new Function parses and executes body
const calculator = new Function('a', 'b', `return ${formulaString};`);

// ❌ DANGEROUS: String arguments to setTimeout / setInterval
setTimeout(`alert("${message}")`, 1000); // Internally invokes eval!
```

### Safe, Secure Alternatives:
```javascript
// ✅ SAFE: Pass explicit function references to timers
setTimeout(() => {
  alert(message);
}, 1000);

// ✅ SAFE: Use structured JSON parsing instead of eval
const config = JSON.parse(rawConfigString);

// ✅ SAFE: Dynamic calculation via dispatch table instead of new Function
const mathOperations = {
  '+': (a, b) => a + b,
  '-': (a, b) => a - b,
  '*': (a, b) => a * b,
  '/': (a, b) => (b === 0 ? 0 : a / b)
};

function safeCompute(operator, a, b) {
  const handler = mathOperations[operator];
  if (!handler) throw new Error('Unsupported operator');
  return handler(a, b);
}
```

---

## 🛡️ 3. Defending Sinks with Trusted Types & Safe DOM APIs

The most prevalent client-side vulnerability is **DOM XSS**. It occurs when untrusted data (like `location.hash` or query parameters) is written directly into an execution sink (like `element.innerHTML` or `script.src`).

### Safe vs Dangerous DOM Assignments:
| Sink / Property | Safety Level | Mechanism |
| :--- | :--- | :--- |
| **`element.textContent`** | **Safe** | Encodes all HTML tags as literal text; no parser invocation. |
| **`element.setAttribute('src', url)`** | **Dangerous** | Can execute code if `url` begins with `javascript:`. |
| **`element.innerHTML`** | **Dangerous** | Parses string into DOM nodes; executes embedded tags/events. |

### Enforcing the W3C Trusted Types API:
Trusted Types restricts dangerous sinks from accepting raw strings. Sinks only accept certified `TrustedHTML` objects created via validated policies:

```javascript
// 1. Feature detection and policy creation:
let sanitizePolicy = {
  createHTML: (string) => string // Fallback
};

if (window.trustedTypes && window.trustedTypes.createPolicy) {
  sanitizePolicy = window.trustedTypes.createPolicy('dom-sanitizer', {
    createHTML: (untrustedInput) => {
      // Clean untrusted string using DOMPurify
      return DOMPurify.sanitize(untrustedInput);
    }
  });
}

// 2. Safe sink assignment:
const userBio = '<p>Hello <img src="x" onerror="alert(1)"></p>';
const cleanHtml = sanitizePolicy.createHTML(userBio);

// Browser allows this because cleanHtml is a verified TrustedHTML instance:
document.getElementById('profile-bio').innerHTML = cleanHtml;
```

---

## 🔒 4. Hardening Against Prototype Pollution

Prototype Pollution occurs when recursive object merges or path-assignment functions allow malicious inputs to overwrite properties on `Object.prototype`.

### Defensive Strategies:

#### A. Use Null-Prototype Objects for Lookup Dictionaries:
```javascript
// Ordinary object inherits from Object.prototype:
const normalDict = {};
console.log('toString' in normalDict); // true

// Null-prototype object has NO prototype chain:
const safeLookup = Object.create(null);
console.log(safeLookup.__proto__); // undefined
console.log('toString' in safeLookup); // false! Safe from pollution!
```

#### B. Safe Recursive Deep Merge:
```javascript
function safeDeepMerge(target, source) {
  for (const key of Object.keys(source)) {
    // 🛡️ BLOCK POLLUTION KEYS:
    if (key === '__proto__' || key === 'constructor' || key === 'prototype') {
      continue; // Skip dangerous prototype mutation vectors!
    }

    if (
      source[key] &&
      typeof source[key] === 'object' &&
      !Array.isArray(source[key])
    ) {
      if (!target[key]) target[key] = {};
      safeDeepMerge(target[key], source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}
```

#### C. Freezing the Global Prototype (Defensive Barrier):
```javascript
// In critical high-security environments, freeze Object.prototype during bootstrap:
Object.freeze(Object.prototype);
```

---

## 📬 5. Secure Cross-Origin Messaging (`postMessage`)

`window.postMessage` enables communication between frames and popups across origins. Insecure implementations accept messages from any sender or dispatch to arbitrary listeners.

### The Two PostMessage Rules:
1. **Never send with wildcard target origin (`'*'`) if data is sensitive:**
   ```javascript
   // ❌ DANGEROUS: Any domain can read this token if framed
   popup.postMessage({ token: secretToken }, '*');

   // ✅ SECURE: Specify the exact target origin
   popup.postMessage({ token: secretToken }, 'https://auth.company.com');
   ```

2. **Always validate `event.origin` on receiving listeners:**
   ```javascript
   // Receiver script:
   window.addEventListener('message', (event) => {
     // 🛡️ STRICT ORIGIN CHECK:
     const TRUSTED_ORIGIN = 'https://auth.company.com';
     if (event.origin !== TRUSTED_ORIGIN) {
       console.warn(`Blocked untrusted postMessage from origin: ${event.origin}`);
       return;
     }

     // Validate payload shape before acting:
     const { type, payload } = event.data || {};
     if (type === 'AUTH_COMPLETE' && typeof payload?.token === 'string') {
       handleAuthSuccess(payload.token);
     }
   });
   ```

---

## 🎲 6. Cryptography & Randomness: Web Crypto API

Never use `Math.random()` for security purposes (passwords, session IDs, CSRF tokens, or cryptographic nonces). `Math.random()` is a pseudo-random number generator (PRNG) with predictable seeds.

### Generating Cryptographically Secure Random Values:
```javascript
// Generate secure random UUID:
const secureId = crypto.randomUUID();
console.log('Cryptographically secure UUID:', secureId);

// Generate random cryptographic bytes:
function generateSecureToken(length = 32) {
  const buffer = new Uint8Array(length);
  crypto.getRandomValues(buffer);
  return Array.from(buffer, (byte) => byte.toString(16).padStart(2, '0')).join('');
}

console.log('Secure Token:', generateSecureToken(16));
```

---

## 🧼 7. Clean Code: Security Checklist

```javascript
// ❌ BAD: Dynamic redirect to arbitrary user-supplied URL (Open Redirect / XSS)
const returnUrl = new URLSearchParams(window.location.search).get('next');
window.location.href = returnUrl; // Attacker supplies "javascript:stealCookies()"!

// ✅ GOOD: Validate against a strict allowlist or internal path requirement
function safeRedirect(targetUrl) {
  try {
    const parsed = new URL(targetUrl, window.location.origin);
    // Ensure redirect stays strictly on our own trusted origin:
    if (parsed.origin === window.location.origin) {
      window.location.href = parsed.href;
    } else {
      window.location.href = '/dashboard';
    }
  } catch {
    window.location.href = '/dashboard';
  }
}
```

---

## 🎓 8. Congratulations! Core JavaScript Mastery Complete

You have successfully completed the entire **3-Level JavaScript Core Curriculum**:
- **Level 1 (Basic):** Foundations of syntax, data types, operators, conditionals, and primitives.
- **Level 2 (Foundation):** Closures, DOM manipulation, asynchronous programming, Web APIs, and storage.
- **Level 3 (Advanced):** V8 memory & execution internals, Event loop concurrency, Classes & ESM, testing, code review, modern bundling, and security-aware coding.

From here, you are equipped with the deep foundational rigor required to architect resilient enterprise frontend applications, lead technical code reviews, or transition seamlessly into our specialized **JavaScript-Web-Pentesting** track to audit complex client-side applications!
