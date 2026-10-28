# 07 - Conditionals, Branching & Truthy/Falsy

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-conditions.md) | [العربية 🇸🇦](../ar/07-conditions.md)

---

## 🚦 1. What are Conditional Statements?

Conditionals allow programs to execute specific blocks of logic based on dynamic runtime evaluation—branching execution paths when conditions are met or falling back when they fail.

---

## 🔀 2. Branching via `if`, `else if`, and `else`

```javascript
const score = 88;

if (score >= 90) {
    console.log("Grade: A");
} else if (score >= 80) {
    console.log("Grade: B");
} else if (score >= 70) {
    console.log("Grade: C");
} else {
    console.log("Grade: Below threshold");
}
```

---

## 🎭 3. Truthy vs Falsy Evaluation

In JavaScript, any expression evaluated within a Boolean context is automatically coerced to either `true` or `false`.

### The 8 Falsy Values in JavaScript ❌:
1. `false`
2. `0` and `-0`
3. `0n` (BigInt zero)
4. `""` (Empty string)
5. `null`
6. `undefined`
7. `NaN`
8. `document.all` (Historical browser compatibility quirk)

> [!IMPORTANT]
> **Everything else evaluates to Truthy ✅!**  
> Notice that:
> - Empty Arrays `[]` are **Truthy**!
> - Empty Objects `{}` are **Truthy**!
> - Non-empty strings `"0"`, `"false"`, `" "` are **Truthy**!

```javascript
const userQuery = ""; // Empty string: Falsy

if (userQuery) {
    console.log("Executing search query...");
} else {
    console.log("Please enter a search term."); // Triggered
}
```

---

## ⚡ 4. The Ternary Operator (`?:`)

A concise inline expression replacing basic `if/else` assignments:

```
condition ? valueIfTrue : valueIfFalse
```

```javascript
const age = 21;
const status = age >= 18 ? "Eligible" : "Underage";
console.log(status); // "Eligible"
```

---

## 🎛️ 5. The `switch` Statement

Optimized for branching against discreet, enumerated match criteria:

```javascript
const command = "start";

switch (command) {
    case "start":
        console.log("Service initializing...");
        break; // Prevents fallthrough into subsequent cases
    case "stop":
        console.log("Service halting...");
        break;
    case "restart":
        console.log("Service recycling...");
        break;
    default:
        console.log("Unknown command parameter.");
}
```

> [!WARNING]
> Without a terminal `break` keyword, execution continues falling through adjacent `case` clauses regardless of condition validity.

---

## 🎯 Summary & Lesson Challenge

- Use `if / else` for ranged or compound boolean expressions.
- Remember the 8 falsy values to predict conditional behavior accurately.
- Use ternary expressions for clean value assignments and `switch` for multi-value discrete dispatching.

**Challenge:** Write a script that checks an `orderTotal` variable:
- If $> \$200$: free shipping.
- If between $\$100$ and $\$200$: shipping costs $\$15$.
- If $< \$100$: shipping costs $\$25$.

---

**Next Lesson:**  
⬅️ [Previous: Operators](./06-operators.md) | ➡️ [Next: Loops & Iteration](./08-loops.md)
