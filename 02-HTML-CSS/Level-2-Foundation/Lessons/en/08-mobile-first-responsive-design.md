# 08 - Mobile-First Responsive Design & Media Queries

> 🌐 **Language / اللغة:** [English 🇬🇧](./08-mobile-first-responsive-design.md) | [العربية 🇸🇦](../ar/08-mobile-first-responsive-design.md)

---

## 📌 1. The Mobile-First Engineering Paradigm

In traditional legacy development ("Desktop-First"), engineers built massive desktop layouts and then hacked them down with `max-width` media queries to fit smaller mobile screens.

**Mobile-First Design** reverses this logic through **Progressive Enhancement**:
1. You write base CSS for small mobile screens first (simplest single-column flow, minimal overhead).
2. You layer on multi-column layouts and visual enhancements progressively using `min-width` media queries as screen real estate expands!

```
 Mobile Base (< 640px)          Tablet (>= 768px)            Desktop (>= 1024px)
 ┌──────────────────┐       ┌──────────────────────┐     ┌────────────────────────────┐
 │  [ Header ]      │       │ [Header]             │     │ [Header]                   │
 │  [ Nav ]         │       ├──────────┬───────────┤     ├───────┬────────────┬───────┤
 │  [ Main Content] │  ──►  │ [Main]   │ [Sidebar] │ ──► │[Nav]  │ [Main]     │[Aside]│
 │  [ Sidebar ]     │       ├──────────┴───────────┤     ├───────┴────────────┴───────┤
 │  [ Footer ]      │       │ [Footer]             │     │ [Footer]                   │
 └──────────────────┘       └──────────────────────┘     └────────────────────────────┘
```

---

## 📡 2. Media Query Syntax & Standard Breakpoints

The `@media` at-rule conditionally applies CSS rules when device conditions match:

```css
/* ==========================================================================
   1. Base Styles: Mobile Phones (Default, No Query Needed!)
   ========================================================================== */
.container {
    width: 100%;
    padding: 1rem;
}

.site-nav {
    display: flex;
    flex-direction: column; /* Stacked links on phone */
}

/* ==========================================================================
   2. Tablets & Small Laptops (min-width: 48em / 768px)
   ========================================================================== */
@media (min-width: 48em) {
    .site-nav {
        flex-direction: row; /* Horizontal links on tablet */
        justify-content: space-between;
    }
}

/* ==========================================================================
   3. Desktops & Workstations (min-width: 64em / 1024px)
   ========================================================================== */
@media (min-width: 64em) {
    .dashboard-layout {
        display: grid;
        grid-template-columns: 260px 1fr;
    }
}
```

### Standard Modern Breakpoints:
- **`sm` (640px / 40em):** Large smartphones and small handhelds.
- **`md` (768px / 48em):** Tablets in portrait mode.
- **`lg` (1024px / 64em):** Tablets in landscape, laptops, and smaller desktops.
- **`xl` (1280px / 80em):** Standard desktop monitors.
- **`2xl` (1536px / 96em):** Wide ultrawide and 4K displays.

---

## 📏 3. Fluid Typography with `clamp()`

Instead of writing media queries for every heading size, modern CSS provides `clamp(min, preferred, max)`:

```css
h1 {
    /* Minimum: 2rem | Ideal: scales with 5% of viewport width | Maximum: 3.75rem */
    font-size: clamp(2rem, 5vw + 1rem, 3.75rem);
}
```
The text smoothly expands as the viewport widens without any abrupt breakpoint jumps!

---

## ♿ 4. Accessibility & User Preference Media Queries

Responsive design goes far beyond screen width—it honors user physiological preferences:

### A. Dark Mode (`prefers-color-scheme`):
```css
@media (prefers-color-scheme: dark) {
    body {
        background-color: #0f172a;
        color: #f8fafc;
    }
}
```

### B. Motion Sensitivity (`prefers-reduced-motion`):
Vestibular disorder sufferers experience dizziness from animated transitions. Honor their system preferences:
```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
    }
}
```

---

## 🎯 Summary & Lesson Challenge

- Write base styles for mobile, then progressively layer enhancements with `min-width`.
- Use relative `em`/`rem` units in media queries for accessibility.
- Implement fluid typography using `clamp()`.
- Always support `prefers-reduced-motion` for vestibular accessibility.

**Challenge:** Build a card layout that stacks elements vertically on mobile, switches to 2 columns on tablets (`min-width: 48em`), and 3 columns on desktop (`min-width: 64em`), complete with dark mode detection.

---

**Next Lesson:**  
➡️ [Lesson 09: CSS Positioning Deep Dive (Relative, Absolute, Fixed, Sticky)](./09-css-positioning-deep-dive.md) | ⬅️ [Lesson 07: Grid Areas & Auto-Fit](./07-css-grid-areas-and-alignment.md)
