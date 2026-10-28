# 🗺️ JavaScript Core Engineering Guide

> 🌐 **Language:** **English** | [🇸🇦 تصفح باللغة العربية (README.ar.md)](./README.ar.md)

Welcome to the **JavaScript Core Engineering Guide**!  
This repository is an open-source, dual-language pedagogical curriculum designed to take you from writing your first line of code to mastering browser runtime execution, asynchronous concurrency, and security-aware code analysis.

---

## 🧭 The Learning Roadmap (Where Are You & Where Are You Going?)

This curriculum is structured as a linear, milestone-driven progression. Follow the roadmap below:

```mermaid
flowchart TD
    Start(["🚀 START HERE: New to Programming or JS"]) --> L1["Level 1: Basic Logic & Syntax"]
    
    subgraph Level1 ["Level 1: Foundation (Pure Language)"]
        L1_A["Lessons 01-04<br/>• Introduction & V8<br/>• Syntax & Statements<br/>• Variables (let/const)<br/>• Primitive & Object Types"] --> L1_B["Lessons 05-08<br/>• Type Coercion<br/>• Operators & Strict Equality<br/>• Conditionals & Truthy/Falsy<br/>• Loops & Iteration"]
        L1_B --> L1_C["Lessons 09-12<br/>• Functions & Return<br/>• Arrays & Objects<br/>• String Manipulation<br/>• Errors & Debugging"]
        L1_C --> L1_Drills["5 Practical Problem-Solving Drills"]
    end

    L1_Drills --> L1_Project{{"🏆 Capstone 1: JavaScript Logic Toolkit<br/>(Pure functions + Zero-dependency Test Runner)"}}
    
    L1_Project --> Gate1{"Level 1 Readiness Gate:<br/>Can you write functions, loops,<br/>and data logic without copying?"}
    
    Gate1 -- "YES" --> L2["Level 2: Browser Environment & Web APIs"]
    Gate1 -- "NO / Review" --> L1

    subgraph Level2 ["Level 2: The Browser & Asynchronous Web"]
        L2_A["Advanced Core & Scope<br/>• Arrow Functions & Array Iterators<br/>• Scope Chains & Hoisting<br/>• Callbacks & References"] --> L2_B["Browser & DOM Mechanics<br/>• Window & Document Nodes<br/>• DOM CRUD & Event Bubbling/Delegation<br/>• Forms & URLSearchParams"]
        L2_B --> L2_C["Network & Async Architecture<br/>• JSON Serialization<br/>• Fetch API & HTTP Status Codes<br/>• Promises & Async/Await<br/>• Storage & SOP / CORS Boundaries"]
        L2_C --> L2_Labs["Applied DOM & DevTools Labs"]
    end

    L2_Labs --> L2_Project{{"🏆 Capstone 2: API Explorer<br/>(In-browser HTTP Dispatcher & JSON Viewer)"}}
    
    Gate2{"Level 2 Readiness Gate:<br/>Can you trace form data into an API<br/>and debug network calls in DevTools?"}
    
    L2_Project --> Gate2
    Gate2 -- "YES" --> L3["Level 3: Deep Runtime & Security Auditing"]
    Gate2 -- "NO / Review" --> L2

    subgraph Level3 ["Level 3: Internal Runtime Mechanics & Security"]
        L3_A["Execution Engine Deep Dive<br/>• Lexical Environment & Closures<br/>• The 4 Binding Rules of 'this'<br/>• Prototype Chains & Mutation<br/>• Event Loop Microtask Queues"] --> L3_B["Engineering Practices<br/>• ES Classes & Modules<br/>• Stack Trace Forensic Analysis<br/>• Unit Testing & Strict Assertions<br/>• Regex & ReDoS Prevention"]
        L3_B --> L3_C["Security-Aware Engineering<br/>• Minification & Source Maps<br/>• Auditing Dangerous Execution Sinks<br/>• Origin Verification & Safe DOM"]
        L3_C --> L3_Audits["Code Review & Data Flow Tracing"]
    end

    L3_Audits --> L3_Project{{"🏆 Capstone 3: JavaScript Security Analyzer<br/>(Static Application Security Testing Engine)"}}

    L3_Project --> Target(["🎯 DESTINATION: Mastered Core Runtime<br/>Ready for Client-Side Web Pentesting & Advanced Development"])
```

---

## 📂 Active Repository Structure

Every directory contains dedicated English (`/en`) and Arabic (`/ar`) documentation and code files:

```plaintext
Programming Languages/
├── README.md                                    # Root Navigation & Master Roadmap (English)
├── README.ar.md                                 # الدليل وخريطة المسار بالعربية
└── 01-JavaScript-Core/                          # Complete JavaScript Core Track
    ├── README.md                                # JS Core Roadmap & Syllabus (English)
    ├── README.ar.md                             # دليل جافا سكريبت بالعربية
    │
    ├── Level-1-Basic/                           # 🟢 LEVEL 1: Basic Logic & Syntax
    │   ├── README.md                            # English Overview & Checklist
    │   ├── README.ar.md                         # نظرة عامة بالعربية
    │   ├── Lessons/                             # 12 Complete Step-by-Step Lessons
    │   │   ├── en/ (01 to 12 .md files)
    │   │   └── ar/ (01 to 12 .md files)
    │   ├── Exercises/                           # 5 Problem-Solving Drills
    │   │   ├── en/README.md
    │   │   └── ar/README.md
    │   └── Final-Project/                       # Project: JavaScript Logic Toolkit
    │       ├── en/README.md
    │       └── ar/README.md
    │
    ├── Level-2-Foundation/                      # 🟡 LEVEL 2: Browser & Web APIs
    │   ├── README.md
    │   ├── README.ar.md
    │   ├── Lessons/ (en/ & ar/)                 # 15 Lessons Directory & Syllabus
    │   ├── Practical-Applications/ (en/ & ar/)  # Applied DOM & DevTools Labs
    │   └── Final-Project/ (en/ & ar/)           # Project: API Explorer
    │
    └── Level-3-Advanced/                        # 🔴 LEVEL 3: Internals & Security Auditing
        ├── README.md
        ├── README.ar.md
        ├── Lessons/ (en/ & ar/)                 # 13 In-Depth Lessons Syllabus
        ├── Testing-and-Review/ (en/ & ar/)      # Code Review & Audit Drills
        └── Final-Project/ (en/ & ar/)           # Project: JavaScript Security Analyzer
```

---

## 🚀 How to Navigate This Repository

1. **Step 1:** If you are starting fresh, jump directly into [`Level 1 — Lesson 01: Introduction`](./01-JavaScript-Core/Level-1-Basic/Lessons/en/01-introduction.md).
2. **Step 2:** Work through Lessons `01` to `12`, testing snippets locally in your browser console or Node.js.
3. **Step 3:** Complete the [`Level 1 Exercises`](./01-JavaScript-Core/Level-1-Basic/Exercises/en/README.md).
4. **Step 4:** Build the [`JavaScript Logic Toolkit`](./01-JavaScript-Core/Level-1-Basic/Final-Project/en/README.md) and ensure all assertion tests pass (`PASS`).
5. **Step 5:** Proceed to [`Level 2 — Foundation`](./01-JavaScript-Core/Level-2-Foundation/README.md) to conquer DOM, events, and asynchronous web communication.

---

<p align="center">
  Crafted with ❤️ for developers worldwide • Bilingual English / العربية
</p>
