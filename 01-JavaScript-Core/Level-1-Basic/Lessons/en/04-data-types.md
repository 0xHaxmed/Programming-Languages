# 04 - JavaScript Data Types & Memory Basics

> 🌐 **Language / اللغة:** [English 🇬🇧](./04-data-types.md) | [العربية 🇸🇦](../ar/04-data-types.md)

---

## 🗂️ 1. Dynamic Typing Overview

JavaScript is a **dynamically typed** language. Variable types do not need explicit annotations during declaration; types are automatically inferred and associated with runtime values rather than variable bindings:

```javascript
let dynamicData = 42;          // Initially assigned a Number
dynamicData = "Now a String";  // Re-bound to a String value dynamically
```

Data types in JavaScript are bifurcated into two primary classifications:
1. **Primitive Types**
2. **Reference Types (Objects)**

---

## 🧱 2. Primitive Data Types

Primitive values represent raw, immutable data stored directly in stack memory:

| Type | Syntax Example | Technical Description |
| :--- | :--- | :--- |
| **String** | `"Hello"`, `'JS'`, \`Score: ${score}\` | Sequences of UTF-16 code units. Template literals enable interpolation. |
| **Number** | `100`, `3.1415`, `-20`, `NaN` | 64-bit floating-point numbers (IEEE 754 standard). `NaN` denotes invalid arithmetic. |
| **Boolean** | `true` or `false` | Logical truth values evaluated in conditional branching. |
| **Undefined** | `let val;` | Indicates an uninitialized variable; assigned by the runtime automatically. |
| **Null** | `let user = null;` | Represents the deliberate, explicit absence of any object value. |
| **BigInt** | `9007199254740991n` | Arbitrary-precision integers supporting numbers beyond $2^{53} - 1$. |
| **Symbol** | `Symbol("uniqueKey")` | Guaranteed unique, immutable identifiers used as hidden object properties. |

---

## 📦 3. Reference Types (Objects)

Reference values are allocated on the memory Heap. The variable identifier merely holds a pointer/reference to that heap location:

### A. Objects
Key-value property mappings:
```javascript
const user = {
    username: "alex99",
    tier: "premium",
    isActive: true
};
console.log(user.username); // "alex99"
```

### B. Arrays
Ordered collections indexed by zero-based sequential integers:
```javascript
const languages = ["JavaScript", "Python", "Go"];
console.log(languages[0]); // "JavaScript"
```

### C. Functions
First-class executable code structures:
```javascript
const greet = function() {
    return "Hello, World!";
};
```

---

## 🔍 4. Type Inspection via `typeof`

The `typeof` operator inspects and returns a string describing an operand's data type:

```javascript
typeof "Welcome";        // "string"
typeof 1024;             // "number"
typeof false;            // "boolean"
typeof undefined;        // "undefined"
typeof { id: 1 };        // "object"
typeof [10, 20];         // "object" (Arrays are specialized Objects)
typeof function(){};     // "function"
```

> [!WARNING]
> **The Infamous Historical Bug:**  
> ```javascript
> typeof null; // "object"
> ```  
> In the original 1995 JS implementation, object types had a type tag of 0 in memory. Since `null` represented a null pointer (0x00), it incorrectly returned `"object"`. This quirk is preserved to ensure backward compatibility. Remember: `null` is a primitive, NOT an object.

---

## 🎯 Summary & Lesson Challenge

- JavaScript features 7 primitive types and reference types (Objects/Arrays/Functions).
- Primitive values are copied by value; objects are referenced by memory pointer.
- `typeof` is a diagnostic tool for type verification.

**Challenge:** Create a variable holding a Number and another holding the string `"10"`. Use `typeof` to verify their types. Consider what happens when adding them together (`num + str`); we explore this in the next lesson!

---

**Next Lesson:**  
⬅️ [Previous: Variables](./03-variables.md) | ➡️ [Next: Type Conversion & Coercion](./05-type-conversion.md)
