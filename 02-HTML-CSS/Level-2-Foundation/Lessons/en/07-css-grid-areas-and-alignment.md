# 07 - CSS Grid Named Areas & Responsive Auto-Fit

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-css-grid-areas-and-alignment.md) | [العربية 🇸🇦](../ar/07-css-grid-areas-and-alignment.md)

---

## 📌 1. Visual Layout Mapping with `grid-template-areas`

One of the most expressive capabilities of CSS Grid is **Named Grid Areas**. Instead of counting line numbers, you visually sketch your layout in ASCII-like strings:

```html
<div class="site-layout">
    <header class="header">Header</header>
    <nav class="nav">Navigation</nav>
    <main class="main">Main Content</main>
    <aside class="sidebar">Sidebar</aside>
    <footer class="footer">Footer</footer>
</div>
```

```css
.site-layout {
    display: grid;
    grid-template-columns: 240px 1fr 200px;
    grid-template-rows: auto 1fr auto;
    grid-template-areas:
        "header  header  header"
        "nav     main    sidebar"
        "footer  footer  footer";
    min-height: 100vh;
    gap: 1rem;
}

/* Assign elements to their named blueprint zones */
.header  { grid-area: header; }
.nav     { grid-area: nav; }
.main    { grid-area: main; }
.sidebar { grid-area: sidebar; }
.footer  { grid-area: footer; }
```

### The Period (`.`) Placeholder:
To leave a grid cell empty, use a period:
```css
grid-template-areas:
    "header header"
    "main   ."
    "footer footer";
```

---

## 🚀 2. Responsive Grids with ZERO Media Queries (`auto-fit`)

This single line of CSS is arguably the most powerful layout rule in modern web engineering:

```css
.card-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;
}
```

### How It Works:
1. As the viewport widens, the browser fits as many `280px` columns as mathematically possible.
2. If there is extra leftover width, the `1fr` fraction automatically stretches the cards to perfectly fill the empty space.
3. If the viewport narrows (e.g., on a mobile phone), cards wrap into fewer columns, collapsing gracefully down to a single full-width column on mobile screens—**without writing a single `@media` query!**

---

## 🔍 3. `auto-fit` vs `auto-fill`: What's the Difference?

```
 Container Width: 1000px | Cards: 2 (min 200px each)

 auto-fill (Creates ghost empty tracks for potential cards)
 ┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
 │   Card 1     │    Card 2    │ (Empty Slot) │ (Empty Slot) │ (Empty Slot) │
 └──────────────┴──────────────┴──────────────┴──────────────┴──────────────┘

 auto-fit (Collapses empty tracks to 0px, stretching existing cards!)
 ┌─────────────────────────────┬─────────────────────────────┐
 │           Card 1            │           Card 2            │
 └─────────────────────────────┴─────────────────────────────┘
```

- **`auto-fill`:** Preserves empty track slots even if there are no items to populate them.
- **`auto-fit` (Recommended):** Collapses empty track slots to `0px` and stretches existing items to fill the entire container width.

---

## 🎯 4. Aligning Grid Tracks & Cells

| Property | Axis | Target |
| :--- | :--- | :--- |
| `justify-items` | Inline (Horizontal) | Aligns content inside each individual grid cell (`start`, `center`, `end`, `stretch`). |
| `align-items` | Block (Vertical) | Aligns content vertically inside each individual grid cell. |
| `justify-content` | Inline (Horizontal) | Aligns the entire grid matrix within the parent container. |
| `align-content` | Block (Vertical) | Aligns the entire grid matrix vertically within the container. |

---

## 🎯 Summary & Lesson Challenge

- `grid-template-areas` turns CSS into a visual layout blueprint.
- Master `repeat(auto-fit, minmax(280px, 1fr))` for automated responsive card grids.
- Understand the distinction between `auto-fit` (stretch) and `auto-fill` (preserve slots).

**Challenge:** Create a responsive product catalog grid that displays 4 columns on large monitors, 2 on tablets, and 1 on phones using `repeat(auto-fit, minmax(250px, 1fr))` with zero media queries.

---

**Next Lesson:**  
➡️ [Lesson 08: Mobile-First Responsive Design & Media Queries](./08-mobile-first-responsive-design.md) | ⬅️ [Lesson 06: CSS Grid Architecture](./06-css-grid-architecture.md)
