# Level 3 Testing, Assertions & Security Code Review

> 🌐 **Language / اللغة:** [English 🇬🇧](./README.md) | [العربية 🇸🇦](../ar/README.md)

---

## 🔍 Objectives of Testing & Code Review

This section trains engineers to navigate large-scale JavaScript codebases, construct resilient test harnesses, and sharply distinguish between standard logical bugs vs high-impact security vulnerabilities:

### 1. Data Flow Analysis & Sinks/Sources Mapping
- Systematically trace untrusted inputs originating from external **Sources** (`location.hash`, `URLSearchParams`, `window.addEventListener("message")`) as they traverse application logic into dangerous **Execution Sinks** (`innerHTML`, `document.write`, `eval`).

### 2. Prototype Pollution Mechanics & Auditing
- Dissect the programmatic mechanics behind vulnerable recursive object mergers (`deepMerge`) that permit attacker-controlled `__proto__` property injections altering root `Object.prototype`.

### 3. Edge Case & Boundary Assertion Suites
- Author unit test suites intentionally fuzzing boundary conditions: unhandled `null`/`undefined` references, negative numeric boundaries, circular object references, and regex backtracking timeouts.

---

⬅️ [Level 3 Lessons](../../Lessons/en/README.md) | 🏆 [Final Project: JavaScript Security Analyzer](../../Final-Project/en/README.md)
