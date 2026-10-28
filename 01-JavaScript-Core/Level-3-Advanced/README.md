# Level 3 — Advanced: Deep Runtime Mechanics & Security

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Educational Mission

- **Goal:** Unpack the internal mechanical behavior of JavaScript engines (V8), trace asynchronous concurrency queues, audit prototype inheritance chains, and master static code security analysis.
- **Core Focus:** Execution contexts, lexical closures, the `this` binding model, Event Loop microtask scheduling, prototype pollution primitives, and security-aware code refactoring.

---

## 🧭 Level 3 Curriculum Directory

| # | Topic Domain | Detailed Syllabus Topics | Direct Reference |
| :-: | :--- | :--- | :--- |
| **01** | **Execution & Lexical Scope** | Lexical Environments, Scope Chains, Closures memory structures, and advanced Hoisting mechanics. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **02** | **The `this` Keyword** | The 4 binding rules of `this` (Default, Implicit, Explicit via `call`/`apply`/`bind`, and Lexical Arrow Functions). | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **03** | **Prototypes & Inheritance** | Prototype chains (`__proto__` vs `prototype`), property shadow inheritance, and the runtime mechanics of Prototype Pollution. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **04** | **Advanced Functions** | Higher-Order Functions, function composition, currying, and functional pipeline paradigms. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **05** | **Advanced Asynchronous Flow** | Promise lifecycle states, error propagation flows, `Promise.allSettled`, and async pipeline controls. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **06** | **Event Loop & Concurrency** | V8 concurrency: Call Stack, Web APIs, Macrotask queue vs Microtask queue timing and starvation. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **07** | **Classes & ES Modules** | ES6 Class abstractions, private fields (`#`), inheritance, and ES Module semantics (`import`/`export`). | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **08** | **Error Analysis & Tracing** | Deep stack trace forensics, custom Error subclassing, and fatal exception mitigation. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **09** | **Code Review & Flow Analysis** | Architectural codebase navigation, Data Flow Analysis, Sinks/Sources tracing, and clean refactoring. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **10** | **Testing & Strict Assertions** | Unit testing harness architecture, strict assertion validation, and defensive edge-case testing. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **11** | **Regex & Pattern Matching** | Advanced Regular Expression engines, capture groups, lookaheads, and Regular Expression Denial of Service (ReDoS) prevention. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **12** | **Bundling & Source Maps** | Minification, tree-shaking, bundler outputs (Webpack, Vite), and Source Map symbol resolution. | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |
| **13** | **Security-Aware Coding** | Defensive DOM and string handling, auditing dangerous API sinks (`eval`, `new Function`, unvalidated `postMessage` origins). | [English](./Lessons/en/README.md) / [عربي](./Lessons/ar/README.md) |

---

## 🔍 Testing, Verification & Code Auditing Labs
Deepen your architectural inspection skills:
- 👉 [Level 3 Testing & Security Review (English)](./Testing-and-Review/en/README.md)
- 👉 [معمل مراجعة الأكواد باللغة العربية](./Testing-and-Review/ar/README.md)

---

## 🏆 Capstone Project: JavaScript Security Analyzer
Build and execute a static security analysis tool (SAST) auditing source code for hazardous patterns:
- Detects dangerous execution sinks (`eval`, `new Function`).
- Identifies unvalidated `postMessage` event listeners.
- Flags potential DOM XSS and Prototype Pollution vectors.
- 👉 [English Project Guide & Engine](./Final-Project/en/README.md)
- 👉 [دليل وكود الأداة بالعربية](./Final-Project/ar/README.md)

---

## 🎓 Final Track Graduation Benchmark
Upon concluding Level 3 and passing all static analyzer test benchmarks, you have officially mastered the complete **JavaScript Core Foundation**. You are fully equipped to specialize in client-side application security: **JavaScript for Web Pentesting**!
