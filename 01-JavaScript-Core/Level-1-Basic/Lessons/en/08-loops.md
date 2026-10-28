# 08 - Loops & Iteration (`for`, `while`, `for...of`)

> 🌐 **Language / اللغة:** [English 🇬🇧](./08-loops.md) | [العربية 🇸🇦](../ar/08-loops.md)

---

## 🔁 1. What are Loops?

Loops automate repetitious computational tasks by repeatedly executing a statement block until a specified termination condition evaluates to `false`.

---

## 🔄 2. The Classic `for` Loop

Best suited when the total iteration count is predetermined:

```
for (initialization; continuationCondition; updateExpression)
```

```javascript
for (let i = 1; i <= 5; i++) {
    console.log(`Iteration: ${i}`);
}
// Outputs: 1, 2, 3, 4, 5
```

---

## ⏳ 3. The `while` Loop

Best suited when iterations depend on dynamic runtime state rather than a predetermined index count:

```javascript
let fuel = 3;

while (fuel > 0) {
    console.log(`Remaining fuel units: ${fuel}`);
    fuel--; // Crucial decrement step to avoid an infinite loop
}

console.log("Engine halted: Out of fuel.");
```

---

## 🚦 4. The `do...while` Loop

Guarantees that the code block executes **at least once** before verifying the truth condition:

```javascript
let attempts = 10;

do {
    console.log("Executed at least once regardless of condition validity!");
    attempts++;
} while (attempts < 5);
```

---

## 🛑 5. Iteration Flow Control: `break` and `continue`

- **`break`:** Terminates the enclosing loop structure immediately.
- **`continue`:** Aborts the remainder of the current iteration and jumps directly to the next cycle.

```javascript
for (let i = 1; i <= 10; i++) {
    if (i === 4) {
        continue; // Skip printing 4
    }
    if (i === 8) {
        break; // Exit completely at 8
    }
    console.log(i); // Outputs: 1, 2, 3, 5, 6, 7
}
```

---

## 🌟 6. Modern Loop Iterators: `for...of` and `for...in`

### A. `for...of` (Iterables: Arrays, Sets, Strings)
Direct, clean access to individual values:
```javascript
const frameworks = ["React", "Vue", "Angular"];

for (const framework of frameworks) {
    console.log(`Framework: ${framework}`);
}
```

### B. `for...in` (Enumerable Properties of Objects)
Iterates over key strings:
```javascript
const device = { model: "Pixel", os: "Android", ram: "12GB" };

for (const prop in device) {
    console.log(`${prop}: ${device[prop]}`);
}
```

---

## 🎯 Summary & Lesson Challenge

- Use `for` for counted index tracking.
- Guard against infinite loops in `while` by ensuring state progression towards termination.
- Prefer `for...of` when consuming array elements.

**Challenge:** Write a `for` loop that prints all even numbers between 1 and 20.

---

**Next Lesson:**  
⬅️ [Previous: Conditions](./07-conditions.md) | ➡️ [Next: Functions & Parameters](./09-functions.md)
