# 02 - Scope, Hoisting & The Temporal Dead Zone

> 🌐 **Language / اللغة:** [English 🇬🇧](./02-scope-and-hoisting.md) | [العربية 🇸🇦](../ar/02-scope-and-hoisting.md)

---

## 🎯 1. Overview & Learning Objectives

One of the most frequent sources of subtle bugs in JavaScript applications is a misunderstanding of how the engine resolves variable visibility and allocates memory during code execution.

By the end of this lesson, you will master:
1. The 3 levels of scope in JavaScript: **Global**, **Function**, and **Block**.
2. How the JavaScript engine resolves identifiers via the **Scope Chain**.
3. The precise behavioral differences between `var`, `let`, and `const`.
4. The mechanics of **Hoisting** across Creation and Execution phases.
5. What the **Temporal Dead Zone (TDZ)** is and why it throws `ReferenceError`.
6. Clean Code variable declaration best practices.

---

## 🌐 2. The 3 Tiers of Scope

Scope dictates where a variable or function is accessible within your source code.

```
+-------------------------------------------------------------+
| 1. GLOBAL SCOPE                                             |
|    Available everywhere across the entire runtime           |
|                                                             |
|    +---------------------------------------------------+    |
|    | 2. FUNCTION SCOPE (Local)                         |    |
|    |    Confined to the function body                  |    |
|    |                                                   |    |
|    |    +-----------------------------------------+    |    |
|    |    | 3. BLOCK SCOPE                          |    |    |
|    |    |    Confined strictly inside { ... }     |    |    |
|    |    +-----------------------------------------+    |    |
|    +---------------------------------------------------+    |
+-------------------------------------------------------------+
```

### 1. Global Scope
Variables declared outside any function or block live in the Global Scope. In browsers, global `var` declarations attach directly as properties of the `window` object:
```javascript
var appName = 'ShopApp';
console.log(window.appName); // 'ShopApp' (Pollutes global window)

const apiKey = 'SECRET_123';
console.log(window.apiKey);  // undefined (let and const do NOT pollute window)
```

### 2. Function Scope
Variables declared inside a function are accessible only within that function:
```javascript
function initSession() {
  var sessionId = 'sess_9941';
  let token = 'xyz_token';
}
initSession();
// console.log(sessionId); // Uncaught ReferenceError: sessionId is not defined
```

### 3. Block Scope
Any pair of curly braces `{ ... }` (in `if`, `for`, `while`, or standalone blocks) defines a block scope. **`let` and `const` respect block scope; `var` does NOT!**
```javascript
if (true) {
  var leakedVar = 'I can escape blocks!';
  let scopedLet = 'I am trapped inside this block';
  const scopedConst = 'Me too';
}

console.log(leakedVar);   // 'I can escape blocks!' (var ignores blocks!)
// console.log(scopedLet); // Uncaught ReferenceError: scopedLet is not defined
```

---

## 🔗 3. The Scope Chain & Lexical Resolution

JavaScript uses **Lexical Scoping**, meaning scope is determined by where variables and functions are physically authored in the source code.

When a variable is evaluated:
1. The engine checks the immediate local scope.
2. If not found, it traverses outward to the parent enclosing scope.
3. It repeats this traversal step-by-step until reaching the Global Scope.
4. If still not found anywhere, it throws a `ReferenceError`.

```javascript
const globalGreeting = 'Hello';

function outer() {
  const outerName = 'Alice';

  function inner() {
    const innerTitle = 'Engineer';
    // Traverses inward-to-outward:
    console.log(`${globalGreeting}, ${outerName} (${innerTitle})`);
  }

  inner();
}
outer(); // "Hello, Alice (Engineer)"
```

---

## 🏗️ 4. Hoisting: Creation Phase vs Execution Phase

When the JavaScript engine (V8, SpiderMonkey) executes a script or function, it does not execute line 1 immediately. It runs in **two phases**:

```
PHASE 1: Creation Phase (Memory Allocation)
- Allocates memory for function declarations.
- Allocates memory for variables (var initialized as undefined; let/const left uninitialized).

PHASE 2: Execution Phase (Code Evaluation)
- Runs code line-by-line from top to bottom.
- Evaluates expressions and assigns actual values.
```

### A. Function Declaration Hoisting
Function declarations are **hoisted entirely** with their implementation body:
```javascript
// This works completely without error:
greetUser('Hassan'); // "Welcome Hassan!"

function greetUser(name) {
  console.log(`Welcome ${name}!`);
}
```

### B. Function Expressions & Arrow Functions are NOT Hoisted
Because function expressions are assigned to variables, they follow variable hoisting rules:
```javascript
// TypeError: sayHi is not a function
sayHi(); 

var sayHi = function() {
  console.log('Hi!');
};
```

---

## 🚫 5. The Temporal Dead Zone (TDZ)

A common myth is that `let` and `const` are not hoisted. **They ARE hoisted**, but unlike `var`, they are not initialized with `undefined`.

The period between entering the scope and the actual line where the variable is declared is known as the **Temporal Dead Zone (TDZ)**.

```javascript
{
  // <---- TDZ for 'username' begins here
  // console.log(username); // ❌ Uncaught ReferenceError: Cannot access 'username' before initialization
  
  const greeting = 'Welcome'; // 'username' is still in TDZ
  
  let username = 'Bob';       // <---- TDZ ends here! Initialized with 'Bob'
  console.log(username);      // ✅ 'Bob'
}
```

### Comparison Matrix: `var` vs `let` vs `const`

| Feature | `var` | `let` | `const` |
| :--- | :---: | :---: | :---: |
| **Scope Boundary** | Function Scope | Block Scope `{}` | Block Scope `{}` |
| **Hoisting Behavior** | Hoisted + initialized as `undefined` | Hoisted into **TDZ** | Hoisted into **TDZ** |
| **Re-declaration** | Allowed in same scope | Forbidden (`SyntaxError`) | Forbidden (`SyntaxError`) |
| **Re-assignment** | Allowed | Allowed | Forbidden (`TypeError`) |
| **Window Property** | Yes (`window.x`) | No | No |

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Using var and relying on hoisting ambiguity
function calculateTotal(items) {
  var total = 0;
  for (var i = 0; i < items.length; i++) {
    var discount = 5;
    total += items[i] - discount;
  }
  console.log(i); // 3 (leaked into function scope!)
  return total;
}

// ✅ GOOD: Using const by default, let for counters, strict block scoping
function calculateTotal(items) {
  const DISCOUNT = 5;
  let total = 0;

  for (let i = 0; i < items.length; i++) {
    total += items[i] - DISCOUNT;
  }
  // console.log(i); // ReferenceError: i is cleanly contained inside for loop
  return total;
}

// ❌ BAD: Re-declaring variables in nested blocks with the same name (Shadowing confusion)
let user = 'Admin';
if (true) {
  let user = 'Guest'; // Shadowing outer user variable
}

// ✅ GOOD: Distinct, descriptive naming avoiding shadowing
const loggedInUser = 'Admin';
if (isPublicVisitor) {
  const visitorRole = 'Guest';
}
```

---

## 📝 7. Summary & Next Steps
- Always prefer **`const`** for immutable bindings; use **`let`** only when a variable must be re-assigned.
- **Never use `var`** in modern codebases.
- Understand the **TDZ** to prevent premature variable access errors.
- In the next lesson, we will explore **Objects, Memory Pointers, and Cloning Mechanics (Objects & References)**.
