# 03 - Objects, References & Memory Allocation

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-objects-and-references.md) | [العربية 🇸🇦](../ar/03-objects-and-references.md)

---

## 🎯 1. Overview & Learning Objectives

In JavaScript, understanding how data is stored in memory is the dividing line between junior and senior engineers. While primitive types behave predictably, objects (including arrays and functions) operate via **memory references**.

By the end of this lesson, you will master:
1. The architectural difference between the **Stack** and the **Heap**.
2. How **Primitive Values** (by value) differ from **Reference Types** (by reference).
3. The consequences of reference sharing and unintended object mutations.
4. The mechanics and limitations of **Shallow Copying** (`...`, `Object.assign`).
5. Performing robust **Deep Copying** using the modern `structuredClone()` API.
6. Object immutability with `Object.freeze()` and `Object.seal()`.

---

## 🧠 2. Stack vs Heap: How Memory is Allocated

JavaScript runtimes divide RAM into two primary memory spaces:

```
+------------------------------------+      +-----------------------------------------+
|        CALL STACK (Fast, Fixed)    |      |         MEMORY HEAP (Dynamic, Large)    |
+------------------------------------+      +-----------------------------------------+
| Primitive: score = 100             |      |                                         |
| Primitive: isActive = true         |      | Object Data:                            |
| Pointer:   userRef  ===============> ===> | { id: 1, name: 'Alice', role: 'Dev' }   |
+------------------------------------+      +-----------------------------------------+
```

### A. Primitive Types (Copied by Value)
Primitives (`string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint`) are stored directly on the Stack. Copying a primitive creates an independent duplicate:
```javascript
let a = 10;
let b = a; // Value 10 is copied
b = 20;

console.log(a); // 10 (Completely unaffected)
console.log(b); // 20
```

### B. Reference Types (Copied by Pointer)
Objects, arrays, and functions are allocated on the Heap. The variable stored on the Stack holds only a **pointer (memory address)**:
```javascript
const userA = { name: 'Alice', age: 25 };
const userB = userA; // Copies the MEMORY ADDRESS, not the object!

userB.age = 30; // Mutates the object in the Heap

console.log(userA.age); // 30! Both variables point to the exact same object!
```

---

## ⚖️ 3. Object Equality & Comparison

Because reference types store memory addresses, the strict equality operator (`===`) compares **references, not values**:

```javascript
const obj1 = { role: 'admin' };
const obj2 = { role: 'admin' };
const obj3 = obj1;

console.log(obj1 === obj2); // false (Different addresses in Heap memory!)
console.log(obj1 === obj3); // true  (Points to the exact same Heap address)
```

---

## 🧬 4. Shallow Copying vs Deep Copying

When duplicating objects, you must distinguish between copying the surface properties and cloning nested structures.

### A. Shallow Copying (Surface Level)
Techniques:
1. Spread operator: `const copy = { ...original };`
2. `Object.assign()`: `const copy = Object.assign({}, original);`
3. Array slicing: `const copy = originalArray.slice();`

```javascript
const team = {
  name: 'DevSec',
  leader: { name: 'Sarah', age: 32 } // Nested object!
};

const shallowClone = { ...team };
shallowClone.name = 'RedTeam'; // Safe: primitive 'name' is copied independently

// DANGER: Nested objects are still shared references!
shallowClone.leader.age = 35;
console.log(team.leader.age); // 35! Original team leader's age was mutated!
```

### B. Deep Copying (Complete Independence)
A deep copy recursively duplicates every nested object and array so that zero references are shared.

#### Method 1: The Modern Standard — `structuredClone()`
Built directly into modern browsers and Node.js (v17+):
```javascript
const deepClone = structuredClone(team);

deepClone.leader.age = 40;
console.log(team.leader.age); // 35 (Original is completely safe!)
```
`structuredClone()` handles cyclical references, Dates, Sets, Maps, and TypedArrays seamlessly.

#### Method 2: Legacy Fallback — `JSON.parse(JSON.stringify(obj))`
```javascript
const deepClone = JSON.parse(JSON.stringify(team));
```
> [!WARNING]
> **Limitations of JSON serialization:**
> - Drops functions and `undefined` properties.
> - Converts `Date` objects to strings.
> - Throws an error on circular references (`obj.self = obj`).

---

## 🔒 5. Object Immutability: `freeze` vs `seal`

JavaScript provides built-in mechanisms to restrict object modifications:

### `Object.freeze(obj)`
Prevents adding, deleting, or modifying existing properties:
```javascript
const config = Object.freeze({
  apiUrl: 'https://api.example.com',
  timeout: 5000
});

// config.timeout = 10000; // Silently fails in non-strict mode; throws TypeError in strict mode
console.log(config.timeout); // 5000
```
*(Note: `Object.freeze()` is **shallow**. Nested objects inside the frozen object can still be modified unless you freeze them recursively).*

### `Object.seal(obj)`
Prevents adding or deleting properties, but allows **modifying existing properties**.

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Mutating an incoming object parameter (Side Effects!)
function activateUser(user) {
  user.isActive = true; // Mutates caller's data
  user.lastLogin = new Date();
  return user;
}

// ✅ GOOD: Immutably returning an updated copy
function activateUser(user) {
  return {
    ...user,
    isActive: true,
    lastLogin: new Date()
  };
}

// ❌ BAD: Using JSON serialization for complex deep clones
const clone = JSON.parse(JSON.stringify(dataWithDatesAndRegex));

// ✅ GOOD: Using native structuredClone
const clone = structuredClone(dataWithDatesAndRegex);
```

---

## 📝 7. Summary & Next Steps
- **Primitives** are copied by value; **Objects** are copied by memory reference pointer.
- Comparing objects with `===` checks if they share the identical Heap address.
- Use the **Spread operator (`...`)** for simple 1-level shallow clones.
- Use **`structuredClone()`** for deep nested object trees.
- In the next lesson, we will explore **Callback Functions, Higher-Order Patterns, and Concurrency Basics**.
