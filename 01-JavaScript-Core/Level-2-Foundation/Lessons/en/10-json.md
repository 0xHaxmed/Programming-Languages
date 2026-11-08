# 10 - Data Serialization: The JSON Specification

> 🌐 **Language / اللغة:** [English 🇬🇧](./10-json.md) | [العربية 🇸🇦](../ar/10-json.md)

---

## 🎯 1. Overview & Learning Objectives

**JSON (JavaScript Object Notation)** is the undisputed global standard for exchanging data across the internet. Although syntactically derived from JavaScript object literal notation, JSON is a language-agnostic, text-only data serialization specification (standardized under **RFC 8259**).

By the end of this lesson, you will master:
1. The structural constraints separating raw JavaScript objects from valid JSON text.
2. Serializing data with `JSON.stringify()`, replacer functions, and indentation.
3. Parsing JSON safely with `JSON.parse()` and error boundary handling.
4. Restoring custom data types (such as `Date`) using reviver functions.
5. Detecting and mitigating serialization pitfalls: circular references, data loss, and prototype injections.

---

## 📜 2. JavaScript Object vs Valid JSON

A common point of confusion is treating a JavaScript object as JSON. JSON is **always a text string**:

```
+------------------------------------+      +-----------------------------------------+
| JAVASCRIPT OBJECT LITERAL          |      | VALID JSON STRING                       |
+------------------------------------+      +-----------------------------------------+
| const user = {                     |      | '{"name":"Alice","age":28,"roles":[]}'  |
|   name: 'Alice', // Single quotes  |      |                                         |
|   age: 28,                         |      | RULES:                                  |
|   roles: [],                       |      | 1. Keys MUST use double quotes ("key")  |
|   active: true,                    |      | 2. Strings MUST use double quotes       |
|   greet() { console.log('Hi'); }   |      | 3. NO trailing commas                   |
| };                                 |      | 4. NO comments allowed                  |
|                                    |      | 5. NO functions, undefined, or symbols  |
+------------------------------------+      +-----------------------------------------+
```

### Supported JSON Data Types:
- `string` (must be in `"double quotes"`)
- `number` (integers, floats; no `NaN` or `Infinity`)
- `boolean` (`true`, `false`)
- `null`
- `array` (`[1, 2, 3]`)
- `object` (`{ "key": "value" }`)

---

## 🔄 3. Serialization: `JSON.stringify()`

`JSON.stringify(value, replacer, space)` transforms JavaScript values into serialized JSON strings.

### Basic Usage:
```javascript
const session = {
  id: 402,
  user: 'Hassan',
  isAdmin: false,
  cart: ['Laptop', 'Mouse']
};

const jsonString = JSON.stringify(session);
console.log(typeof jsonString); // "string"
console.log(jsonString); // '{"id":402,"user":"Hassan","isAdmin":false,"cart":["Laptop","Mouse"]}'
```

### What Gets Dropped During Serialization?
`JSON.stringify` automatically strips properties that JSON cannot represent:
```javascript
const weirdObject = {
  title: 'Test',
  secretToken: undefined,          // OMITTED completely!
  calculateHash: () => 'hash123',  // OMITTED completely!
  uniqueId: Symbol('id'),          // OMITTED completely!
  items: [undefined, () => {}]     // Inside arrays, replaced with null: [null, null]
};

console.log(JSON.stringify(weirdObject)); // '{"title":"Test","items":[null,null]}'
```

### Advanced Arguments: Replacer & Pretty-Printing
```javascript
const product = { id: 10, title: 'Desk', cost: 120, markup: 30 };

// 1. Array Replacer: Whitelist only specific keys:
console.log(JSON.stringify(product, ['title', 'cost']));
// '{"title":"Desk","cost":120}'

// 2. Function Replacer: Transform values dynamically:
const filtered = JSON.stringify(product, (key, value) => {
  if (key === 'cost') return undefined; // Strips 'cost'
  return value;
});

// 3. Pretty-Printing with Indentation (great for debugging and logging):
console.log(JSON.stringify(product, null, 2));
/*
{
  "id": 10,
  "title": "Desk",
  "cost": 120,
  "markup": 30
}
*/
```

---

## 📥 4. Deserialization: `JSON.parse()` & Error Handling

`JSON.parse(text, reviver)` converts JSON text back into native JavaScript objects.

### ⚠️ Mandatory Rule: Always Wrap in `try...catch`!
If the input string contains invalid syntax (e.g., single quotes or an unclosed brace), `JSON.parse()` throws a fatal `SyntaxError` that crashes execution if unhandled:

```javascript
function safeJsonParse(rawString, fallback = null) {
  try {
    return JSON.parse(rawString);
  } catch (error) {
    console.error('Failed to parse JSON payload:', error.message);
    return fallback; // Return safe default instead of crashing
  }
}

const badInput = "{ 'user': 'Alex' }"; // Invalid JSON (single quotes)
const result = safeJsonParse(badInput, {});
console.log(result); // {} (Gracefully handled)
```

### The Reviver Function: Restoring Complex Types
JSON has no native `Date` type; dates serialize into ISO strings (`"2026-11-08T20:30:00.000Z"`). Use a **Reviver** to restore them to live `Date` instances during parsing:

```javascript
const serverPayload = '{"title":"Release","createdAt":"2026-11-08T20:30:00.000Z"}';

const record = JSON.parse(serverPayload, (key, value) => {
  if (key === 'createdAt') {
    return new Date(value); // Reconstruct Date object
  }
  return value;
});

console.log(record.createdAt instanceof Date); // true
console.log(record.createdAt.getFullYear());   // 2026
```

---

## 💥 5. Pitfalls & Security Considerations

### 1. Circular References
An object containing a reference to itself throws an immediate `TypeError`:
```javascript
const node = { name: 'Parent' };
node.self = node; // Circular pointer!

// JSON.stringify(node); 
// ❌ Uncaught TypeError: Converting circular structure to JSON
```

### 2. Client-Side Prototype Injection via JSON
When an application receives JSON from untrusted external endpoints and merges keys recursively without validation:
```javascript
// Malicious JSON payload:
const untrustedJson = '{"__proto__": {"isAdmin": true}}';
const parsed = JSON.parse(untrustedJson);

// If passed to a naive recursive merge, it mutates Object.prototype!
```
*(Always sanitize keys against `__proto__` and `constructor` before performing recursive object merges).*

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Bare JSON.parse assuming network responses are always valid JSON
const data = JSON.parse(responseBody); // Crashes if server returns HTML error page (500 / 502)!

// ✅ GOOD: Defensively parsing with try/catch
let data;
try {
  data = JSON.parse(responseBody);
} catch {
  data = { error: 'Invalid JSON response from server' };
}

// ❌ BAD: Storing functions inside objects meant for API exchange
const payload = {
  id: 1,
  execute: () => doWork() // Silently omitted upon stringify!
};
```

---

## 📝 7. Summary & Next Steps
- JSON is a **text format**; JavaScript objects are **in-memory data structures**.
- Keys and strings in JSON **must use double quotes (`"`)**.
- `JSON.stringify()` drops functions, `undefined`, and Symbols.
- **Never call `JSON.parse()` without a `try...catch` block** in production code.
- In the next lesson, we will master client-server communication: **Network Requests with the Fetch API**.
