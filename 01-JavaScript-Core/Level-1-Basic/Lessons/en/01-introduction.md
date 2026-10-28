# 01 - Introduction to JavaScript & Runtime Environment

> 🌐 **Language / اللغة:** [English 🇬🇧](./01-introduction.md) | [العربية 🇸🇦](../ar/01-introduction.md)

---

## 📌 1. What is JavaScript?

**JavaScript (JS)** is a lightweight, high-level, interpreted (or JIT-compiled), multi-paradigm programming language.  
Created in 1995 by Brendan Eich at Netscape, it was originally designed to bring static web pages to life by enabling interactive client-side behaviors.

> [!NOTE]
> **Key Distinction:**  
> **Java** is completely different from **JavaScript**. They differ in syntax, architecture, and purpose. The naming was purely a marketing strategy in 1995.

---

## 🏗️ 2. The Relationship Between HTML, CSS, and JavaScript

Modern web applications are built upon three core layers:

1. **HTML (Structure):** Defines the layout, content, elements, and semantic markup (headings, paragraphs, buttons, forms).
2. **CSS (Presentation):** Dictates appearance, styling, responsive layouts, colors, and typography.
3. **JavaScript (Behavior & Logic):** Drives user interaction, async data fetching without page reloads, DOM updates, and business logic.

```
   [ HTML ]      +      [ CSS ]      +      [ JavaScript ]
 (Structure)           (Presentation)            (Behavior)
```

---

## ⚙️ 3. Where Does JavaScript Run?

### A. Inside the Browser (Client-Side)
Every modern web browser ships with an embedded **JavaScript Engine**:
- **V8:** Google Chrome, Microsoft Edge, Brave, Opera.
- **SpiderMonkey:** Mozilla Firefox.
- **JavaScriptCore (WebKit / Nitro):** Apple Safari.

### B. Outside the Browser (Server-Side / Desktop)
With runtimes like **Node.js**, **Deno**, and **Bun**, JavaScript can run directly on operating systems. This powers:
- Backend servers and REST/GraphQL APIs.
- Cross-platform desktop apps (e.g., VS Code, Discord using Electron).
- Automation scripts and CLI tools.

---

## 🚀 4. Executing Your First Code

### Method 1: Browser Developer Tools (Console)
1. Open any browser tab.
2. Press `F12` (or right-click -> **Inspect**) and navigate to the **Console** tab.
3. Enter the following line and hit `Enter`:

```javascript
console.log("Welcome to JavaScript Core Fundamentals!");
```

### Method 2: HTML with Inline `<script>`
Create an `index.html` file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>First JS Run</title>
</head>
<body>
    <h1>Hello, World!</h1>

    <script>
        console.log("JavaScript executed successfully within the HTML document!");
    </script>
</body>
</html>
```

---

## 🎯 Summary & Lesson Challenge

- JavaScript delivers dynamic behavior and computational logic to the web and backend systems.
- HTML constructs the skeleton, CSS handles the visuals, and JS drives functionality.
- Runtimes like browser engines and Node.js parse, compile, and execute JS code.

**Challenge:** Open your browser console, print your name and target learning goals using `console.log()`, and test triggering a dialog box with `alert("Hello from JS!")`.

---

**Next Lesson:**  
➡️ [Lesson 02: Syntax, Statements, and Expressions](./02-syntax.md) | 🏠 [Level 1 Index](../../README.md)
