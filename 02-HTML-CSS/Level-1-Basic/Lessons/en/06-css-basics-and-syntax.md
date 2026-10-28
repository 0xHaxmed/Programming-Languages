# 06 - CSS Basics & Core Syntax

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-css-basics-and-syntax.md) | [العربية 🇸🇦](../ar/06-css-basics-and-syntax.md)

---

## 📌 1. What is CSS?

**CSS (Cascading Style Sheets)** is the presentation language used to describe how HTML elements are rendered on screen, paper, or in other media. While HTML defines the **structure and semantics**, CSS governs the **visual appearance, typography, layout, and responsiveness**.

---

## 🏗️ 2. Anatomy of a CSS Rule

A CSS rule consists of a **Selector** and a **Declaration Block**:

```css
selector {
    property: value;
    property: value;
}
```

```
     Selector         Declaration Block
   ┌──────────┐    ┌─────────────────────────┐
    h1.heading  {   color: #0284c7;           }
                    └────────────┘
                      Declaration
                    ├───┤  ├─────┤
                  Property  Value
```

### Components:
- **Selector:** Points to the HTML element(s) you want to style (e.g., `h1.heading`).
- **Property:** The visual feature you are configuring (e.g., `color`, `font-size`, `margin`).
- **Value:** The setting applied to that property (e.g., `#0284c7`, `1.5rem`).
- **Semicolon (`;`):** Must terminate every single declaration.

---

## 🔌 3. Three Methods to Apply CSS to HTML

### A. External Stylesheet (Recommended Industry Standard):
Linked inside `<head>` via `<link>`:
```html
<head>
    <link rel="stylesheet" href="styles.css">
</head>
```
- **Pros:** Clean separation of concerns, browser caching, site-wide consistency.
- **Security:** Compatible with strict Content Security Policy (CSP) configurations (`style-src 'self'`).

### B. Internal `<style>` Block:
Embedded directly inside `<head>`:
```html
<head>
    <style>
        body { background-color: #f8fafc; }
    </style>
</head>
```
- **Use Case:** Single-file self-contained demos or critical CSS inlining.

### C. Inline Style Attribute (Anti-pattern):
Applied directly onto an element tag:
```html
<p style="color: red; font-size: 14px;">Warning</p>
```
- **Why Avoid:** High maintenance cost, impossible to reuse, breaks separation of concerns, and is **blocked by standard security CSP policies** unless unsafe hashes or nonces are explicitly allowed!

---

## 🧹 4. Browser Defaults & The CSS Reset

Browsers ship with built-in default stylesheets called **User-Agent Stylesheets**. Different browsers (Chrome vs Safari vs Firefox) have slightly different default margins, paddings, and font sizes.

To achieve consistent rendering across all platforms, engineers use a minimal **CSS Reset**:

```css
/* Universal Box-Sizing & Reset */
*, *::before, *::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
```

---

## 🎯 Summary & Lesson Challenge

- CSS controls presentation and layout; HTML controls meaning.
- A CSS rule targets elements with a selector and configures properties with values.
- Always prefer external `.css` files over inline styles.
- Normalize browser default margins with a CSS reset.

**Challenge:** Create `index.html` and a separate `styles.css`. Link them using `<link rel="stylesheet">`, apply a background color to `body`, and style an `<h1>` with a custom color and font family.

---

**Next Lesson:**  
➡️ [Lesson 07: CSS Selectors, Cascade & Specificity](./07-css-selectors-and-specificity.md) | ⬅️ [Lesson 05: Lists & Tabular Data](./05-lists-and-tables.md)
