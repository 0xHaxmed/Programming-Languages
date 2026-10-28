# 02 - Headings, Paragraphs & Text Semantics

> 🌐 **Language / اللغة:** [English 🇬🇧](./02-text-headings-and-paragraphs.md) | [العربية 🇸🇦](../ar/02-text-headings-and-paragraphs.md)

---

## 📌 1. Headings Hierarchy (`<h1>` to `<h6>`)

HTML provides six levels of document headings. Headings are not merely for visual sizing—they define the outline and information hierarchy of your document:

```html
<h1>Main Document Title (Only ONE per page)</h1>
<h2>Major Section Topic</h2>
<h3>Subsection Detail</h3>
<h4>Sub-component Topic</h4>
<h5>Minor Grouping</h5>
<h6>Deep Detail</h6>
```

### Best Practices & Accessibility:
- **Never skip heading levels** (e.g., jumping from `<h2>` directly to `<h4>`). Screen readers rely on sequential outlines to help visually impaired users navigate.
- **Single `<h1>` Rule:** Reserve `<h1>` for the primary topic of the page (crucial for SEO and a11y).
- **Style with CSS, not Tags:** Do not use `<h3>` just because you want small text; use CSS `font-size` for styling.

---

## 📝 2. Paragraphs & Line Breaks

- **Paragraph `<p>`:** Represents a block of text. Browsers automatically add margin before and after paragraphs.
- **Line Break `<br>`:** Forces a line break within a text flow without creating a new paragraph.
- **Horizontal Rule `<hr>`:** Represents a thematic break or transition between topics.

```html
<p>
    HyperText Markup Language is the backbone of the web.<br>
    It provides structure to arbitrary content.
</p>
<hr>
<p>This is a new thematic topic.</p>
```

---

## 🏷️ 3. Semantic Inline Formatting

Modern HTML separates **presentation** from **semantic meaning**:

| Tag | Purpose / Meaning | Visual Default | When to Use |
| :--- | :--- | :--- | :--- |
| `<strong>` | High importance, seriousness, or urgency | **Bold** | Warnings, critical terms |
| `<b>` | Stylistic bolding without extra importance | **Bold** | Keyword formatting, product names |
| `<em>` | Stress emphasis (changes spoken inflection) | *Italics* | Emphasized speech in sentences |
| `<i>` | Alternate voice, tone, or technical term | *Italics* | Technical terms, thoughts, taxonomy |
| `<mark>` | Highlighted text for reference | Yellow background | Search result hits |
| `<code>` | Inline code snippet | Monospace | Function names, variable identifiers |
| `<blockquote>` | Extended external quotation | Indented block | Multi-line quotes with `cite` attribute |

### Example:
```html
<p>
    <strong>Warning:</strong> Always validate user input. Use <code>DOMPurify</code> to sanitize untrusted strings.
</p>
<blockquote cite="https://www.w3.org">
    "The power of the Web is in its universality."
</blockquote>
```

---

## 🎯 Summary & Lesson Challenge

- Use `<h1>` to `<h6>` strictly for semantic outlines, never for font sizing.
- Distinguish between semantic tags (`<strong>`, `<em>`) and pure presentation (`<b>`, `<i>`).
- Use `<mark>` and `<code>` to communicate textual context clearly.

**Challenge:** Create a mini biography page containing one `<h1>`, two `<h2>` sections, paragraphs with bold/italic emphasis, and an inline `<code>` tag.

---

**Next Lesson:**  
➡️ [Lesson 03: Hyperlinks & Secure Anchors](./03-links-and-anchors.md) | ⬅️ [Lesson 01: HTML Anatomy](./01-html-anatomy-and-document.md)
