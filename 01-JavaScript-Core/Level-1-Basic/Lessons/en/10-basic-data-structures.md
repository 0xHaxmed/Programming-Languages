# 10 - Basic Data Structures (Arrays & Objects)

> 🌐 **Language / اللغة:** [English 🇬🇧](./10-basic-data-structures.md) | [العربية 🇸🇦](../ar/10-basic-data-structures.md)

---

## 📚 1. Why Data Structures?

Primitive variables store individual values. Real-world systems model relational data: user registries, e-commerce cart items, network telemetry, and database records. In JavaScript, **Arrays** and **Objects** are the fundamental vehicles for organizing complex data.

---

## 🗃️ 2. Arrays

An Array is an ordered, zero-indexed list of arbitrary values:

```javascript
const tools = ["Debugger", "Compiler", "Linter"];

// Accessing items via zero-based Index:
console.log(tools[0]); // "Debugger"
console.log(tools.length); // 3 (Total element count)
```

### Essential Array Mutation Methods:
```javascript
const inventory = ["CPU", "GPU"];

// Appending to the end:
inventory.push("RAM"); // ["CPU", "GPU", "RAM"]

// Removing from the end:
const lastItem = inventory.pop(); // Returns "RAM"

// Prepending to the beginning:
inventory.unshift("Motherboard"); // ["Motherboard", "CPU", "GPU"]

// Removing from the beginning:
inventory.shift(); // Returns "Motherboard"

// Membership testing:
console.log(inventory.includes("GPU")); // true
```

---

## 📦 3. Objects

An Object is an unordered collection of related data stored as **Key-Value pairs** (Properties):

```javascript
const server = {
    hostname: "prod-web-01",
    ip: "192.168.1.100",
    port: 443,
    isHealthy: true
};

// 1. Dot Notation (Standard & concise):
console.log(server.hostname); // "prod-web-01"

// 2. Bracket Notation (Required for dynamic keys or symbols):
console.log(server["ip"]); // "192.168.1.100"

const targetKey = "port";
console.log(server[targetKey]); // 443
```

### Modifying, Inserting & Deleting Properties:
```javascript
// Updating:
server.port = 8443;

// Inserting new property:
server.region = "us-east-1";

// Deleting:
delete server.isHealthy;
```

---

## 🌐 4. Composite Nesting: Arrays of Objects

In enterprise applications, REST endpoints typically serialize data as Arrays of Objects:

```javascript
const users = [
    { id: 101, username: "alice", role: "developer" },
    { id: 102, username: "bob", role: "analyst" },
    { id: 103, username: "charlie", role: "admin" }
];

for (const user of users) {
    console.log(`[ID: ${user.id}] ${user.username} (${user.role})`);
}
```

---

## 🎯 Summary & Lesson Challenge

- Arrays (`[]`) manage ordered, indexed sequences.
- Objects (`{}`) model key-value semantic entities.
- Combining both constructs forms the foundation of modern JSON data exchange.

**Challenge:** Create an array named `library` storing 3 book objects (`title`, `author`, `isRead`). Write a loop iterating over the collection that prints only the titles of books marked as `isRead === true`.

---

**Next Lesson:**  
⬅️ [Previous: Functions](./09-functions.md) | ➡️ [Next: String Handling](./11-string-handling.md)
