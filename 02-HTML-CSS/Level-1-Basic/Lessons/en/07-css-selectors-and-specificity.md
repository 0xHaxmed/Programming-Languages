# 07 - CSS Selectors, Cascade & Specificity

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-css-selectors-and-specificity.md) | [العربية 🇸🇦](../ar/07-css-selectors-and-specificity.md)

---

## 📌 1. Basic & Combinator Selectors

CSS selectors match elements in the DOM tree based on tag name, class, identifier, relationships, or attributes:

### A. Core Selectors:
```css
* { /* Universal: matches every element */ }
p { /* Type: matches all <p> elements */ }
.btn { /* Class: matches any element with class="btn" */ }
#header { /* ID: matches the single element with id="header" */ }
```

### B. Combinators (DOM Relationships):
```css
/* Descendant: any <a> inside .nav (at any depth) */
.nav a { color: #0284c7; }

/* Direct Child: only direct <li> children of <ul> */
ul > li { list-style: square; }

/* Adjacent Sibling: the very first <p> immediately following <h2> */
h2 + p { font-size: 1.2rem; }

/* General Sibling: all <p> elements following <h2> at the same level */
h2 ~ p { line-height: 1.6; }
```

---

## 🔍 2. Attribute Selectors (Key Foundation for CSS Security)

Attribute selectors match elements based on the presence and values of HTML attributes:

```css
/* Exact match: input with type="password" */
input[type="password"] { border-color: #ef4444; }

/* Prefix match (^=): href starting with "https://" */
a[href^="https://"] { text-decoration: underline; }

/* Suffix match ($=): href ending with ".pdf" */
a[href$=".pdf"] { color: #dc2626; }

/* Substring match (*=): class containing "admin" */
div[class*="admin"] { background: #fef2f2; }
```

> 🛡️ **Security Foresight:** In Level 3, you will see how malicious attackers use prefix attribute selectors (`input[value^="a"]`) to secretly exfiltrate CSRF tokens and user passwords character-by-character without JavaScript!

---

## 🎭 3. Pseudo-classes & Pseudo-elements

- **Pseudo-classes (`:state`):** Match an element based on its dynamic state or position in the tree:
  - `:hover` — mouse cursor hovering over element.
  - `:focus` — element has keyboard focus (essential for accessible navigation!).
  - `:active` — element being clicked.
  - `:disabled` — form element currently inactive.
  - `:first-child` / `:nth-child(2n)` — positional styling.

- **Pseudo-elements (`::part`):** Style a specific sub-part or inject decorative content:
  - `::before` & `::after` — inject cosmetic content via `content: ""`.
  - `::placeholder` — style input placeholder text.

```css
.button {
    background-color: #0284c7;
    transition: background 0.2s ease;
}

.button:hover {
    background-color: #0369a1;
}

.button:focus-visible {
    outline: 2px solid #38bdf8;
    outline-offset: 2px;
}
```

---

## ⚖️ 4. The Specificity Calculation Formula

When multiple conflicting rules match the same element, CSS calculates a **Specificity Score**:

```
 ┌─────────────┬────────────────┬──────────────────────┬───────────────────────┐
 │ Inline (A)  │   IDs (B)      │ Classes, Attributes, │ Elements, Pseudo-     │
 │             │                │ Pseudo-classes (C)   │ elements (D)          │
 ├─────────────┼────────────────┼──────────────────────┼───────────────────────┤
 │ style="..." │ #main-nav      │ .btn, [type="text"], │ p, div, h1,           │
 │             │                │ :hover               │ ::before              │
 └─────────────┴────────────────┴──────────────────────┴───────────────────────┘
```

### Specificity Hierarchy:
1. `style=""` (Inline): **(1, 0, 0, 0)** — Always beats external rules.
2. `#id`: **(0, 1, 0, 0)**
3. `.class`, `[attr]`, `:hover`: **(0, 0, 1, 0)**
4. `element`, `::after`: **(0, 0, 0, 1)**

### Calculating Example:
- `nav ul li a` = 4 elements = **(0, 0, 0, 4)**
- `nav ul li a.active` = 1 class + 4 elements = **(0, 0, 1, 4)** (Wins!)
- `#main-nav a.active` = 1 ID + 1 class + 1 element = **(0, 1, 1, 1)** (Crushes previous!)

> ⚠️ **The `!important` Danger:** Never use `!important` to force overrides in ordinary development. It breaks the cascade and creates impossible-to-maintain stylesheets. Solve specificity issues by refining class names (e.g., using BEM architecture).

---

## 🎯 Summary & Lesson Challenge

- Combinators define relationships in the DOM tree (`>`, `+`, `~`).
- Attribute selectors target values dynamically.
- Understand the 4-tier specificity model to avoid specificity wars.

**Challenge:** Write HTML containing a list of links. Write CSS that turns HTTPS links green (`[href^="https://"]`), PDF links red (`[href$=".pdf"]`), and changes background color on `:hover` and `:focus`.

---

**Next Lesson:**  
➡️ [Lesson 08: The CSS Box Model & Sizing](./08-the-box-model.md) | ⬅️ [Lesson 06: CSS Basics & Core Syntax](./06-css-basics-and-syntax.md)
