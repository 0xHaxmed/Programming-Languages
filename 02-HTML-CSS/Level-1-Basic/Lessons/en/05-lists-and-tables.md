# 05 - Lists & Tabular Data

> 🌐 **Language / اللغة:** [English 🇬🇧](./05-lists-and-tables.md) | [العربية 🇸🇦](../ar/05-lists-and-tables.md)

---

## 📌 1. Lists: Structuring Groups of Items

HTML provides three types of list structures, each conveying a specific semantic relationship:

### A. Unordered Lists (`<ul>`):
Used when the order of items does not matter (bulleted by default):
```html
<ul>
    <li>Content Security Policy (CSP)</li>
    <li>Subresource Integrity (SRI)</li>
    <li>Strict-Transport-Security (HSTS)</li>
</ul>
```

### B. Ordered Lists (`<ol>`):
Used when the sequence or ranking matters (numbered by default):
```html
<ol>
    <li>Discover injection point (Source)</li>
    <li>Trace execution flow through DOM</li>
    <li>Reach dangerous execution Sink</li>
</ol>
```

### C. Description Lists (`<dl>`):
Used for key-value pairs, glossaries, or metadata sets:
```html
<dl>
    <dt>XSS</dt>
    <dd>Cross-Site Scripting, an injection flaw executing client-side scripts.</dd>
    <dt>CSRF</dt>
    <dd>Cross-Site Request Forgery, tricking users into executing unintended actions.</dd>
</dl>
```

---

## 📊 2. Accessible Data Tables (`<table>`)

> ⚠️ **Golden Rule:** **NEVER use tables for page layout.** Tables must be used exclusively to represent two-dimensional tabular data.

### Standard Accessible Table Structure:
```html
<table>
    <caption>Quarterly Vulnerability Audit Report</caption>
    <thead>
        <tr>
            <th scope="col">Vulnerability Type</th>
            <th scope="col">Severity</th>
            <th scope="col">Status</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">DOM XSS</th>
            <td>High</td>
            <td>Remediated</td>
        </tr>
        <tr>
            <th scope="row">Open Redirect</th>
            <td>Low</td>
            <td>Under Review</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td colspan="2">Total Audited Findings</td>
            <td>2</td>
        </tr>
    </tfoot>
</table>
```

### Accessibility Essentials:
- **`<caption>`:** Gives a clear title for screen reader users before reading table contents.
- **`<thead>`, `<tbody>`, `<tfoot>`:** Semantically partitions header rows, data rows, and summary totals.
- **`<th scope="col">` and `<th scope="row">`:** Explicitly tells screen readers which data cells belong to which headers.
- **`colspan` / `rowspan`:** Merges cells across columns or rows.

---

## 🎯 Summary & Lesson Challenge

- Use `<ul>` for unranked sets, `<ol>` for ranked steps, and `<dl>` for key-value terms.
- Use `<table>` only for tabular data, always incorporating `<caption>`, `<thead>`, `<tbody>`, and `<th scope="...">`.

**Challenge:** Create an HTML table listing 3 web security vulnerabilities with columns for `Name`, `OWASP Category`, and `CVSS Score`. Use proper semantic tags and cell scopes.

---

**Next Lesson:**  
➡️ [Lesson 06: CSS Basics & Core Syntax](./06-css-basics-and-syntax.md) | ⬅️ [Lesson 04: Media Elements](./04-media-images-and-audio-video.md)
