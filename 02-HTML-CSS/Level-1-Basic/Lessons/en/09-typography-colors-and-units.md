# 09 - Typography, Units & Color Models

> 🌐 **Language / اللغة:** [English 🇬🇧](./09-typography-colors-and-units.md) | [العربية 🇸🇦](../ar/09-typography-colors-and-units.md)

---

## 📌 1. Units of Measurement: Absolute vs Relative

Choosing the correct CSS unit is vital for accessibility, maintainability, and responsiveness:

| Unit | Type | Base Reference | Ideal Use Case |
| :--- | :--- | :--- | :--- |
| `px` | Absolute | Physical screen pixels | Borders (`1px solid`), small fixed icons |
| `rem` | Relative | Root element (`html`) font size | **All font-sizes**, paddings, margins |
| `em` | Relative | Current element font size | Button padding that scales with text size |
| `%` | Relative | Parent container's dimensions | Fluid column widths |
| `vw` / `vh` | Relative | 1% of Viewport Width / Height | Hero banner heights, full-screen modals |
| `dvh` | Relative | Dynamic Viewport Height (mobile) | Mobile full-height screens avoiding URL bar overlap |

### ♿ The Accessibility Rule: `rem` for Font Sizes
Never set body copy in fixed `px` (e.g., `font-size: 16px`). If a visually impaired user increases their browser default font size to 24px, fixed `px` ignores their preference, whereas `rem` respects and scales with it!

```css
html {
    font-size: 100%; /* Respects browser user preference (typically 16px) */
}

body {
    font-size: 1rem;       /* 16px */
    line-height: 1.6;     /* Relative multiplier */
}

h1 {
    font-size: 2.25rem;   /* 36px */
}
```

---

## 🎨 2. Modern Color Models

CSS supports multiple formats for defining colors:

### A. Hexadecimal (HEX):
```css
color: #0284c7;        /* 6-digit hex: Red (02), Green (84), Blue (c7) */
color: #0284c780;      /* 8-digit hex with 50% alpha transparency */
```

### B. RGB / RGBA:
```css
color: rgb(2, 132, 199);
color: rgb(2 132 199 / 75%); /* Modern comma-free syntax with alpha */
```

### C. HSL (Hue, Saturation, Lightness):
The most intuitive color model for human designers:
```css
/* Hue: 0-360 deg on color wheel | Saturation: 0-100% | Lightness: 0-100% */
color: hsl(200, 98%, 39%);
color: hsl(200 98% 39% / 50%);
```
- Want a darker variant? Just decrease lightness (`hsl(200, 98%, 25%)`).
- Want a muted pastel variant? Just decrease saturation.

---

## 🔤 3. Typography Fundamentals

```css
body {
    /* System Font Stack Fallback */
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", sans-serif;
    font-weight: 400;         /* Normal: 400, Semi-Bold: 600, Bold: 700 */
    line-height: 1.6;         /* Golden ratio for reading comfort (1.5 - 1.7) */
    letter-spacing: -0.01em;  /* Slight tightening for modern sans-serif */
    color: #0f172a;           /* Dark slate text (never pure #000 on white) */
}
```

### Essential Typography Properties:
- **`font-family`:** List fonts in order of preference, always ending with a generic family (`sans-serif` or `serif`).
- **`line-height`:** Spacing between lines of text. Always use unitless multipliers (`1.6`), not fixed pixels.
- **`text-align`:** Alignment (`left`, `center`, `right`, `justify`).

---

## 🎯 Summary & Lesson Challenge

- Use `rem` for typography to respect user accessibility settings.
- Use `px` for thin borders and micro-offsets.
- Master HSL for color palette variation.
- Never use pure black `#000000` text on pure white backgrounds; use soft slates (`#0f172a` or `#1e293b`).

**Challenge:** Create a typography showcase page containing a headline, a subheading, and two paragraphs. Use `rem` for all text sizes, set a comfortable `line-height: 1.6`, and use HSL to define a base color and its lighter tint for borders.

---

**Next Lesson:**  
➡️ [Lesson 10: Display Modes & Element Visibility](./10-display-and-visibility.md) | ⬅️ [Lesson 08: The CSS Box Model](./08-the-box-model.md)
