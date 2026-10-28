# Level 1 Practical Drills & Exercises

> 🌐 **Language / اللغة:** [English 🇬🇧](./README.md) | [العربية 🇸🇦](../ar/README.md)

Welcome to the Level 1 problem set. To solve these drills, create a local scratch file (e.g., `exercises.js`), develop the logic without copying solutions, and test outcomes inside your terminal or browser console.

---

## 🏋️ Exercise 1: Variables, Math & Discount Logic
Write a function `calculateCartTotal(price, quantity, taxPercentage, discountCode)`:
- Computes gross price ($price \times quantity$).
- Applies tax percentage.
- If `discountCode === "SAVE10"`, applies a 10% deduction.
- Returns the final payable balance rounded to 2 decimal places.

---

## 🏋️ Exercise 2: Branching & Authorization Guards
Write a function `validateUserAccess(user)` that receives an object payload:
```javascript
const user = { username: "mohamed", age: 19, isBanned: false };
```
- If `isBanned === true`, return `"Access Denied: Account Suspended"`.
- If `age < 18`, return `"Access Denied: Underage User"`.
- If `username === ""`, return `"Invalid Request: Username Required"`.
- If all guard conditions pass, return `"Welcome, " + user.username`.

---

## 🏋️ Exercise 3: Array Aggregations & Loops
Given an array of student examination marks:
```javascript
const grades = [65, 88, 42, 95, 73, 50, 38];
```
Write logic that:
1. Extracts all passing grades ($\ge 50$) into a new array `passingGrades`.
2. Computes the arithmetic mean (average) of the passing grades.
3. Finds the highest and lowest scores using a raw `for` loop (without using `Math.max` / `Math.min`).

---

## 🏋️ Exercise 4: String Frequency Analysis
Write a function `countVowels(sentence)`:
- Counts the total occurrence of vowels (`a`, `e`, `i`, `o`, `u`), case-insensitively.
- Example: `countVowels("JavaScript")` returns `3`.

---

## 🏋️ Exercise 5: In-Memory Inventory Manager
Model an inventory dataset:
```javascript
const inventory = [
    { id: 101, title: "Laptop", stock: 5, price: 1500 },
    { id: 102, title: "Mouse", stock: 0, price: 25 },
    { id: 103, title: "Keyboard", stock: 12, price: 70 },
    { id: 104, title: "Monitor", stock: 3, price: 420 }
];
```
Implement `sellItem(id, quantity)`:
- Searches item by `id`.
- Returns `"Item Not Found"` if non-existent.
- Returns `"Insufficient Stock"` if requested quantity exceeds availability.
- Deducts purchased quantity and returns a receipt object with total price and remaining stock.

---

### 💡 Completion Benchmark:
Successfully solving these 5 challenges indicates complete readiness for:  
🏆 **[Level 1 Final Project: JavaScript Logic Toolkit](../../Final-Project/en/README.md)**
