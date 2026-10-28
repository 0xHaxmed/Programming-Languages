# Level 2 Practical Application Labs

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Educational Goal
Apply Level 2 modern layout engines, form validation, and responsive techniques through 3 production-grade frontend architectural labs.

---

## 🛠️ Lab 1: Responsive Navigation Bar with Flexbox & Mobile Stacking

### Objective:
Build a sleek navigation bar that displays horizontally on desktop/tablets and collapses into an accessible, clean vertical stack on mobile devices.

### Specifications:
- Brand logo on the left; links and a "Sign In" button on the right.
- Mobile (< 768px): Links stack vertically, occupying full width.
- Desktop (>= 768px): Uses `display: flex; justify-content: space-between; align-items: center;` with a `gap: 1.5rem`.
- GPU-accelerated hover effects on navigation items (`transform: translateY(-2px)`).

---

## 🛠️ Lab 2: Zero-Media-Query Fluid Grid with `auto-fit`

### Objective:
Construct a responsive metrics dashboard grid that smoothly reflows across 4 columns, 2 columns, and 1 column without writing a single `@media` rule!

### Specifications:
- Grid container: `display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 1.5rem;`.
- 4 Metric cards containing an icon, title, statistic, and change percentage.
- Cards must use `box-sizing: border-box`, smooth hover transitions, and subtle borders.

---

## 🛠️ Lab 3: Accessible & Hardened Client Registration Form

### Objective:
Implement a client onboarding form enforcing native HTML5 constraint validation, mobile keyboard optimizations, and clear validation feedback.

### Specifications:
- Full Name: `required`, `autocomplete="name"`.
- Corporate Email: `type="email"`, `required`, `autocomplete="email"`.
- Password: `type="password"`, `minlength="8"`, `pattern="^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d@$!%*#?&]{8,}$"`, `autocomplete="new-password"`.
- Country: `<select>` dropdown with disabled placeholder option.
- Terms of Service: `<input type="checkbox">` required.
- Dynamic error styling using `:user-invalid` without harassing users before they finish typing.

---

**Next Step:**  
🏆 [Build the Level 2 Capstone: Responsive SaaS Portal Landing Page](../Final-Project/en/README.md)
