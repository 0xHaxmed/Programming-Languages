# Level 2 — Foundation: The Browser Environment & Web APIs

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Educational Mission

- **Goal:** Master real-world JavaScript executing inside web browsers, bridging static scripting logic with interactive user events, the DOM, and asynchronous server communications.
- **Core Focus:** Event lifecycles, DOM tree operations, network requests via `fetch()`, DevTools forensics, and client-server security boundaries.

---

## 🧭 Level 2 Curriculum Directory

| # | Topic Domain | Detailed Focus Topics | Direct Reference |
| :-: | :--- | :--- | :---: |
| **01** | **Functions & Modern Data** | Arrow functions, functional array iterators (`map`, `filter`, `reduce`), destructuring, and spread/rest operators. | [English](./Lessons/en/01-functions-and-data.md) / [عربي](./Lessons/ar/01-functions-and-data.md) |
| **02** | **Scope & Execution Context** | Global, function, and block scope boundaries, and execution context hoisting mechanics. | [English](./Lessons/en/02-scope-and-hoisting.md) / [عربي](./Lessons/ar/02-scope-and-hoisting.md) |
| **03** | **Objects & References** | Nested object trees, heap memory pointers, shallow vs deep cloning. | [English](./Lessons/en/03-objects-and-references.md) / [عربي](./Lessons/ar/03-objects-and-references.md) |
| **04** | **Callbacks** | Passing functions as arguments, callback patterns, and foundational concurrency. | [English](./Lessons/en/04-callbacks.md) / [عربي](./Lessons/ar/04-callbacks.md) |
| **05** | **Browser Environment** | The `window` global and `document` node; delineating JS engine vs Web APIs. | [English](./Lessons/en/05-browser-environment.md) / [عربي](./Lessons/ar/05-browser-environment.md) |
| **06** | **DOM Tree Manipulation** | Node tree navigation, query selectors, element creation, dynamic styling, and deletion. | [English](./Lessons/en/06-dom-manipulation.md) / [عربي](./Lessons/ar/06-dom-manipulation.md) |
| **07** | **Events Architecture** | Event listeners, event lifecycle (capturing & bubbling phases), and event delegation patterns. | [English](./Lessons/en/07-events-system.md) / [عربي](./Lessons/ar/07-events-system.md) |
| **08** | **Forms & Inputs** | Form submission trapping (`preventDefault`), input state tracking, and sanitization validation. | [English](./Lessons/en/08-forms-and-validation.md) / [عربي](./Lessons/ar/08-forms-and-validation.md) |
| **09** | **URLs & Navigation** | Programmatic navigation via `window.location`, and query string parsing via `URLSearchParams`. | [English](./Lessons/en/09-urls-and-navigation.md) / [عربي](./Lessons/ar/09-urls-and-navigation.md) |
| **10** | **JSON** | Serialization/deserialization mechanics with `JSON.stringify` and `JSON.parse`. | [English](./Lessons/en/10-json.md) / [عربي](./Lessons/ar/10-json.md) |
| **11** | **Network Requests** | Network dispatching with `fetch()`, HTTP verbs, headers, status codes, and payload decoding. | [English](./Lessons/en/11-network-requests.md) / [عربي](./Lessons/ar/11-network-requests.md) |
| **12** | **Asynchronous Basics** | Asynchronous programming fundamentals: Promise states, chaining, and `async/await` syntax. | [English](./Lessons/en/12-async-basics.md) / [عربي](./Lessons/ar/12-async-basics.md) |
| **13** | **Browser Storage** | Client-side persistence: `localStorage`, `sessionStorage`, and Cookie primitives. | [English](./Lessons/en/13-browser-storage.md) / [عربي](./Lessons/ar/13-browser-storage.md) |
| **14** | **DevTools Debugging** | Advanced DevTools inspection: Sources panel breakpoints, call stack analysis, and Network tab forensics. | [English](./Lessons/en/14-devtools-debugging.md) / [عربي](./Lessons/ar/14-devtools-debugging.md) |
| **15** | **Browser Security Basics** | The Same-Origin Policy (SOP), Cross-Origin Resource Sharing (CORS), and trust boundaries. | [English](./Lessons/en/15-browser-security-basics.md) / [عربي](./Lessons/ar/15-browser-security-basics.md) |

---

## 🧪 Applied Browser Labs
Reinforce DOM security and asynchronous workflows via practical scenarios:
- 👉 [Applied Browser & DOM Labs (English)](./Practical-Applications/en/README.md)
- 👉 [معامل وتطبيقات الـ DOM باللغة العربية](./Practical-Applications/ar/README.md)

---

## 🏆 Capstone Project: API Explorer
Build and run a browser-based HTTP API Client & JSON Telemetry inspector:
- 👉 [English Project Guide & Architecture](./Final-Project/en/README.md)
- 👉 [دليل وكود المشروع بالعربية](./Final-Project/ar/README.md)

---

## ✅ Readiness Checklist Before Advancing to Level 3
- [ ] You can explain why `innerHTML` is insecure when processing dynamic user input.
- [ ] You can dispatch `fetch()` requests and handle network failures with `try/catch`.
- [ ] You know how to pause code execution using browser DevTools Breakpoints.
- [ ] You understand why CORS is an enforced browser policy rather than an error in JavaScript.
