# 10 - CSS Transitions, Transforms & Hardware Acceleration

> 🌐 **Language / اللغة:** [English 🇬🇧](./10-css-transitions-and-transforms.md) | [العربية 🇸🇦](../ar/10-css-transitions-and-transforms.md)

---

## 📌 1. The Power of `transform`

The `transform` property modifies the coordinate space of an element, altering its position, size, orientation, or skew without disrupting neighboring elements in the document flow:

```css
/* Translate (Move horizontally and vertically) */
transform: translate(20px, -10px);

/* Scale (Resize relative to center) */
transform: scale(1.05);

/* Rotate */
transform: rotate(45deg);

/* Combining multiple transformations in order */
transform: translateY(-4px) scale(1.02);
```

---

## ⏱️ 2. CSS Transitions

The `transition` property enables smooth, gradual interpolation between CSS property state changes (such as on `:hover`, `:focus`, or JavaScript class toggles):

```css
.card {
    /* property | duration | timing-function | delay */
    transition: transform 0.25s cubic-bezier(0.4, 0, 0.2, 1), 
                box-shadow 0.25s ease;
}

.card:hover {
    transform: translateY(-6px);
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.15);
}
```

### Transition Sub-Properties:
- **`transition-property`:** Name of properties to animate (never use `all` in production—it degrades performance!).
- **`transition-duration`:** Time the transition takes (e.g., `0.2s`, `300ms`).
- **`transition-timing-function`:** Acceleration curve (`ease`, `linear`, `ease-in-out`, or custom `cubic-bezier`).
- **`transition-delay`:** Time to wait before animation starts.

---

## ⚡ 3. The Performance Architecture: CPU Reflow vs GPU Compositing

Understanding the browser rendering pipeline separates amateur designers from senior web engineers:

```
 Property Change         Triggers Layout (Reflow)?     Triggers Repaint?     GPU Composited?
 ────────────────────────────────────────────────────────────────────────────────────────
 width, height, left, top       YES (CPU Heavy)              YES                  NO
 background-color, color         NO                          YES                  NO
 transform, opacity              NO                           NO             YES (60-120 FPS!)
```

### The Rendering Pipeline Stages:
1. **Layout (Reflow):** The browser recalculates the geometry, dimensions, and positions of every element on the page. Animating properties like `top`, `left`, `width`, or `margin` forces constant CPU recalculations, causing visible lag, battery drain, and dropped frames (jank).
2. **Paint (Repaint):** Filling in pixels (colors, borders, shadows).
3. **Composite:** Sending bitmaps directly to the **GPU (Graphics Processing Unit)**.

> 🚀 **The Performance Rule:** **Only animate `transform` and `opacity`.** These properties bypass Layout and Paint completely, executing directly on the GPU for silky-smooth 60fps to 120fps animations!

### The `will-change` Hint:
For complex, frequently animated components, hint the browser's compositor ahead of time:
```css
.modal-overlay {
    will-change: transform, opacity;
}
```

---

## 🎯 Summary & Lesson Challenge

- Use `transform` to translate, scale, and rotate without affecting layout flow.
- Always animate `transform` and `opacity` instead of `width`, `height`, or `top`/`left`.
- Use specific properties in `transition` instead of `transition: all`.

**Challenge:** Create an interactive interactive button that scales slightly (`transform: scale(1.04)`) and lifts up (`transform: translateY(-2px)`) with a smooth `box-shadow` on hover, running exclusively on the GPU compositor.

---

**Next Milestone:**  
➡️ [Level 2 Practical Application Labs](../Practical-Applications/en/README.md) | 🏆 [Level 2 Capstone Project](../Final-Project/en/README.md) | 🏠 [Level 2 Index](../../README.md)
