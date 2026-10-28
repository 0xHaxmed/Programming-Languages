# 06 - CSS Grid Architecture (Two-Dimensional Blueprints)

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-css-grid-architecture.md) | [العربية 🇸🇦](../ar/06-css-grid-architecture.md)

---

## 📌 1. The Two-Dimensional Layout Engine

While Flexbox is designed for 1D linear flows (row OR column), **CSS Grid** is the first true **Two-Dimensional Layout Engine** in CSS. It allows you to orchestrate elements across **rows AND columns simultaneously**, creating structured blueprints for whole pages and complex dashboards.

```
       Col 1             Col 2             Col 3
  ┌────────────────┬────────────────┬────────────────┐
  │                │                │                │  Row 1
  ├────────────────┼────────────────┼────────────────┤
  │                │                │                │  Row 2
  ├────────────────┼────────────────┼────────────────┤
  │                │                │                │  Row 3
  └────────────────┴────────────────┴────────────────┘
```

---

## 🏗️ 2. Activating the Grid Container

Declaring `display: grid` on an element activates grid formatting context for its direct children:

```html
<div class="dashboard-grid">
    <div class="card">Analytics</div>
    <div class="card">Logs</div>
    <div class="card">Alerts</div>
    <div class="card">Reports</div>
</div>
```

```css
.dashboard-grid {
    display: grid;
    grid-template-columns: 200px 1fr 1fr; /* 3 Columns */
    grid-template-rows: auto;
    gap: 1.5rem;
}
```

---

## 📏 3. The Fractional Unit (`fr`) & `repeat()`

The **Fractional Unit (`fr`)** represents a fraction of the available free space in the grid container:

```css
/* 3 equal columns: each gets 1/3 of the container width */
grid-template-columns: 1fr 1fr 1fr;

/* Repeating cleanly with repeat(count, track-size) */
grid-template-columns: repeat(3, 1fr);

/* Asymmetric dashboard: 260px fixed sidebar, rest split 2:1 */
grid-template-columns: 260px 2fr 1fr;
```

---

## 🛡️ 4. Fluid Resiliency with `minmax()`

To prevent tracks from shrinking beyond readability:
```css
/* Columns expand to fill space, but will NEVER compress below 220px */
grid-template-columns: repeat(3, minmax(220px, 1fr));
```

---

## 🔢 5. Grid Lines, Tracks & Spanning Cells

Grid lines are numbered starting from `1` (or negative `-1` from the opposite edge):

```
 Line 1           Line 2           Line 3           Line 4
   │                │                │                │
   ▼                ▼                ▼                ▼
   ┌────────────────┬────────────────┬────────────────┐
 1 │                │                │                │
   ├────────────────┼────────────────┼────────────────┤
 2 │                                 │                │
   │      (Spanning 2 columns)       │                │
   ├────────────────┼────────────────┼────────────────┤
 3 │                │                │                │
   └────────────────┴────────────────┴────────────────┘ 4
```

### Positioning Items Across Tracks:
```css
/* Span across columns */
.hero-card {
    grid-column: 1 / 3;     /* Starts at line 1, ends before line 3 */
    /* Or equivalent shorthand: */
    grid-column: span 2;
}

/* Span full width across all columns */
.full-width-banner {
    grid-column: 1 / -1;    /* Spans from line 1 all the way to the last line! */
}
```

---

## ⚖️ 6. Flexbox vs Grid: The Ultimate Rule of Thumb

| Dimension | CSS Grid | Flexbox |
| :--- | :--- | :--- |
| **Dimensions** | **2D** (Rows AND Columns simultaneously) | **1D** (Row OR Column) |
| **Approach** | **Layout-First** (define structure, place items inside) | **Content-First** (items dictate spacing) |
| **Ideal For** | Page skeletons, dashboards, photo galleries, forms | Navigation bars, button groups, icon alignments |

---

## 🎯 Summary & Lesson Challenge

- Grid manages rows and columns simultaneously.
- Use `fr` units to allocate fractional shares of free space.
- Use `minmax()` to guarantee readable minimum boundaries.
- Use `grid-column: span N` or `1 / -1` to span items across tracks.

**Challenge:** Create a 12-column responsive blueprint (`grid-template-columns: repeat(12, 1fr)`). Place a header spanning `1 / -1`, a sidebar spanning `span 3`, and a main section spanning `span 9`.

---

**Next Lesson:**  
➡️ [Lesson 07: Grid Named Areas & Responsive Auto-Fit](./07-css-grid-areas-and-alignment.md) | ⬅️ [Lesson 05: Advanced Flexbox](./05-flexbox-advanced-alignment.md)
