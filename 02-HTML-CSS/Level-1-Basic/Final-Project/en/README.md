# Capstone Project: Developer Portfolio Card

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Project Overview
In this Level 1 Capstone Project, you will examine, inspect, and customize a production-ready **Developer Portfolio Card** built strictly with semantic HTML5 and defensive CSS.

### Key Architectural Elements Implemented:
- **Zero Frameworks:** 100% pure, vanilla HTML5 and CSS3.
- **Defensive Box Model:** Global `box-sizing: border-box` ensuring all paddings and borders never disrupt layout math.
- **Fluid & Accessible Typography:** Root `rem` units honoring user browser font scale preferences.
- **Reverse Tabnabbing Mitigation:** External profile links secured with `target="_blank" rel="noopener noreferrer"`.
- **Layout Stability:** Explicit `width` and `height` attributes on the avatar image to eliminate Cumulative Layout Shift (CLS).

---

## 📂 Project Structure
```plaintext
Final-Project/
├── index.html       # Semantic HTML5 markup
├── styles.css       # Clean, modern CSS with Box Model & token design
├── en/README.md     # English project guide
└── ar/README.md     # دليل المشروع التتويجي بالعربية
```

---

## 🚀 How to Run & Inspect Locally

1. Open `index.html` directly in your browser:
   - On Windows: Double-click `index.html` or run:
     ```powershell
     Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-1-Basic\Final-Project\index.html"
     ```
   - Or start a local Python HTTP server:
     ```bash
     python -m http.server 8080
     ```
2. Open **Chrome DevTools** (`F12` or `Ctrl+Shift+I`):
   - Navigate to the **Elements** panel.
   - Hover over `.profile-card` and switch to the **Computed** tab.
   - Observe how `padding`, `border`, and `margin` are organized cleanly within the Box Model diagram.

---

## 🏆 Customization Challenge for You

1. Change the name, title, bio, and competency tags to reflect your personal profile.
2. Customize the header gradient background using HSL colors.
3. Add a third button in the footer for your personal blog or portfolio link, making sure to include `rel="noopener noreferrer"`.

---

**Next Milestone:**  
👉 **[Proceed to Level 2: Modern Layouts, Flexbox, Grid & Forms](../../Level-2-Foundation/README.md)**
