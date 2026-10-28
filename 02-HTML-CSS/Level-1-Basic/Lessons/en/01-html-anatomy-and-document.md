# 01 - HTML Anatomy & Document Structure

> 🌐 **Language / اللغة:** [English 🇬🇧](./01-html-anatomy-and-document.md) | [العربية 🇸🇦](../ar/01-html-anatomy-and-document.md)

---

## 📌 1. What is HTML?

**HTML (HyperText Markup Language)** is the standard markup language used to structure web documents. It is **not** a programming language; it is a declarative markup system that instructs browser layout engines on how to assemble, parse, and present text, images, forms, and interactive components.

---

## 🏗️ 2. Anatomy of an HTML Element

An HTML element consists of an opening tag, optional attributes, the inner content, and a closing tag:

```
    Opening Tag         Content         Closing Tag
      ┌──────┐      ┌─────────────┐       ┌───────┐
      <p class="intro">Welcome to Web</p>
         └───────────┘
           Attribute
```

### Key Rules:
1. **Attributes (`name="value"`):** Provide configuration metadata (e.g., `class`, `id`, `href`, `src`).
2. **Void / Self-Closing Elements:** Elements that hold no inner textual content do not require a closing tag (e.g., `<img />`, `<input />`, `<br />`, `<meta />`).

---

## 📄 3. The Minimal Standard HTML5 Document Skeleton

Every standard HTML5 document follows this universal blueprint:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML5 Document Blueprint</title>
</head>
<body>
    <h1>Hello, Modern Web!</h1>
    <p>This is my first semantic HTML5 document.</p>
</body>
</html>
```

### Deconstructing the Skeleton:
- **`<!DOCTYPE html>`:** Informs the browser layout engine to render in **Standard Mode** rather than Quirks Mode.
- **`<html lang="en">`:** Root container node. The `lang` attribute is essential for screen readers (accessibility) and search engine indexing.
- **`<head>`:** Container for metadata, stylesheets, character encoding, and title. Nothing inside `<head>` is directly rendered in the visible viewport.
- **`<meta charset="UTF-8">`:** Enforces standard UTF-8 character encoding, correctly rendering Arabic, Chinese, emojis, and symbols.
- **`<meta name="viewport" content="width=device-width, initial-scale=1.0">`:** Critical for **Mobile-First Responsive Design**, ensuring mobile devices match viewport width to physical screen width.
- **`<body>`:** All visible DOM nodes that users see and interact with.

---

## 🎯 Summary & Lesson Challenge

- HTML defines semantic document structures.
- Attributes configure element behaviors and metadata.
- Always include `DOCTYPE`, `lang`, `charset="UTF-8"`, and the mobile `viewport` tag.

**Challenge:** Create an `index.html` file on your computer, paste the skeleton above, change the title to your name, and open it in Google Chrome or Firefox.

---

**Next Lesson:**  
➡️ [Lesson 02: Headings, Paragraphs & Text Semantics](./02-text-headings-and-paragraphs.md) | 🏠 [Level 1 Index](../../README.md)
