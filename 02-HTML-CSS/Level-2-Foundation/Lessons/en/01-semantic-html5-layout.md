# 01 - Semantic HTML5 Structural Layout

> 🌐 **Language / اللغة:** [English 🇬🇧](./01-semantic-html5-layout.md) | [العربية 🇸🇦](../ar/01-semantic-html5-layout.md)

---

## 📌 1. Beyond the `<div>`: The Semantic Web

Before HTML5, developers built entire web layouts using generic `<div>` tags with arbitrary class names (`<div id="nav">`, `<div class="footer">`). To a web browser, search crawler, or screen reader, a `<div>` has **zero semantic meaning**—it is merely a blank styling bucket.

HTML5 introduced dedicated **Structural Sectioning Elements** that transform a web document into an accessible, navigable tree of semantic landmarks:

```
┌────────────────────────────────────────────────────────────┐
│                        <header>                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                       <nav>                          │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
┌──────────────────────────────────────────────┬─────────────┐
│                   <main>                     │   <aside>   │
│  ┌────────────────────────────────────────┐  │  (Sidebar,  │
│  │               <section>                │  │   Related)  │
│  │  ┌──────────────────┐ ┌─────────────┐  │  │             │
│  │  │    <article>     │ │  <article>  │  │  │             │
│  │  └──────────────────┘ └─────────────┘  │  │             │
│  └────────────────────────────────────────┘  │             │
└──────────────────────────────────────────────┴─────────────┘
┌────────────────────────────────────────────────────────────┐
│                        <footer>                            │
└────────────────────────────────────────────────────────────┘
```

---

## 🏗️ 2. The Semantic Landmark Elements

### A. `<header>`
Represents introductory content or a set of navigational links for its nearest sectioning ancestor or the page as a whole. Usually contains brand logo, search bar, and primary navigation.

### B. `<nav>`
Identifies a section intended only for major navigation links. Screen readers allow visually impaired users to jump directly past content to the `<nav>` landmark or skip it.

### C. `<main>`
Encloses the **unique, primary content** of the document.
- **Strict Rule:** There must be only **one** visible `<main>` element per document. It must not contain content repeated across pages (like sidebars, headers, or footers).

### D. `<section>` vs `<article>` (The Classic Distinction):
- **`<article>`:** Represents an **independent, self-contained composition** that could theoretically be syndicated, distributed, or read on its own (e.g., a blog post, news story, forum topic, product card).
- **`<section>`:** A **thematic grouping of related content**, typically with its own heading (e.g., "Features", "Pricing", "Customer Testimonials").

### E. `<aside>`
Contains content tangentially related to the content around it, which could be considered separate (e.g., sidebars, callout quotes, advertising, related links).

### F. `<footer>`
Represents closing metadata for its sectioning ancestor or page: copyright notices, author contact links, privacy policies, and terms of service.

---

## ♿ 3. Accessibility & SEO Benefits

1. **Screen Reader Landmarks:** Assistive technologies provide keyboard shortcuts allowing users to jump directly between `<main>`, `<nav>`, and `<aside>` without listening to entire page contents.
2. **Search Engine Indexing:** Crawlers prioritize text inside `<main>` and `<article>` over boilerplate text in `<footer>`.

---

## 🎯 Summary & Lesson Challenge

- Replace generic `<div>` wrappers with semantic landmarks.
- An `<article>` can stand alone; a `<section>` groups a theme with a heading.
- Only one `<main>` per page.

**Challenge:** Refactor a personal blog homepage layout using `<header>`, `<nav>`, `<main>`, two `<article>` cards inside a `<section>`, a sidebar `<aside>`, and a `<footer>`.

---

**Next Lesson:**  
➡️ [Lesson 02: HTML5 Forms & Accessible Input Elements](./02-html-forms-and-inputs.md) | 🏠 [Level 2 Index](../../README.md)
