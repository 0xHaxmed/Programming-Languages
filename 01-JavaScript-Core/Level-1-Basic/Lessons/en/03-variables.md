# 03 - Variables & Declarations (`let`, `const`, `var`)

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-variables.md) | [العربية 🇸🇦](../ar/03-variables.md)

---

## 📦 1. What is a Variable?

In computer programming, a variable is an allocated, labeled storage location in system memory (RAM). It holds a value (such as a string, number, or object) that a program can read, manipulate, and reference during execution.

```javascript
let accountHolder = "Alice";
console.log(accountHolder); // "Alice"
```

---

## 🏷️ 2. Comprehensive Comparison: `const` vs `let` vs `var`

Modern JavaScript (ES6+) provides three variable declaration keywords:

| Feature | `const` (Constant) | `let` (Mutable) | `var` (Legacy - Deprecated) |
| :--- | :---: | :---: | :---: |
| **Re-assignment** | ❌ Disallowed | ✅ Allowed | ✅ Allowed |
| **Re-declaration in Same Scope** | ❌ SyntaxError | ❌ SyntaxError | ✅ Allowed (Prone to silent bugs) |
| **Scoping Rules** | Block Scope `{}` | Block Scope `{}` | Function Scope (Leans outside blocks) |
| **Temporal Dead Zone (TDZ)** | ✅ Protected (Throws `ReferenceError`) | ✅ Protected (Throws `ReferenceError`) | ❌ Unprotected (Hoisted as `undefined`) |

---

## 🔍 3. Practical Usage & Best Practices

### A. Using `const` (The Standard Default)
Use `const` for identifiers whose references will remain unchanged throughout their lifespan:
```javascript
const API_BASE_URL = "https://api.github.com";
const PORT = 8080;

// Re-assigning throws an error:
// PORT = 3000; // TypeError: Assignment to constant variable.
```

### B. Using `let` (When Value Mutation is Required)
Use `let` for accumulators, loop counters, and state variables:
```javascript
let counter = 0;
counter += 1;
console.log(counter); // 1
```

### C. The Inherent Pitfalls of `var` ❌
1. **Block Leaking:**
```javascript
if (true) {
    var exposedToken = "secret_key_123";
    let protectedToken = "safe_key_456";
}

console.log(exposedToken); // Outputs: "secret_key_123" (leaked outside block!)
// console.log(protectedToken); // ReferenceError: protectedToken is not defined
```

2. **Accidental Silent Overwrites:**
```javascript
var systemStatus = "online";
// Hundreds of lines later...
var systemStatus = "offline"; // Overwritten silently without compiler warnings
```

---

## 📝 4. Identifier Naming Rules & Conventions

1. **Permitted Characters:** Alphanumeric characters (`a-z`, `A-Z`, `0-9`), dollar signs (`$`), and underscores (`_`).
2. **Initial Character:** Identifiers cannot begin with numeric digits (e.g. `1user` ❌ is invalid, `user1` ✅ is valid).
3. **Reserved Keywords:** Language keywords cannot be repurposed as identifier names (`let`, `class`, `function`, `return`).
4. **Style Conventions:** Adopt **camelCase** for local variables and functions, and **UPPER_SNAKE_CASE** for compile-time constants:
   - `userProfile` ✅
   - `calculateTotalPrice()` ✅
   - `MAX_RETRY_COUNT` ✅

---

## 🎯 Summary & Lesson Challenge

> [!IMPORTANT]
> **Modern JS Standard:**  
> Default to `const`. Switch to `let` only when you explicitly need reassignment. Completely avoid `var` in production.

**Challenge:** Declare a `const` representing your target programming goal, and a `let` tracking your current completed lesson count. Increment the lesson count, and log both variables with descriptive labels.

---

**Next Lesson:**  
⬅️ [Previous: Syntax](./02-syntax.md) | ➡️ [Next: Data Types](./04-data-types.md)
