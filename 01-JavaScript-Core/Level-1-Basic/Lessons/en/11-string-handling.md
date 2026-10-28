# 11 - String Manipulation & Built-in Methods

> 🌐 **Language / اللغة:** [English 🇬🇧](./11-string-handling.md) | [العربية 🇸🇦](../ar/11-string-handling.md)

---

## 🔤 1. String Immutability

In JavaScript, Strings are **primitive values and strictly immutable**. Built-in string methods do not mutate the underlying memory buffer of the original string; instead, they construct and return **a new String instance**.

---

## 📏 2. Length & Character Indexing

```javascript
const framework = "JavaScript";

console.log(framework.length);         // 10 (UTF-16 code units)
console.log(framework[0]);             // "J" (First character)
console.log(framework[framework.length - 1]); // "t" (Last character)
```

---

## 🛠️ 3. Core String Manipulation Methods

### A. Case Normalization
```javascript
const raw = "CyberSecurity";
console.log(raw.toLowerCase()); // "cybersecurity"
console.log(raw.toUpperCase()); // "CYBERSECURITY"
```

### B. Whitespace Sanitization (`trim`)
Crucial for cleaning user input prior to processing or database persistence:
```javascript
const rawInput = "   admin@system.local   \r\n";
console.log(rawInput.trim()); // "admin@system.local"
```

### C. Search & Inspection
```javascript
const logEntry = "ERROR [AuthService] Failed login attempt from IP 10.0.0.1";

console.log(logEntry.includes("ERROR"));       // true
console.log(logEntry.startsWith("ERROR"));     // true
console.log(logEntry.endsWith("10.0.0.1"));    // true
console.log(logEntry.indexOf("AuthService"));  // 7
```

### D. Substring Extraction (`slice`)
```javascript
const sessionID = "sess_98234_prod";

// Slice from index 5 to 10 (end index exclusive):
console.log(sessionID.slice(5, 10)); // "98234"

// Negative indexing extracts from the tail:
console.log(sessionID.slice(-4));    // "prod"
```

### E. Pattern Replacement (`replace` & `replaceAll`)
```javascript
const query = "cat and dog and cat";

// replace() alters only the initial occurrence:
console.log(query.replace("cat", "bird")); 
// "bird and dog and cat"

// replaceAll() alters all matching instances:
console.log(query.replaceAll("cat", "bird")); 
// "bird and dog and bird"
```

### F. Tokenization (`split`)
Deconstructs a string into an Array of substrings delimited by a separator:
```javascript
const csvRow = "id,username,email,role";
const columns = csvRow.split(",");
console.log(columns); // ["id", "username", "email", "role"]
```

---

## 📝 4. Template Literals & Interpolation

Using backticks (\`\`), developers achieve clean multi-line layouts and inline variable interpolation without messy `+` string concatenations:

```javascript
const user = "David";
const attemptsLeft = 2;

const securityAlert = `
[Security Alert]
User '${user}' exceeded authentication threshold.
Remaining attempts before account lockdown: ${attemptsLeft}.
`;

console.log(securityAlert);
```

---

## 🎯 Summary & Lesson Challenge

- Strings cannot be modified in-place (immutable primitives).
- Sanitize inputs with `trim()`, inspect with `includes()`, and slice substrings safely with `slice()`.
- Template literals elevate code readability.

**Challenge:** Write a function `maskEmail(email)` that takes an email address (e.g., `"john.doe@example.com"`) and returns an obfuscated version preserving the first and last letter of the local part (e.g. `"j***e@example.com"`).

---

**Next Lesson:**  
⬅️ [Previous: Basic Data Structures](./10-basic-data-structures.md) | ➡️ [Next: Error Handling & Debugging](./12-error-handling.md)
