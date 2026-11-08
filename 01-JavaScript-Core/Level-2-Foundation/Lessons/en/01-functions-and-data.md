# 01 - Functions, Functional Iterators & Modern Data Manipulation

> 🌐 **Language / اللغة:** [English 🇬🇧](./01-functions-and-data.md) | [العربية 🇸🇦](../ar/01-functions-and-data.md)

---

## 🎯 1. Overview & Learning Objectives

In Level 1, you learned how to declare basic functions and store items in arrays and objects. In modern enterprise JavaScript (ES6+), functions are treated as first-class citizens and data manipulation relies on declarative, immutable paradigms.

By the end of this lesson, you will master:
1. Writing concise, idiomatic **Arrow Functions**.
2. Transforming data collections declaratively using `map()`, `filter()`, and `reduce()`.
3. Unpacking data structures efficiently with **Destructuring Assignment**.
4. Utilizing the **Spread (`...`)** and **Rest (`...`)** operators for immutable state updates.
5. Applying Clean Code conventions (Airbnb style) to avoid data mutation bugs.

---

## 🏹 2. Arrow Functions (`=>`)

Introduced in ES6, arrow functions offer a concise syntax for writing function expressions.

### Syntax Variations:
```javascript
// 1. Standard Multi-line Arrow Function
const calculateTax = (amount, rate) => {
  const tax = amount * rate;
  return amount + tax;
};

// 2. Concise Body (Implicit Return) — One-liners automatically return the evaluated expression:
const double = (n) => n * 2;

// 3. Returning an Object Literal (Wrap in parentheses to avoid block ambiguity):
const makeUser = (id, username) => ({ id, username });

// 4. Single Parameter (Parentheses are optional, but recommended for consistency):
const square = x => x * x;
```

> [!TIP]
> **Clean Code Guideline (Airbnb):**  
> Prefer arrow functions for anonymous callbacks (e.g. inside `map`, `filter`, or event listeners) for clean, readable code.

---

## 🔄 3. Functional Array Iterators: `map`, `filter`, `reduce`

In modern JavaScript, avoid imperative `for` loops when transforming data collections. Declarative methods express *what* should happen, not *how* to iterate.

### A. `Array.prototype.map()`: Transforming Elements
Returns a **new array** of the exact same length where each element is the result of invoking a callback:

```javascript
const pricesInUSD = [10, 25, 50, 100];
const exchangeRate = 1.25;

// Transform USD prices to EUR:
const pricesInEUR = pricesInUSD.map(price => price * exchangeRate);
console.log(pricesInEUR); // [12.5, 31.25, 62.5, 125]
```

### B. `Array.prototype.filter()`: Selecting Subsets
Returns a **new array** containing only the elements that return `true` for a predicate function:

```javascript
const products = [
  { id: 1, name: 'Keyboard', inStock: true, price: 75 },
  { id: 2, name: 'Mouse', inStock: false, price: 25 },
  { id: 3, name: 'Monitor', inStock: true, price: 300 }
];

// Extract available inventory:
const availableProducts = products.filter(item => item.inStock && item.price < 100);
console.log(availableProducts); 
// [{ id: 1, name: 'Keyboard', inStock: true, price: 75 }]
```

### C. `Array.prototype.reduce()`: Aggregating into a Single Value
Collapses an array into an accumulator value (a number, an object, or another collection):

```javascript
const cartItems = [
  { title: 'Book', price: 15, quantity: 2 },
  { title: 'Pen', price: 3, quantity: 5 },
  { title: 'Bag', price: 40, quantity: 1 }
];

// Calculate total cart value:
const cartTotal = cartItems.reduce((accumulator, item) => {
  return accumulator + (item.price * item.quantity);
}, 0); // 0 is the initial value of accumulator

console.log(cartTotal); // 85
```

### Quick Reference for Search Methods:
- `find(fn)`: Returns the **first element** matching the condition, or `undefined`.
- `findIndex(fn)`: Returns the index of the first match, or `-1`.
- `some(fn)`: Returns `true` if **at least one** element passes the condition.
- `every(fn)`: Returns `true` only if **all** elements pass the condition.

---

## 📦 4. Destructuring Assignment

Destructuring allows you to unpack values from arrays or properties from objects directly into distinct variables.

### Object Destructuring:
```javascript
const developer = {
  id: 101,
  fullName: 'Alice Johnson',
  role: 'Frontend Engineer',
  country: 'Egypt'
};

// Basic unpacking:
const { fullName, role } = developer;

// Unpacking with Aliasing (renaming variables) and Default Values:
const { role: jobTitle, seniority = 'Junior' } = developer;
console.log(jobTitle);  // 'Frontend Engineer'
console.log(seniority); // 'Junior' (default applied because property was undefined)
```

### Array Destructuring:
```javascript
const rgb = [255, 128, 0];

// Positional assignment:
const [red, green, blue] = rgb;
console.log(red); // 255

// Skipping elements with empty commas:
const [, onlyGreen] = rgb;
console.log(onlyGreen); // 128
```

---

## ⚡ 5. Rest & Spread Operators (`...`)

Although they share the exact same three dots (`...`), their behavior depends on where they are placed:

### A. Rest Operator: Gathering Items
Gathers remaining elements into an array:

```javascript
// 1. In function parameters (Replaces legacy 'arguments' object):
function logScores(teamName, ...scores) {
  console.log(`Team: ${teamName}`);
  console.log(`Scores:`, scores); // 'scores' is a true JavaScript Array
}
logScores('Alpha', 95, 88, 100);

// 2. In destructuring:
const [primaryUser, ...otherUsers] = ['Alice', 'Bob', 'Charlie', 'Dana'];
console.log(primaryUser); // 'Alice'
console.log(otherUsers);  // ['Bob', 'Charlie', 'Dana']
```

### B. Spread Operator: Unpacking Items
Expands an iterable (like an array or string) or an object into individual elements:

```javascript
// 1. Combining Arrays (Immutably without .concat):
const frontend = ['HTML', 'CSS', 'JS'];
const backend = ['Node.js', 'PostgreSQL'];
const fullStack = [...frontend, ...backend, 'Docker'];

// 2. Immutably Updating Object State:
const baseConfig = { theme: 'dark', language: 'en', autoSave: true };

// Overwriting 'theme' while preserving all other properties:
const userConfig = {
  ...baseConfig,
  theme: 'solarized'
};
console.log(userConfig);
// { theme: 'solarized', language: 'en', autoSave: true }
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Mutating function argument directly
function applyDiscount(item, discount) {
  item.price = item.price - discount; // Mutates original object in memory!
  return item;
}

// ✅ GOOD: Immutably returning a new object with spread
function applyDiscount(item, discount) {
  return {
    ...item,
    price: item.price - discount
  };
}

// ❌ BAD: Imperative loop with manual accumulator push
const activeNames = [];
for (let i = 0; i < users.length; i++) {
  if (users[i].isActive) {
    activeNames.push(users[i].name);
  }
}

// ✅ GOOD: Declarative chaining with filter and map
const activeNames = users
  .filter(user => user.isActive)
  .map(user => user.name);
```

---

## 📝 7. Summary & Next Steps
- Use **Arrow Functions** for concise, expressive callbacks.
- Leverage **`map`**, **`filter`**, and **`reduce`** to process data without mutating source arrays.
- Use **Destructuring** to keep function parameter signatures and data extraction clean.
- Use **Spread (`...`)** to clone and merge collections immutably.
- In the next lesson, we will explore **Execution Context, Scope, and Hoisting Mechanics**.
