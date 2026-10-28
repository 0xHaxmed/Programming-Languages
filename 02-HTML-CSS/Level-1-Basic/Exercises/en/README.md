# Level 1 Practical Drills: HTML5 & CSS Foundations

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Objective
Reinforce your core understanding of HTML semantics, secure anchor hygiene, media sizing, accessible tables, and the CSS Box Model through 5 targeted hands-on drills.

---

## 🏋️ Drill 1: Refactoring "Div Soup" into Semantic HTML5

### Problem Statement:
A legacy developer wrote the following markup using only generic `<div>` tags:
```html
<div class="header">
    <div class="logo">DevSecOps</div>
</div>
<div class="title">Web Security Foundations</div>
<div class="content">
    <div class="paragraph">Cross-Site Scripting occurs when untrusted input is executed.</div>
</div>
<div class="footer">
    <div class="copyright">&copy; 2026 DevSecOps</div>
</div>
```

### Your Task:
Rewrite this structure using semantic HTML5 tags: `<header>`, `<nav>`, `<h1>`, `<main>`, `<p>`, and `<footer>`. Explain why semantic tags benefit screen readers and search engines.

---

## 🏋️ Drill 2: Secure External Link & Resilient Media Card

### Problem Statement:
Build an author profile snippet containing:
1. An author image (`width="120"`, `height="120"`, meaningful `alt`, `loading="lazy"`).
2. A brief bio paragraph with `<strong>` and `<code>` highlights.
3. An external link to their GitHub profile opening in a new tab.

### Security Requirement:
Your anchor tag **must** mitigate the reverse tabnabbing vulnerability by using `target="_blank"` with `rel="noopener noreferrer"`.

---

## 🏋️ Drill 3: Accessible Vulnerability Assessment Table

### Problem Statement:
Construct an HTML table that reports 3 security findings:
- Finding 1: DOM XSS | High Severity | Remediated
- Finding 2: Missing CSRF Token | Medium Severity | In Progress
- Finding 3: Information Disclosure | Low Severity | Accepted Risk

### Accessibility Requirements:
- Must contain a `<caption>` explaining the report.
- Must partition `<thead>`, `<tbody>`, and `<tfoot>`.
- Must assign `scope="col"` to column headers and `scope="row"` to finding names.
- Must use `colspan` in the footer row to summarize total findings.

---

## 🏋️ Drill 4: Precision Box Model Calculation

### Problem Statement:
You have a design specification for a card:
- Total width on screen: **360px**
- Left & Right Padding: **24px each**
- Left & Right Border: **2px solid #cbd5e1**
- Background: `#f8fafc`

### Your Task:
Write the CSS rules for `.spec-card`:
1. Use `box-sizing: border-box`.
2. Inspect the element in Chrome DevTools (Elements -> Computed).
3. Confirm that the total rendered width is exactly 360px without manual subtraction.

---

## 🏋️ Drill 5: Pure CSS Horizontal Navigation Bar

### Problem Statement:
Convert a vertical HTML `<ul>` list into a sleek, accessible horizontal navigation menu:
```html
<nav class="site-nav">
    <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#docs">Documentation</a></li>
        <li><a href="#tools">Tools</a></li>
        <li><a href="#security">Security</a></li>
    </ul>
</nav>
```

### Styling Requirements:
1. Remove default list bullets (`list-style: none`) and margin/padding.
2. Use `display: inline-block` on the links or list items so they flow horizontally.
3. Apply comfortable padding and a subtle `:hover` background color.
4. Add an accessible `:focus-visible` outline for keyboard navigation.

---

**Next Step:**  
🏆 [Build the Level 1 Capstone Project: Developer Portfolio Card](../Final-Project/en/README.md)
