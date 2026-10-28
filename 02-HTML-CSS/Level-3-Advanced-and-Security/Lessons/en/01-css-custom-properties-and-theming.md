# 01 - CSS Custom Properties (Variables) & Dynamic Theming

> 🌐 **Language / اللغة:** [English 🇬🇧](./01-css-custom-properties-and-theming.md) | [العربية 🇸🇦](../ar/01-css-custom-properties-and-theming.md)

---

## 📌 1. What are CSS Custom Properties?

Unlike preprocessor variables (Sass / Less) that compile into static values at build time, **CSS Custom Properties (CSS Variables)** live dynamically in the browser's DOM tree at runtime. They cascade, inherit down the tree, and can be read and manipulated instantly by CSS or JavaScript.

---

## 🏗️ 2. Declaring & Using Variables

Variables must begin with a double-hyphen (`--`):

```css
/* 1. Global Declaration on the root document node */
:root {
    --brand-primary: hsl(217, 91%, 60%);
    --brand-dark: hsl(224, 76%, 48%);
    --surface-bg: #ffffff;
    --text-color: hsl(222, 47%, 11%);
    --radius-sm: 0.25rem;
    --radius-md: 0.5rem;
}

/* 2. Usage with var() and optional fallbacks */
.button {
    background-color: var(--brand-primary);
    color: var(--surface-bg);
    border-radius: var(--radius-md, 4px); /* 4px fallback if variable is undefined */
}
```

---

## 🌳 3. Cascade & Component-Scoped Overrides

Because variables cascade, a child element can redefine a variable without touching the rest of the stylesheet:

```css
.card {
    --card-accent: #0284c7; /* Default accent */
    border-top: 4px solid var(--card-accent);
}

/* Modifier simply overrides the variable! */
.card--critical {
    --card-accent: #ef4444; /* Instantly turns the border red! */
}
```

---

## 🌓 4. Dynamic Theme Switching (Light / Dark Mode)

Using custom properties, toggling between light and dark themes requires zero changes to individual component selectors:

```css
:root {
    --bg-page: #f8fafc;
    --bg-card: #ffffff;
    --text-primary: #0f172a;
    --border-subtle: #e2e8f0;
}

/* Dark Theme Palette */
[data-theme="dark"] {
    --bg-page: #0f172a;
    --bg-card: #1e293b;
    --text-primary: #f8fafc;
    --border-subtle: #334155;
}

/* Components automatically adapt to whichever palette is active! */
body {
    background-color: var(--bg-page);
    color: var(--text-primary);
}

.panel {
    background-color: var(--bg-card);
    border: 1px solid var(--border-subtle);
}
```

---

## 🎯 Summary & Lesson Challenge

- CSS Custom Properties are dynamic, cascading, and live at runtime.
- Use `:root` for global design system tokens.
- Implement dark mode seamlessly by reassigning token values under `[data-theme="dark"]`.

**Challenge:** Create a card component with a title, body, and button. Define design tokens for background, text, and accent color. Add a theme switcher using a HTML attribute toggle `[data-theme="cyberpunk"]` that switches the palette to neon colors.

---

**Next Lesson:**  
➡️ [Lesson 02: BEM CSS Architecture & Scalability](./02-bem-architecture-and-maintainable-css.md) | 🏠 [Level 3 Index](../../README.md)
