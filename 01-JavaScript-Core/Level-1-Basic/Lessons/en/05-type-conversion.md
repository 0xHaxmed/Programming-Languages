# 05 - Type Conversion & Type Coercion

> 🌐 **Language / اللغة:** [English 🇬🇧](./05-type-conversion.md) | [العربية 🇸🇦](../ar/05-type-conversion.md)

---

## 🔄 1. What is Type Conversion?

In software development, converting a value from one data type to another is a frequent necessity (e.g., parsing a user text input into an integer for arithmetic calculation).

JavaScript features two paradigms of conversion:
1. **Explicit Conversion (Type Casting):** Triggered deliberately by the programmer using built-in constructors or helper functions.
2. **Implicit Conversion (Type Coercion):** Handled automatically behind the scenes by the runtime engine when operators encounter incompatible types.

---

## 🛠️ 2. Explicit Conversion

### A. To String
```javascript
const count = 42;
const s1 = String(count);        // "42"
const s2 = count.toString();     // "42"
const s3 = `${count}`;           // "42" (via template literal)
```

### B. To Number
```javascript
const raw = "150";
const n1 = Number(raw);          // 150
const n2 = parseInt("150.75px"); // 150 (extracts integer prefix)
const n3 = parseFloat("3.1415"); // 3.1415 (extracts decimal number)
const n4 = +"88";                // 88 (unary plus operator)

// Edge Cases:
Number("invalid");  // NaN (Not-a-Number)
Number(true);       // 1
Number(false);      // 0
Number(null);       // 0
Number(undefined);  // NaN
```

### C. To Boolean
```javascript
Boolean(1);          // true
Boolean(0);          // false
Boolean("hello");    // true
Boolean("");         // false
Boolean(null);       // false
Boolean(undefined);  // false
```

---

## 🎭 3. Implicit Type Coercion

JavaScript attempts to resolve operand mismatches dynamically. Understanding these coercion rules is essential for writing bug-free logic:

### A. The Addition Operator (`+`) & String Concatenation
If either operand in an addition operation is a String, JavaScript coerces the other operand into a String and executes concatenation:
```javascript
console.log(10 + "5");      // "105"
console.log("Total: " + 20); // "Total: 20"
console.log(1 + 2 + "3");    // "33" (1 + 2 = 3 evaluates first, then coerced to "33")
```

### B. Mathematical Operators (`-`, `*`, `/`, `%`)
Unlike `+`, subtraction, multiplication, and division always attempt numeric conversion:
```javascript
console.log("20" - 5);   // 15
console.log("6" * "4");  // 24
console.log("100" / "2");// 50
console.log("text" - 2); // NaN
```

### C. Boolean Coercion in Arithmetic
```javascript
console.log(true + 2);  // 3 (true coerces to 1)
console.log(false + 10); // 10 (false coerces to 0)
```

---

## ⚠️ 4. Coercion Gotchas & Common Pitfalls

```javascript
console.log([] + []);           // "" (empty string)
console.log([] + {});           // "[object Object]"
console.log(null == undefined);  // true (loose equality coercion)
console.log(null === undefined); // false (strict equality: distinct types)
```

> [!TIP]
> **Defensive Coding Guideline:**  
> Rely on explicit conversions (`Number()`, `String()`, `Boolean()`) and strictly favor the triple-equals comparison operator (`===`) over double-equals (`==`) to eliminate ambiguous coercion bugs.

---

## 🎯 Summary & Lesson Challenge

- Explicit conversion gives you explicit control over data types.
- Implicit coercion occurs automatically, with `+` prioritizing string concatenation.
- Non-numeric strings in arithmetic operations produce `NaN`.

**Challenge:** Predict the evaluated output of the following expressions before running them in the console:
1. `"" + 5 + 2`
2. `"" - 5 + 2`
3. `true + true + true`
4. `"120px" - 20`

---

**Next Lesson:**  
⬅️ [Previous: Data Types](./04-data-types.md) | ➡️ [Next: Operators & Expressions](./06-operators.md)
