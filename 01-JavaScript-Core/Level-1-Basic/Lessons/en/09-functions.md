# 09 - Functions, Parameters & Return Values

> 🌐 **Language / اللغة:** [English 🇬🇧](./09-functions.md) | [العربية 🇸🇦](../ar/09-functions.md)

---

## 🧩 1. What is a Function?

A function is a reusable, named subprogram designed to perform a distinct computational task. Functions are foundational to building robust software architectures:
1. **DRY Principle (Don't Repeat Yourself):** Avoid redundant duplicate code blocks.
2. **Modularity & Separation of Concerns:** Decompose complex software tasks into small, testable logic units.

---

## 🛠️ 2. Function Declaration & Invocation

```javascript
// 1. Defining the Function Declaration
function initiateGreeting() {
    console.log("System initialization routine complete.");
}

// 2. Invoking / Calling the Function
initiateGreeting();
```

---

## 📥 3. Parameters vs Arguments

- **Parameter:** The placeholder variable defined in the **function signature** to receive data.
- **Argument:** The actual concrete value supplied to the function upon **invocation**.

```javascript
// 'name' and 'role' are Parameters
function displayBadge(name, role) {
    console.log(`User: ${name} | Permission: ${role}`);
}

// "Sarah" and "Security Analyst" are Arguments
displayBadge("Sarah", "Security Analyst");
```

### Default Parameters
Assign fallback values if an argument is omitted or evaluates to `undefined`:
```javascript
function sendNotification(message, priority = "Normal") {
    console.log(`[Priority: ${priority}] -> ${message}`);
}

sendNotification("Cache cleared");                  // Priority defaults to "Normal"
sendNotification("Unauthorized access attempt!", "High");
```

---

## 📤 4. The Return Statement (`return`)

Functions compute values and return them back to the calling context:

```javascript
function calculateDiscount(basePrice, discountPercentage) {
    const discountedAmount = basePrice * (discountPercentage / 100);
    return basePrice - discountedAmount;
}

const finalPrice = calculateDiscount(200, 15);
console.log(`Payable Total: $${finalPrice}`); // $170
```

> [!IMPORTANT]
> **Execution Termination:**  
> The `return` keyword terminates function execution immediately. Statements situated directly beneath a hit `return` statement are unreachable.

---

## 🏹 5. Syntax Variants

### A. Function Declaration (Hoisted)
```javascript
function add(x, y) {
    return x + y;
}
```

### B. Function Expression (Anonymous or Named)
```javascript
const multiply = function(x, y) {
    return x * y;
};
```

### C. Arrow Function (ES6 Concise Syntax)
```javascript
const square = (n) => n * n;
console.log(square(5)); // 25
```

---

## 🎯 Summary & Lesson Challenge

- Functions compartmentalize reusable execution blocks.
- Parameters define the signature; arguments pass dynamic values.
- `return` yields computed output and exits execution.

**Challenge:** Write a function `convertCelsiusToFahrenheit(celsius)` that implements the conversion formula:  
$$F = (C \times \frac{9}{5}) + 32$$  
Invoke the function with $25^\circ\text{C}$ and log the resulting Fahrenheit degree.

---

**Next Lesson:**  
⬅️ [Previous: Loops](./08-loops.md) | ➡️ [Next: Basic Data Structures (Arrays & Objects)](./10-basic-data-structures.md)
