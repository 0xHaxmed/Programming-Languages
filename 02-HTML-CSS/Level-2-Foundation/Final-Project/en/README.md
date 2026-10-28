# Capstone Project: Responsive CloudShield SaaS Portal

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Project Overview
The Level 2 Capstone Project showcases a full-scale, production-grade **CloudShield SaaS Security Portal** crafted with semantic HTML5, modern CSS Grid, Flexbox, and fluid typography.

### Architectural Features Implemented:
1. **Mobile-First Responsive Pipeline:** The navigation bar, hero CTA buttons, and footer stack vertically on smartphones and progressively transform into multi-column layouts on tablets and desktops.
2. **Zero-Media-Query Grid:** The Features section uses `grid-template-columns: repeat(auto-fit, minmax(250px, 1fr))` to dynamically adapt across 4, 3, 2, and 1 column without writing media queries!
3. **Fluid Typography:** The hero headline scales smoothly with the viewport using `font-size: clamp(2rem, 4vw + 1rem, 3.25rem)`.
4. **Accessible Form Architecture:** Complete with explicit `<label for="...">` associations, mobile-optimized inputs (`type="email"`), and non-intrusive error feedback via `:user-invalid`.
5. **GPU Acceleration:** All hover transitions animate `transform: translateY()` on the compositor thread for smooth 60fps performance.

---

## 📂 Project Structure
```plaintext
Final-Project/
├── index.html       # Semantic HTML5 layout & accessible form
├── styles.css       # Mobile-first CSS with Grid, Flexbox & CSS variables
├── en/README.md     # English project documentation
└── ar/README.md     # توثيق المشروع بالعربية
```

---

## 🚀 How to Run & Inspect

1. Open `index.html` in your browser:
   ```powershell
   Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-2-Foundation\Final-Project\index.html"
   ```
2. Open **Chrome DevTools** (`F12`), toggle the **Device Toolbar** (`Ctrl+Shift+M`), and test the layout across iPhone, iPad, and 4K display dimensions.
3. Observe how the form fields highlight red when invalid using `:user-invalid` without interfering during normal typing.

---

**Next Milestone:**  
👉 **[Proceed to Level 3: Advanced Architecture, Accessibility & Security Flaws](../../Level-3-Advanced-and-Security/README.md)**
