# 02 - Syntax, Statements & Expressions

> 🌐 **Language / اللغة:** [English 🇬🇧](./02-syntax.md) | [العربية 🇸🇦](../ar/02-syntax.md)

---

## 📌 1. Essential Syntax Rules

Syntax defines the grammatical rules governing how JavaScript programs are written and parsed by runtime engines:

### A. Case Sensitivity
JavaScript is strictly case-sensitive. Identifiers with varying capitalization refer to completely distinct entities:
```javascript
let userRole = "Admin";
let userrole = "Guest";

console.log(userRole); // "Admin"
console.log(userrole); // "Guest"
```

### B. Whitespace & Formatting
JavaScript engines collapse and ignore redundant whitespace and indentation. However, maintaining clean, uniform formatting is vital for team readability and maintainability.

### C. Semicolons & Automatic Semicolon Insertion (ASI)
Semicolons (`;`) delimit statements. While JavaScript includes an automatic insertion mechanism (ASI), writing semicolons explicitly is widely considered a defensive coding best practice to prevent ambiguous parser edge cases.

---

## 💬 2. Code Comments

Comments are lines ignored during evaluation, utilized for inline documentation, architectural notes, or temporarily disabling code:

```javascript
// Single-line comment: explaining specific inline logic
let totalScore = 95;

/*
  Multi-line block comment:
  Useful for higher-level architectural notes
  or multi-line algorithmic breakdowns.
*/
```

---

## ⚖️ 3. Statements vs Expressions

Grasping this distinction is fundamental to understanding JavaScript execution models:

| Concept | Definition | Concrete Examples |
| :--- | :--- | :--- |
| **Expression** | Any code segment that **evaluates to a single value**. | `5 * 4` (evaluates to 20)<br>`"Hello " + "World"`<br>`age >= 18`<br>`typeof true` |
| **Statement** | A complete instruction that **performs an action** or controls execution flow. It does not produce a standalone pluggable value. | Variable declaration: `let x = 10;`<br>Conditionals: `if (x > 5) { ... }`<br>Loops: `for (let i = 0; i < 3; i++) { ... }` |

> [!TIP]
> **Heuristic Test:**  
> If an expression can be passed as an argument to `console.log(...)` or placed on the right-hand side of an assignment (`=`), it is an **Expression**.  
> If passing it produces a syntax error (e.g. `console.log(let x = 5)`), it is a **Statement**.

---

## 🎯 Summary & Lesson Challenge

- JavaScript identifiers are strictly case-sensitive.
- Comments clarify intent without impacting runtime execution.
- Expressions produce values; Statements orchestrate program actions.

**Challenge:** Categorize whether each of the following lines represents an Expression or a Statement:
1. `const status = "active";`
2. `100 / 2`
3. `switch(key) { ... }`
4. `Math.max(10, 25)`

---

**Next Lesson:**  
⬅️ [Previous: Introduction](./01-introduction.md) | ➡️ [Next: Variables (`let`, `const`, `var`)](./03-variables.md)
