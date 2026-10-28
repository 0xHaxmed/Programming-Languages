# 12 - Error Handling, Stack Traces & Debugging

> 🌐 **Language / اللغة:** [English 🇬🇧](./12-error-handling.md) | [العربية 🇸🇦](../ar/12-error-handling.md)

---

## 💥 1. Understanding Runtime Errors

Errors are an inevitable reality of software engineering. High-velocity developers excel not because they never encounter exceptions, but because they possess the analytical intuition to inspect runtime diagnostics and remediate bugs rapidly.

---

## 🔍 2. Common JavaScript Error Types

When a fatal execution constraint is violated, the JS engine halts execution and throws an `Error` object:

### A. `ReferenceError`
Occurs when attempting to reference an identifier that has not been declared within the current or enclosing scopes:
```javascript
console.log(sessionToken); // ReferenceError: sessionToken is not defined
```

### B. `TypeError`
Occurs when an operation is performed on an incompatible data type (e.g., calling a non-function, or dereferencing properties from `null` / `undefined`):
```javascript
const client = null;
console.log(client.profile); // TypeError: Cannot read properties of null (reading 'profile')

const port = 3000;
port(); // TypeError: port is not a function
```

### C. `SyntaxError`
Raised during the initial parsing phase prior to byte-code compilation when the grammar violates language specifications:
```javascript
// const let = 10; // SyntaxError: Unexpected token 'let'
```

---

## 🛠️ 3. Deciphering the Stack Trace

A Stack Trace illustrates the call stack chain active at the moment an exception occurred. Essential components:
1. **Error Constructor & Message:** (e.g., `TypeError: Failed to execute 'fetch' on 'Window'`).
2. **Originating Source Map Location:** File path, line number, and column index (e.g., `service.js:42:15`).

---

## 🧰 4. Diagnostic Logging via the `console` API

```javascript
// 1. Informational Warning:
console.warn("[Telemetry] Latency spike detected: > 450ms");

// 2. Fatal Error Highlight:
console.error("[Database] Connection pool exhausted");

// 3. Formatted Matrix Output (Essential for Arrays of Objects):
const auditLogs = [
    { event: "LOGIN", status: 200, user: "alice" },
    { event: "AUTH_FAIL", status: 401, user: "root" }
];
console.table(auditLogs);

// 4. Performance Benchmarking:
console.time("MatrixCalc");
for (let i = 0; i < 1_000_000; i++) {}
console.timeEnd("MatrixCalc"); // MatrixCalc: 1.82ms
```

---

## 🛡️ 5. Graceful Exception Handling (`try...catch...finally`)

Wrap unreliable boundaries (network I/O, parsing external payloads) within `try...catch` blocks to protect applications from catastrophic crashes:

```javascript
function safeParseConfiguration(rawPayload) {
    try {
        console.log("Parsing configuration payload...");
        const config = JSON.parse(rawPayload);
        return config;
    } catch (err) {
        // Intercepts the exception without terminating the runtime
        console.error("Payload corruption detected:", err.message);
        return { fallback: true, mode: "safe" };
    } finally {
        // Guaranteed cleanup block
        console.log("Configuration validation routine finalized.");
    }
}

safeParseConfiguration('{"port": 8080}'); // Valid JSON
safeParseConfiguration('invalid_json_string'); // Handled smoothly
```

---

## 🎯 Summary & Lesson Challenge

- ReferenceErrors point to missing bindings; TypeErrors point to invalid type operations.
- The Stack Trace gives the exact line and file where execution halted.
- Leverage `try...catch` to handle external faults gracefully.

**Challenge:** Write a function `safeDivide(a, b)`:
- If `b === 0`, explicitly throw an error: `throw new Error("Division by zero error")`.
- Call the function inside a `try...catch` block to handle and log the error message safely.

---

**Next Lesson:**  
⬅️ [Previous: String Handling](./11-string-handling.md) | 🎯 [Level 1 Exercises](../../Exercises/en/README.md) | 🏆 [Level 1 Final Project](../../Final-Project/en/README.md)
