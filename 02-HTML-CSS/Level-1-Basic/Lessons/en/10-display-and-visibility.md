# 10 - Display Modes & Element Visibility

> 🌐 **Language / اللغة:** [English 🇬🇧](./10-display-and-visibility.md) | [العربية 🇸🇦](../ar/10-display-and-visibility.md)

---

## 📌 1. The `display` Property

The `display` property dictates how an element behaves in the browser's normal document flow and how its children are arranged.

```
 Block Element (takes full available width, breaks onto new line)
 ┌────────────────────────────────────────────────────────────┐
 │ <div>, <p>, <section>, <h1>                                │
 └────────────────────────────────────────────────────────────┘

 Inline Elements (flow horizontally side-by-side)
 ┌──────────┐ ┌──────────┐ ┌──────────┐
 │  <span>  │ │   <a>    │ │ <strong> │
 └──────────┘ └──────────┘ └──────────┘
```

---

## 🔍 2. The 3 Classic Display Types

| Property Value | Starts on New Line? | Honors `width` / `height`? | Honors vertical `margin` / `padding`? | Typical Tags |
| :--- | :---: | :---: | :---: | :--- |
| `block` | **YES** | **YES** | **YES** | `div`, `p`, `h1`-`h6`, `ul`, `header` |
| `inline` | **NO** | **NO** | **NO** (horizontal only) | `span`, `a`, `strong`, `em`, `code` |
| `inline-block` | **NO** | **YES** | **YES** | Buttons, badge tags, icon wrappers |

### Transforming Elements:
You can convert any HTML tag's display behavior via CSS:
```css
/* Make an anchor behave like a button with custom width and padding */
a.cta-button {
    display: inline-block;
    padding: 0.75rem 1.5rem;
    width: 200px;
    text-align: center;
}
```

---

## 👻 3. Hiding Elements: `display: none` vs `visibility: hidden` vs `opacity: 0`

Choosing how to hide an element has massive layout, accessibility, and security implications:

```
  Normal Element            display: none           visibility: hidden        opacity: 0
┌────────────────┐                                ┌────────────────┐       ┌────────────────┐
│  [ Visible ]   │        (Node removed from      │  [ Invisible ] │       │ [ Transparent] │
│                │          render tree,          │                │       │ (STILL ACCEPTS │
│                │          0px occupied)         │ (Space Kept!)  │       │  CLICKS!)      │
└────────────────┘                                └────────────────┘       └────────────────┘
```

| Technique | Takes Layout Space? | Accessible to Screen Readers? | Clickable / Interactive? | Security / Use Case |
| :--- | :---: | :---: | :---: | :--- |
| `display: none;` | **No** | **No** (ignored completely) | **No** | Accordions, tabs, hidden modals |
| `visibility: hidden;` | **Yes** (empty gap stays) | **No** | **No** | Preserving layout slot |
| `opacity: 0;` | **Yes** | **Yes** (or partially) | **YES! (DANGEROUS)** | Animations & **Clickjacking** vectors! |

> 🛡️ **Security Note (Clickjacking Attack Vector):** In Clickjacking attacks, a malicious page sets an invisible `iframe` over an innocent-looking button with `opacity: 0`. The victim thinks they are clicking a game or video, but they are actually clicking "Authorize Payment" inside the transparent iframe! We will master defenses against this in Level 3.

---

## 🚀 4. The Modern Layout Engines: Flexbox & Grid

While `block` and `inline` manage individual flow, modern multi-column web applications are built with:
- **`display: flex;`** — One-dimensional row/column alignment.
- **`display: grid;`** — Two-dimensional matrix layout blueprints.

These two modern powerhouses are the entire focus of **Level 2 - Foundation**!

---

## 🎯 Summary & Lesson Challenge

- `block` fills the row; `inline` flows with text; `inline-block` allows width/height inside text flow.
- Understand the strict differences between `display: none`, `visibility: hidden`, and `opacity: 0`.
- Recognize the danger of transparent interactive elements (`opacity: 0`).

**Challenge:** Create a navigation bar using an unordered list `<ul>` where list items `<li>` are switched from `display: block` to `display: inline-block` to sit horizontally side-by-side with padding and hover effects.

---

**Next Milestone:**  
➡️ [Level 1 Practical Exercises](../Exercises/en/README.md) | 🏆 [Level 1 Capstone Project](../Final-Project/en/README.md) | 🏠 [Level 1 Index](../../README.md)
