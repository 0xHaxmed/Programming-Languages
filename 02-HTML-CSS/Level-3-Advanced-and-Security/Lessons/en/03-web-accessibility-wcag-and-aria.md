# 03 - Web Accessibility (WCAG 2.1 AA & ARIA Standards)

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-web-accessibility-wcag-and-aria.md) | [العربية 🇸🇦](../ar/03-web-accessibility-wcag-and-aria.md)

---

## 📌 1. The Legal & Ethical Foundation of a11y

**Web Accessibility (a11y)** ensures that web applications are fully usable by individuals with auditory, cognitive, neurological, physical, speech, and visual impairments. Under global standards like **WCAG (Web Content Accessibility Guidelines) 2.1 Level AA**, accessibility is both an ethical responsibility and a strict legal requirement.

### The 4 POUR Principles:
1. **Perceivable:** Information must be presentable to users in ways they can perceive (text alternatives for media, sufficient color contrast).
2. **Operable:** User interface components must be operable via keyboard without trap.
3. **Understandable:** Content and UI operation must be predictable and intuitive.
4. **Robust:** Content must be compatible with a wide variety of assistive technologies (screen readers).

---

## 🎨 2. Color Contrast Ratios (WCAG AA Compliance)

Text must maintain sufficient contrast against its background to remain readable for users with low vision or color blindness:

```
 Minimum Required Contrast Ratios (WCAG Level AA)
 ┌───────────────────────────┬────────────────────────────────┐
 │ Text Classification       │ Minimum Contrast Ratio         │
 ├───────────────────────────┼────────────────────────────────┤
 │ Normal Body Text (< 18pt) │ 4.5 : 1                        │
 │ Large Text (>= 18pt / 14pt│ 3.0 : 1                        │
 │ bold)                     │                                │
 │ UI Components & Borders   │ 3.0 : 1                        │
 └───────────────────────────┴────────────────────────────────┘
```

> 🛠️ **Audit Tool:** Always inspect colors using Chrome DevTools (Elements panel -> inspect color picker to check contrast badges).

---

## ⌨️ 3. Keyboard Navigation & Focus Indicators

Every interactive element (links, buttons, form inputs) must be reachable and actionable using the **keyboard alone** (via `Tab`, `Shift+Tab`, `Enter`, and `Space`).

### The `:focus-visible` Rule:
Never write `outline: none` without providing an immediate `:focus-visible` replacement:
```css
/* ACCESSIBLE FOCUS RING */
button:focus-visible, 
a:focus-visible, 
input:focus-visible {
    outline: 2px solid #0284c7;
    outline-offset: 3px;
}
```

### The `tabindex` Values:
- `tabindex="0"`: Inserts a non-interactive element into normal keyboard tab flow.
- `tabindex="-1"`: Makes an element programmatically focusable via JavaScript, but removes it from natural Tab key flow.
- ❌ **Anti-pattern:** `tabindex="1+"` (positive numbers). Never use positive tabindex—it disrupts the natural DOM reading sequence.

---

## 🏷️ 4. WAI-ARIA: Accessible Rich Internet Applications

> 📜 **The First Rule of ARIA:** **"No ARIA is better than Bad ARIA."** Always prefer native semantic HTML elements (`<button>`, `<dialog>`, `<nav>`) before applying custom ARIA attributes.

### Key ARIA Attributes:

### A. `aria-label` (For Icon Buttons):
When a button contains only a graphic icon and no text, screen readers cannot vocalize its intent:
```html
<!-- SCREEN READER ANNOUNCES: "Close Dialog, button" -->
<button type="button" class="btn-close" aria-label="Close Dialog">
    <span aria-hidden="true">&times;</span>
</button>
```

### B. `aria-hidden="true"` (Hiding Decorative Content):
Prevents screen readers from announcing decorative symbols, emojis, or SVGs:
```html
<button>
    <span aria-hidden="true">🔒</span>
    <span>Authenticate with Passkey</span>
</button>
```

### C. `aria-expanded` (Accordion & Drawer States):
Communicates dynamic collapse/expansion to assistive tools:
```html
<button aria-expanded="false" aria-controls="audit-details">
    View Audit Logs
</button>
<div id="audit-details" hidden>...</div>
```

### D. `aria-live="polite"` (Dynamic Notifications):
Announces dynamic changes (like alerts, cart updates, or search results) without interrupting ongoing speech:
```html
<div class="status-msg" aria-live="polite">
    Security scan completed: 0 threats detected.
</div>
```

---

## 🎯 Summary & Lesson Challenge

- Honor WCAG 2.1 AA standards: minimum 4.5:1 text contrast.
- Preserve visible focus indicators with `:focus-visible`.
- Use `aria-label` on icon-only buttons.
- Hide decorative graphics with `aria-hidden="true"`.

**Challenge:** Build an accessible modal dialog trigger containing an icon button labeled via `aria-label`, with an accessible `:focus-visible` outline and an `aria-live` announcement region.

---

**Next Lesson:**  
➡️ [Lesson 04: Iframe Sandboxing & Clickjacking Defenses](./04-iframe-sandboxing-and-clickjacking.md) | ⬅️ [Lesson 02: BEM Architecture](./02-bem-architecture-and-maintainable-css.md)
