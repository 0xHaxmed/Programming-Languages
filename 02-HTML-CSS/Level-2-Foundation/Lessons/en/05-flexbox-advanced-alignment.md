# 05 - Advanced Flexbox: Wrapping, Sizing & Alignment

> 🌐 **Language / اللغة:** [English 🇬🇧](./05-flexbox-advanced-alignment.md) | [العربية 🇸🇦](../ar/05-flexbox-advanced-alignment.md)

---

## 📌 1. Multi-line Flex Flows: `flex-wrap`

By default, a flex container forces all flex items onto a **single line** (`flex-wrap: nowrap`), even if they overflow the screen width.

To allow items to naturally wrap onto new lines as screen space shrinks:

```css
.card-grid {
    display: flex;
    flex-wrap: wrap; /* Allows items to break into new rows */
    gap: 1.5rem;
}
```

- `nowrap` (default): All items squeezed onto one line.
- `wrap`: Items wrap into new rows from top to bottom.
- `wrap-reverse`: Items wrap into new rows from bottom to top.

---

## 📐 2. The Item Sizing Trifecta: `grow`, `shrink` & `basis`

Flex items calculate their final rendered size through three interdependent properties:

```
 Total Available Space
 ┌────────────────────────────────────────────────────────────┐
 │  ┌───────────────┐ ┌───────────────┐ ┌──────────────────┐  │
 │  │ flex-grow: 1  │ │ flex-grow: 1  │ │  flex-grow: 2    │  │
 │  └───────────────┘ └───────────────┘ └──────────────────┘  │
 └────────────────────────────────────────────────────────────┘
```

### A. `flex-basis` (The Initial Base Size):
Defines the default size of an item before remaining space is distributed. It acts like `width` in row mode or `height` in column mode.
```css
.sidebar {
    flex-basis: 280px;
}
```

### B. `flex-grow` (Absorbing Positive Free Space):
Specifies how much remaining available space in the container an item should absorb relative to its siblings:
- `flex-grow: 0` (default): Item stays at its `flex-basis` size; does not expand.
- `flex-grow: 1`: Item expands to claim available free space.
- An item with `flex-grow: 2` will claim **twice as much** free space as siblings with `flex-grow: 1`.

### C. `flex-shrink` (Shedding Space When Cramped):
Specifies how much an item will shrink when there is insufficient space in the container:
- `flex-shrink: 1` (default): Item shrinks proportionally to prevent container overflow.
- `flex-shrink: 0`: **Prevents shrinking entirely!** Crucial for avatar images, icons, or fixed-width badges to prevent distortion.

### 🌟 The Recommended Shorthand: `flex`
Always prefer the three-value shorthand `flex: <grow> <shrink> <basis>`:
```css
.main-content {
    flex: 1 1 0%;    /* Grow freely, shrink when needed, start from 0 */
}

.fixed-sidebar {
    flex: 0 0 260px; /* Don't grow, don't shrink, stay locked at 260px */
}
```

---

## 🎯 3. Overriding Alignment with `align-self`

An individual flex item can override the container's global `align-items` rule:

```css
.container {
    display: flex;
    align-items: center; /* All items centered vertically */
}

.special-badge {
    align-self: flex-start; /* This single badge pins to the top! */
}
```

---

## ⚠️ 4. Visual Reordering & The Accessibility Trap

Flexbox allows visual reordering using the `order` property:
```css
.first-in-dom {
    order: 2; /* Visually pushed behind item with order: 1 */
}
```

> ♿ **Accessibility Warning:** Changing `order` **only** affects visual rendering. The underlying DOM tree and keyboard `Tab` navigation order remain unchanged! This creates extreme confusion for screen reader and keyboard users whose focus leaps unpredictably around the screen.

---

## 🎯 Summary & Lesson Challenge

- Use `flex-wrap: wrap` for fluid multi-line item cards.
- Master the `flex: <grow> <shrink> <basis>` shorthand.
- Set `flex-shrink: 0` on icons and avatars to prevent squishing.
- Never use `order` to fix flawed DOM hierarchies.

**Challenge:** Create a responsive sidebar-and-content layout where the sidebar has a fixed width (`flex: 0 0 240px`) and the main area expands to consume all remaining space (`flex: 1 1 auto`), wrapping cleanly on small screens.

---

**Next Lesson:**  
➡️ [Lesson 06: CSS Grid Architecture (Two-Dimensional Blueprints)](./06-css-grid-architecture.md) | ⬅️ [Lesson 04: Flexbox Fundamentals](./04-flexbox-fundamentals.md)
