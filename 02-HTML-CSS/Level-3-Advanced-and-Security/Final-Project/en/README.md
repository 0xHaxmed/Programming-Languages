# Capstone Project: Hardened Client Security Dashboard

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Project Overview
The Level 3 Capstone Project delivers an enterprise-grade **CyberCommand Security Dashboard** engineered to demonstrate the union of **BEM Architecture**, **dynamic CSS custom property theming**, **WCAG 2.1 AA accessibility**, and **frontend defensive hardening**.

### Architectural Standards Implemented:
1. **Strict BEM Naming Convention:** Components are cleanly segmented into blocks (`.dashboard`, `.metric-card`, `.panel`), elements (`.metric-card__value`, `.panel__title`), and modifiers (`.btn--outline`, `.badge--success`), maintaining flat `(0, 0, 1, 0)` specificity throughout.
2. **Dynamic Theming System:** Powered by CSS custom properties under `[data-theme="dark"]` and `[data-theme="light"]`, allowing instant runtime theme toggling without flickering or component rewrites.
3. **Accessibility (WCAG 2.1 AA):** Screen-reader accessible landmarks (`<header>`, `<aside>`, `<main>`, `<footer>`), `aria-label` on the theme button, `aria-hidden="true"` on decorative icons, and high-contrast visible focus outlines (`:focus-visible`).
4. **Defensive Hardening:** Enforced Content Security Policy meta directives, explicit image dimensions, and security-verified checklist status.

---

## 📂 Project Structure
```plaintext
Final-Project/
├── index.html       # Hardened dashboard markup & CSP baseline
├── styles.css       # BEM architecture & custom properties design tokens
├── en/README.md     # English project documentation
└── ar/README.md     # دليل وكود المشروع بالعربية
```

---

## 🚀 How to Run & Inspect

1. Open `index.html` in your browser:
   ```powershell
   Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Final-Project\index.html"
   ```
2. Click the **"Switch Theme"** button in the header to toggle between Dark Mode and Light Mode.
3. Press `Tab` on your keyboard to navigate through controls—observe the accessible `:focus-visible` focus ring!
4. Inspect the CSS file in Chrome DevTools to verify that every single selector adheres to flat BEM specificity.

---

**Next Milestone:**  
🎯 **Congratulations! You have mastered the complete HTML5 & CSS3 Engineering Track.**  
👉 Return to the [Master Roadmap: 02-HTML-CSS Index](../../README.md) or explore [01-JavaScript-Core](../../../01-JavaScript-Core/README.md).
