# 03 - Prototypes, Inheritance & The Prototype Chain

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-prototypes-and-inheritance.md) | [العربية 🇸🇦](../ar/03-prototypes-and-inheritance.md)

---

## 🎯 1. Overview & Learning Objectives

Unlike languages like Java or C++ that use classical class-based inheritance (where classes act as static blueprints copied during instantiation), JavaScript implements **Prototypal Inheritance**.

In JavaScript, objects inherit directly from other live objects via an internal link known as the **Prototype Chain**. Understanding prototype resolution is foundational for architecting scalable codebases and securing applications against prototype-based vulnerabilities.

By the end of this lesson, you will master:
1. The architectural distinction between Prototypal Delegation and Classical Inheritance.
2. Differentiating `[[Prototype]]`, `__proto__`, and `Constructor.prototype`.
3. How the engine resolves properties through the **Prototype Chain**.
4. The mechanics of **Property Shadowing** and ownership verification via `Object.hasOwn()`.
5. Creating prototype-less dictionary objects with `Object.create(null)`.
6. Why modifying built-in prototypes (**Monkey-Patching**) is an anti-pattern.

---

## 🔗 2. The Prototype Chain: Delegation from First Principles

Every JavaScript object has an internal hidden property named `[[Prototype]]` that points to another object (or `null`):

```
+------------------+         +-------------------------+         +--------------------+         +------+
|   userInstance   | ------> |  UserProfile.prototype  | ------> |  Object.prototype  | ------> | null |
|  { name: 'Ali' } | [[proto]]|  { getRole: fn() }     | [[proto]]|  { toString: fn() }| [[proto]]|      |
+------------------+         +-------------------------+         +--------------------+         +------+
```

### Property Lookup Resolution:
When you query `userInstance.getRole()`:
1. Does `getRole` exist directly on `userInstance` (Own Property)? **No.**
2. The engine follows `[[Prototype]]` to `UserProfile.prototype`. Does it exist there? **Yes!**
3. It executes the method, binding `this` to `userInstance`.
4. If it traversed all the way to `Object.prototype` without finding the property, it returns `undefined`.

---

## 🧬 3. `prototype` vs `__proto__` vs `Object.getPrototypeOf()`

Three related terms frequently confuse developers:

```
Term                          What It Actually Is
Constructor.prototype         A property on constructor FUNCTIONS used as the [[Prototype]]
                              for objects created with 'new Constructor()'.

obj.__proto__                 A legacy getter/setter on Object.prototype exposing [[Prototype]].
                              (Deprecated in modern specs; avoid using in production).

Object.getPrototypeOf(obj)    The official, modern standard API to inspect an object's prototype.
```

### Constructor Implementation:
```javascript
function Device(brand) {
  this.brand = brand; // Own Property
}

// Attach shared methods to the prototype (Allocated ONCE in Heap memory!):
Device.prototype.powerOn = function () {
  console.log(`${this.brand} is now powering on.`);
};

const laptop = new Device('ThinkPad');
const phone = new Device('Pixel');

// Both instances share the exact same function reference in memory:
console.log(laptop.powerOn === phone.powerOn); // true!
laptop.powerOn(); // "ThinkPad is now powering on."
```

---

## 👥 4. Property Shadowing & `Object.hasOwn()`

If you define a property directly on an object with the same name as one on its prototype, the own property **shadows (hides)** the prototype property without modifying the prototype:

```javascript
laptop.powerOn = function () {
  console.log('Custom ThinkPad turbo boot!');
};

laptop.powerOn(); // "Custom ThinkPad turbo boot!" (Shadowed!)
phone.powerOn();  // "Pixel is now powering on." (Original prototype intact!)
```

### Checking Property Ownership: `Object.hasOwn()`
Introduced in **ES2022**, `Object.hasOwn(obj, prop)` is the safe, modern replacement for the legacy `obj.hasOwnProperty()`:

```javascript
console.log(Object.hasOwn(laptop, 'brand'));   // true (Own property)
console.log(Object.hasOwn(laptop, 'powerOn')); // true (Shadowed own property)
console.log(Object.hasOwn(phone, 'powerOn'));  // false (Inherited from prototype)
```

> [!TIP]
> **Why `Object.hasOwn` replaced `hasOwnProperty`:**  
> If an object is created with `Object.create(null)` or has a custom key named `hasOwnProperty`, calling `obj.hasOwnProperty()` throws a `TypeError`. `Object.hasOwn()` is completely immune to this problem.

---

## 🛡️ 5. Prototype-Less Dictionaries: `Object.create(null)`

Normal objects inherit properties like `toString`, `valueOf`, and `constructor` from `Object.prototype`:
```javascript
const normalObj = {};
console.log('toString' in normalObj); // true (Inherited from Object.prototype!)
```

In security-sensitive contexts (such as key-value lookups, hash tables, and parsers), inheritances can introduce bugs or prototype pollution. You can create a **pure dictionary with NO prototype link**:

```javascript
const secureMap = Object.create(null);

console.log(Object.getPrototypeOf(secureMap)); // null
console.log('toString' in secureMap);          // false! Clean slate.

// Safe from prototype collisions:
secureMap['item1'] = 'Data';
```

---

## 🚫 6. Anti-Pattern: Monkey-Patching Built-In Prototypes

Never modify or add methods to standard JavaScript prototypes (`Array.prototype`, `Object.prototype`, `String.prototype`):

```javascript
// ❌ DANGEROUS MONKEY-PATCHING:
Array.prototype.last = function() {
  return this[this.length - 1];
};

// Why this is forbidden in enterprise engineering:
// 1. Collides with future official ECMAScript language additions.
// 2. Breaks third-party libraries running in the same runtime.
// 3. Can introduce security vulnerabilities (Prototype Pollution).
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Checking property existence with 'in' operator (checks inherited properties!)
if ('toString' in requestPayload) {
  // Evaluates to true even if user never passed 'toString'!
}

// ✅ GOOD: Using Object.hasOwn for strict own-property verification
if (Object.hasOwn(requestPayload, 'targetId')) {
  processTarget(requestPayload.targetId);
}

// ❌ BAD: Re-creating identical methods inside constructors (Wastes Heap memory!)
function User(name) {
  this.name = name;
  this.login = function() { /* allocates duplicate function per user */ };
}

// ✅ GOOD: Attaching shared methods to the prototype (or using ES6 Classes)
function User(name) {
  this.name = name;
}
User.prototype.login = function() { /* shared reference */ };
```

---

## 📝 8. Summary & Next Steps
- JavaScript uses **Prototypal Delegation**: objects delegate missing lookups to their `[[Prototype]]`.
- **`Constructor.prototype`** sets the prototype for objects created with `new`.
- Use **`Object.hasOwn(obj, prop)`** to reliably verify own properties without prototype inheritance.
- Use **`Object.create(null)`** to instantiate clean, prototype-free dictionaries.
- **Never mutate built-in prototypes** (`Monkey-Patching`).
- In the next lesson, we will explore **Advanced Functional Paradigms: Currying, Composition & Higher-Order Functions**.
