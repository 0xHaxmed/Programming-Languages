# 07 - Classes & Modules: Encapsulation, Private Fields & ESM Architecture

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-classes-and-modules.md) | [العربية 🇸🇦](../ar/07-classes-and-modules.md)

---

## 🎯 1. Overview & Learning Objectives

Modern enterprise JavaScript relies on two architectural pillars for code organization: **ES6 Classes** for object-oriented modeling and **ECMAScript Modules (ESM)** for code isolation and distribution.

While classes in JavaScript are syntactic sugar over prototypal delegation, modern additions such as **private class fields (`#`)** provide true language-level encapsulation. Simultaneously, ESM has become the universal standard across browsers and Node.js.

By the end of this lesson, you will master:
1. Deconstructing ES6 `class` syntax down to its underlying prototype mechanics.
2. Subclassing with `extends`, `super()`, and the `super` reference.
3. Enforcing hard encapsulation with **Private Fields (`#field`)** and **Private Methods**.
4. Static properties and initialization blocks (`static { ... }`).
5. ES Module semantics: Static imports, Named vs Default exports, Dynamic `import()`, and Top-Level `await`.
6. Diagnosing and resolving **Circular Dependency** hazards.

---

## 🏛️ 2. ES6 Classes: Syntactic Sugar Over Prototypes

Under the hood, JavaScript does not have traditional classical inheritance like Java or C++. A `class` declaration creates a constructor function and binds its methods to `ClassName.prototype`.

```javascript
// Modern Class Syntax:
class BankAccount {
  constructor(owner, initialBalance = 0) {
    this.owner = owner;
    this.balance = initialBalance;
  }

  deposit(amount) {
    if (amount <= 0) throw new Error('Deposit must be positive');
    this.balance += amount;
    return this.balance;
  }
}

// Exactly equivalent under the hood to ES5 Prototypal construction:
function BankAccountES5(owner, initialBalance = 0) {
  this.owner = owner;
  this.balance = initialBalance;
}
BankAccountES5.prototype.deposit = function (amount) {
  if (amount <= 0) throw new Error('Deposit must be positive');
  this.balance += amount;
  return this.balance;
};

// Proof:
const account = new BankAccount('Alice', 100);
console.log(typeof BankAccount); // "function"
console.log(account.__proto__ === BankAccount.prototype); // true
console.log(BankAccount.prototype.deposit === account.deposit); // true
```

### Key Differences Between `class` and Constructor Functions:
1. **No Hoisting:** Classes are in the Temporal Dead Zone (TDZ) and cannot be invoked before declaration.
2. **Strict Mode by Default:** All code inside class bodies executes automatically in `'use strict'`.
3. **Cannot be called without `new`:** Invoking `BankAccount()` throws `TypeError: Class constructor BankAccount cannot be invoked without 'new'`.

---

## 🔒 3. True Encapsulation: Private Fields & Methods (`#`)

Historically, developers used an underscore prefix (e.g., `_balance`) to denote private intent. However, this provided **zero actual protection**—any code could still inspect, overwrite, or mutate `_balance`.

ECMAScript introduced **Hard Private Fields** prefixed with `#`. They are completely inaccessible from outside the class instance:

```javascript
class SecureVault {
  // 1. Hard Private Fields (declared at the top of the class)
  #encryptionKey;
  #accessPin;
  #auditLogs = [];

  constructor(secretKey, pin) {
    this.#encryptionKey = secretKey;
    this.#accessPin = pin;
  }

  // 2. Private Method
  #recordAccess(action, success) {
    this.#auditLogs.push({ action, success, timestamp: Date.now() });
  }

  // 3. Public Method
  unlock(pinAttempt) {
    if (pinAttempt !== this.#accessPin) {
      this.#recordAccess('unlock', false);
      throw new Error('Authentication failed: Invalid PIN');
    }

    this.#recordAccess('unlock', true);
    return `Vault Decrypted: [Key: ${this.#encryptionKey.slice(0, 4)}****]`;
  }

  // Getter for read-only metadata:
  get logCount() {
    return this.#auditLogs.length;
  }
}

const myVault = new SecureVault('SUPER_SECRET_TOKEN_999', '4321');
console.log(myVault.unlock('4321')); // "Vault Decrypted: [Key: SUPE****]"

// ❌ ATTEMPTS TO ACCESS PRIVATE FIELDS FAIL AT RUNTIME & PARSE TIME:
// console.log(myVault.#accessPin); 
// SyntaxError: Private field '#accessPin' must be declared in an enclosing class

// Property iteration and Object.keys completely ignore private fields:
console.log(Object.keys(myVault)); // []
```

---

## 🧬 4. Inheritance & Static Initialization Blocks

```javascript
class PaymentGateway {
  static #supportedCurrencies = new Set(['USD', 'EUR', 'GBP']);

  // Static initialization block (runs once when the class is defined):
  static {
    if (process.env.ENABLE_JPY === 'true') {
      PaymentGateway.#supportedCurrencies.add('JPY');
    }
  }

  constructor(merchantId) {
    this.merchantId = merchantId;
  }

  static isCurrencySupported(currency) {
    return PaymentGateway.#supportedCurrencies.has(currency);
  }
}

class StripeGateway extends PaymentGateway {
  #apiKey;

  constructor(merchantId, apiKey) {
    // MUST call super() before accessing 'this':
    super(merchantId);
    this.#apiKey = apiKey;
  }

  processPayment(amount, currency) {
    if (!PaymentGateway.isCurrencySupported(currency)) {
      throw new Error(`Unsupported currency: ${currency}`);
    }
    return `Processed ${amount} ${currency} via Stripe for merchant ${this.merchantId}`;
  }
}
```

---

## 📦 5. ES Modules (ESM) Architecture

ECMAScript Modules (ESM) are the official, standard modular system of JavaScript. ESM files are loaded asynchronously, parsed statically, and execute as isolated singletons.

### A. Named vs Default Exports:
```javascript
// mathUtils.js (Module Definition)
export const PI = 3.14159265359;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

// Preferred: Export an object or use default sparingly
export default class Calculator {
  // ...
}
```

```javascript
// app.js (Module Consumption)
// 1. Importing named members:
import Calculator, { add, multiply, PI } from './mathUtils.js';

// 2. Importing all named exports under a namespace:
import * as MathOps from './mathUtils.js';
console.log(MathOps.add(5, 10));
```

### B. Dynamic Imports for On-Demand Code Splitting:
Static `import` statements must be placed at the top level of a file. When you only need a module conditionally (e.g., when a user clicks "Export to Excel"), use **dynamic `import()`**:

```javascript
// Returns a Promise that resolves with the module namespace
async function exportReport(reportData) {
  const { generateExcelFile } = await import('./heavyExcelExportBundle.js');
  generateExcelFile(reportData);
}
```

### C. Top-Level `await`:
In standard ES modules, you can use `await` at the top level without wrapping it in an `async` function. This is invaluable for establishing database connections or loading remote configurations during module initialization:

```javascript
// dbClient.js
const configResponse = await fetch('https://api.internal/config');
export const dbConfig = await configResponse.json();
```

---

## 🔄 6. Circular Dependencies: Mechanics & Remedies

A circular dependency occurs when `moduleA.js` imports `moduleB.js`, and `moduleB.js` imports `moduleA.js`.

```
[ moduleA.js ]  <=============>  [ moduleB.js ]
(imports B)                       (imports A)
```

In ESM, circular dependencies do **not** trigger a hard crash or infinite loop because ESM uses **live bindings** and creates module records before executing code. However, the importing module may read an **`undefined`** reference if the referenced variable has not been initialized yet (Temporal Dead Zone).

### The Fix: Extract Shared Types or Orchestrate via a Mediating Service
```javascript
// ❌ WRONG: Circular relationship
// user.js imports orderService.js
// orderService.js imports user.js

// ✅ CORRECT: Extract common types and dependency inject
// 1. types.js (Contains shared interfaces and constants)
// 2. userService.js (Imports types.js)
// 3. orderService.js (Imports types.js and receives userService via constructor)
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Deep class inheritance hierarchies (Fragile Base Class problem)
class Animal {}
class Mammal extends Animal {}
class Canine extends Mammal {}
class DomesticDog extends Canine {}
class GoldenRetriever extends DomesticDog {}

// ✅ GOOD: Prefer Composition over Inheritance
class Dog {
  constructor(name, abilities = []) {
    this.name = name;
    this.abilities = abilities;
  }

  perform() {
    this.abilities.forEach((ability) => ability.execute(this));
  }
}

// ❌ BAD: Default exports of anonymous objects (harms refactoring & tree-shaking)
export default {
  getUser,
  setUser
};

// ✅ GOOD: Explicit named exports (supports IDE auto-imports & dead-code elimination)
export { getUser, setUser };
```

---

## 📝 8. Summary & Next Steps

- ES6 `class` syntax is syntactic sugar over prototype delegation, but enforces strict mode and prevents invocation without `new`.
- **Private Fields (`#field`)** provide true runtime encapsulation that cannot be breached from outside the class.
- Use `super()` in derived constructors before referencing `this`.
- Prefer **Named Exports** over default exports to enable clean autocomplete and tree-shaking.
- Use **Dynamic `import()`** to code-split heavy dependencies and load them on demand.
- Avoid deep inheritance hierarchies; favor object composition and dependency injection.
- In the next lesson, we will examine **Error Handling Architectures: Custom Error Hierarchies, Stack Traces & Global Rejections**.
