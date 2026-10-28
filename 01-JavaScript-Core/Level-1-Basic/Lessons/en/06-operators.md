# 06 - Operators: Arithmetic, Comparison & Logical

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-operators.md) | [العربية 🇸🇦](../ar/06-operators.md)

---

## 🧮 1. Arithmetic Operators

Arithmetic operators execute mathematical computations on numeric operands:

| Operator | Description | Syntax Example | Evaluated Result |
| :---: | :--- | :--- | :---: |
| `+` | Addition | `10 + 5` | `15` |
| `-` | Subtraction | `10 - 4` | `6` |
| `*` | Multiplication | `4 * 3` | `12` |
| `/` | Division | `20 / 4` | `5` |
| `%` | Modulo (Remainder) | `7 % 3` | `1` |
| `**` | Exponentiation | `2 ** 3` ($2^3$) | `8` |
| `++` | Increment (+1) | `let x = 5; x++;` | `6` |
| `--` | Decrement (-1) | `let y = 5; y--;` | `4` |

---

## 📝 2. Assignment Operators

Shorthand notation for evaluating an expression and updating the left-hand variable in place:

```javascript
let balance = 100;

balance += 50;  // Equivalent to: balance = balance + 50 (150)
balance -= 20;  // Equivalent to: balance = balance - 20 (130)
balance *= 2;   // Equivalent to: balance = balance * 2 (260)
balance /= 4;   // Equivalent to: balance = balance / 4 (65)
```

---

## ⚖️ 3. Comparison Operators

Evaluate relationships between operands, consistently returning a Boolean (`true` or `false`):

### Strict Equality (`===`) vs Loose Equality (`==`)

```javascript
// Loose Equality (==): Performs automatic type coercion (Unsafe)
console.log(5 == "5");    // true (dangerously coerces "5" to number 5)
console.log(0 == false);  // true
console.log("" == 0);     // true

// Strict Equality (===): Checks both value AND data type (Defensive)
console.log(5 === "5");   // false (Number vs String)
console.log(0 === false); // false (Number vs Boolean)
console.log(5 === 5);     // true
```

> [!IMPORTANT]
> **Production Standard:**  
> Always enforce strict equality (`===`) and strict inequality (`!==`). Treat loose equality (`==`, `!=`) as a deprecated anti-pattern.

Relational Comparisons:
- `>` (Greater than) and `<` (Less than)
- `>=` (Greater than or equal) and `<=` (Less than or equal)

---

## 🚦 4. Logical Operators

### A. Logical AND (`&&`)
Returns `true` only if **all** operands evaluate to truthy:
```javascript
console.log(true && true);   // true
console.log(true && false);  // false
```

### B. Logical OR (`||`)
Returns `true` if **at least one** operand evaluates to truthy:
```javascript
console.log(true || false);  // true
console.log(false || false); // false
```

### C. Logical NOT (`!`)
Inverts the Boolean truth value:
```javascript
console.log(!true);  // false
console.log(!false); // true
```

---

## ⚡ 5. Short-Circuiting & Nullish Coalescing (`??`)

In JavaScript, logical operators return the **actual operand value** that resolved the condition rather than a pure Boolean:

```javascript
// Logical OR (||) falls back on the first Truthy value:
const userTheme = "";
const defaultTheme = "dark";
const activeTheme = userTheme || defaultTheme;
console.log(activeTheme); // "dark" (because "" is Falsy)

// Nullish Coalescing (??):
// Only falls back on null or undefined, preserving 0 and false:
const retryCount = 0;
console.log(retryCount || 3); // 3 (Treats 0 as falsy - unintended bug!)
console.log(retryCount ?? 3); // 0 (Correct: 0 is defined and preserved)
```

---

## 🎯 Summary & Lesson Challenge

- Enforce `===` to prevent unexpected coercion artifacts.
- Modulo (`%`) and exponentiation (`**`) solve circular math and power equations.
- Use `??` when dealing with valid numeric `0` or empty strings `""`.

**Challenge:** Write a conditional expression that checks if a user is at least 18 years old **and** possesses a valid verification status (`isVerified === true`). If both conditions are met, log `"Access granted"`.

---

**Next Lesson:**  
⬅️ [Previous: Type Conversion](./05-type-conversion.md) | ➡️ [Next: Conditionals & Control Flow](./07-conditions.md)
