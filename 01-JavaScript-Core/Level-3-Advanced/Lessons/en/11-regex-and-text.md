# 11 - Regular Expressions & Text Processing: Modern Syntax, Lookarounds & ReDoS Prevention

> 🌐 **Language / اللغة:** [English 🇬🇧](./11-regex-and-text.md) | [العربية 🇸🇦](../ar/11-regex-and-text.md)

---

## 🎯 1. Overview & Learning Objectives

Regular Expressions (RegExp) are among the most powerful and dangerous tools in the JavaScript ecosystem. While essential for tokenization, input validation, and text extraction, poorly constructed regex patterns can inflict **Catastrophic Backtracking**—inducing a **Regular Expression Denial of Service (ReDoS)** that freezes the single-threaded V8 engine and crashes production services.

By the end of this lesson, you will master:
1. The internal mechanics of JavaScript's **NFA (Nondeterministic Finite Automaton)** backtracking regex engine.
2. Advanced assertions: **Positive/Negative Lookaheads** (`(?=...)`, `(?!...)`) and **Positive/Negative Lookbehinds** (`(?<=...)`, `(?<!...)`).
3. Modern ES2020+ features: **Named Capture Groups**, match indices (`/d`), and the Unicode set flag (`/v`).
4. Diagnosing, calculating, and eliminating **Catastrophic Backtracking (ReDoS)** vulnerabilities.
5. High-performance text parsing and replacement with `String.prototype.matchAll()` and safe replacer functions.

---

## 🧠 2. The NFA Backtracking Engine & Modern Flags

JavaScript uses an **NFA (Nondeterministic Finite Automaton)** engine. When an NFA engine encounters choices (alternations like `a|b` or quantifiers like `*` and `+`), it greedily consumes characters. If a downstream match fails, it **backtracks** to the previous choice and tests alternative branches.

### Modern RegExp Flags:
| Flag | Meaning | Added | Enterprise Use Case |
| :--- | :--- | :--- | :--- |
| **`g`** | Global | ES3 | Find all matches rather than stopping at the first. |
| **`i`** | Case-Insensitive | ES3 | Match upper and lower case uniformly. |
| **`m`** | Multiline | ES3 | Makes `^` and `$` match start and end of individual lines. |
| **`s`** | DotAll | ES2018 | Allows `.` to match newline characters (`\n`, `\r`). |
| **`u`** | Full Unicode | ES2015 | Correctly treats surrogate pairs (emojis, complex scripts) as single code points. |
| **`v`** | Unicode Sets | ES2024 | Upgrades `u` with set subtraction, intersection, and string properties. |
| **`d`** | Has Indices | ES2022 | Generates start and end substring index positions in `match.indices`. |

---

## 🔍 3. Advanced Assertions: Lookaheads & Lookbehinds

Lookarounds are **zero-width assertions**: they match a position *without* consuming characters into the match result.

```
+--------------------+----------------+------------------------------------------------+
| Assertion Type     | Syntax         | Matches position if:                          |
+--------------------+----------------+------------------------------------------------+
| Positive Lookahead | (?=pattern)    | What follows matches 'pattern'                 |
| Negative Lookahead | (?!pattern)    | What follows DOES NOT match 'pattern'          |
| Positive Lookbehind| (?<=pattern)   | What precedes matches 'pattern'                |
| Negative Lookbehind| (?<!pattern)   | What precedes DOES NOT match 'pattern'         |
+--------------------+----------------+------------------------------------------------+
```

### Practical Examples:
```javascript
// 1. Positive Lookbehind: Extract currency amounts without the currency symbol
const priceText = 'Item costs $150 or €140';
const prices = priceText.match(/(?<=\$|€)\d+/g);
console.log(prices); // ["150", "140"]

// 2. Positive Lookahead: Password complexity policy enforcement
// Must contain: 1 uppercase, 1 lowercase, 1 number, min 8 chars
const strongPasswordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[A-Za-z\d@$!%*?&]{8,}$/;
console.log(strongPasswordRegex.test('P@ssword123')); // true
console.log(strongPasswordRegex.test('weakpassword')); // false

// 3. Negative Lookahead: Find the word "Java" only when NOT followed by "Script"
const langRegex = /\bJava(?!Script)\b/g;
console.log('Java and JavaScript are different'.match(langRegex)); // ["Java"]
```

---

## 🏷️ 4. Named Capture Groups & Indices

Named capture groups provide readable, self-documenting code and eliminate brittle index-based extraction (`match[1]`, `match[2]`):

```javascript
const logEntry = '2026-11-08 [ERROR] Database timeout on shard-04';

// Syntax: (?<groupName>pattern)
const logRegex = /(?<date>\d{4}-\d{2}-\d{2})\s+\[(?<level>[A-Z]+)\]\s+(?<message>.+)/d;

const match = logRegex.exec(logEntry);

if (match) {
  // Destructure cleanly from match.groups:
  const { date, level, message } = match.groups;
  console.log({ date, level, message });
  // Output: { date: '2026-11-08', level: 'ERROR', message: 'Database timeout on shard-04' }

  // ES2022 Match Indices:
  console.log(match.indices.groups.level); // [ 12, 17 ] (Start & end indexes in string)
}
```

---

## 💥 5. Catastrophic Backtracking & ReDoS Prevention

A **Regular Expression Denial of Service (ReDoS)** occurs when a regex pattern contains **nested quantifiers** or overlapping ambiguities paired with an input that almost matches, but fails at the very end.

### The Vulnerability: $O(2^n)$ Exponential Complexity
Consider this innocent-looking pattern intended to validate a series of letters followed by an exclamation mark:

```javascript
// ⚠️ VULNERABLE TO ReDoS: Nested quantifiers ([a-z]+)+
const evilRegex = /^([a-zA-Z0-9]+)+$/;

// Matching valid string takes 0.01ms:
evilRegex.test('aaaaaaaaaaaaaaaaaaaaaaaaa'); 

// ❌ EVIL INPUT: Almost matches, but ends in invalid symbol '!'
// For an input of only 30 characters, the engine evaluates 2^30 (1,073,741,824) paths!
// evilRegex.test('aaaaaaaaaaaaaaaaaaaaaaaaaaaaaa!'); // FREEZES NODE.JS FOR MINUTES!
```

```
Path exploration tree:
                     (a+)
                  /        \
              (a)(a...a)  (aa)(a...a)
             /    \          /    \
           ...    ...       ...   ...
Total branches to verify failure: 2^N (Locks the event loop completely)
```

### The Defensive ReDoS Mitigation Playbook:

1. **Eliminate Nested Quantifiers:** Never write `(a+)+`, `(a*)*`, `(a|b+)+`, or `(.*a)+$`. Flatten repetitions into single, non-overlapping expressions:
   ```javascript
   // ❌ Bad: Nested quantifiers
   const badRegex = /^([a-z]+)+$/;

   // ✅ Safe: Single quantifier
   const safeRegex = /^[a-z]+$/;
   ```

2. **Bound Input Length:** Always check the input string's length before subjecting it to complex regex validation:
   ```javascript
   function validateInput(str) {
     if (typeof str !== 'string' || str.length > 256) {
       return false; // Reject oversized inputs immediately!
     }
     return sanitizedPattern.test(str);
   }
   ```

3. **Use Atomic Groups or Emulated Possessive Quantifiers:** If your regex matches a pattern, ensure it does not backtrack if downstream tokens fail.

---

## 🚀 6. Modern Text Processing with `matchAll`

When extracting multiple token occurrences with capture groups, `String.prototype.matchAll()` returns an iterator of detailed match objects:

```javascript
const markdownText = 'Check out [Google](https://google.com) and [GitHub](https://github.com)';

const linkRegex = /\[(?<text>[^\]]+)\]\((?<url>https?:\/\/[^\s)]+)\)/g;

for (const match of markdownText.matchAll(linkRegex)) {
  const { text, url } = match.groups;
  console.log(`Found link -> Text: "${text}", URL: "${url}"`);
}
// Output:
// Found link -> Text: "Google", URL: "https://google.com"
// Found link -> Text: "GitHub", URL: "https://github.com"
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Instantiating RegExp inside a high-frequency loop
function formatItems(items) {
  return items.map((item) => {
    const regex = new RegExp('[a-z]+', 'g'); // Compiles regex on EVERY iteration!
    return item.replace(regex, '');
  });
}

// ✅ GOOD: Define static RegExp once outside the loop
const TOKEN_REGEX = /[a-z]+/g;

function formatItems(items) {
  return items.map((item) => item.replace(TOKEN_REGEX, ''));
}

// ❌ BAD: Reading positional array indexes from capture groups
const [_, areaCode, localNumber] = match; // Fragile if regex changes!

// ✅ GOOD: Use Named Capture Groups
const { areaCode, localNumber } = match.groups;
```

---

## 📝 8. Summary & Next Steps

- JavaScript regex uses a backtracking **NFA engine**; understand how greedy tokens explore paths.
- Use **Lookaheads (`(?=...)`, `(?!...)`)** and **Lookbehinds (`(?<=...)`, `(?<!...)`)** for zero-width assertions.
- Adopt **Named Capture Groups (`(?<name>...)`)** for readable, self-documenting parsing.
- Always prevent **ReDoS** by eliminating nested quantifiers (`(a+)+`) and enforcing strict input length caps.
- Iterate over repeated matches with **`matchAll()`** instead of maintaining mutable state with `regex.exec()` loops.
- In the next lesson, we will dissect **Modern Bundling & Source Maps: Build Pipelines, Minification & Deployment Hygiene**.
