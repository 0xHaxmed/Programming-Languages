# Level 3 — Advanced Architecture, Accessibility & HTML/CSS Security

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Educational Mission

- **Goal:** Elevate your frontend engineering to senior and security-auditor standards. Master scalable CSS architecture (BEM, CSS Custom Properties), WCAG Accessibility standards, and dive deep into **pure HTML/CSS attack vectors and defenses**: Dangling Markup Injection, CSS Data Exfiltration, and Iframe Sandboxing.
- **Prerequisites:** Completion of [Level 1: Basic Structure](../Level-1-Basic/README.md) and [Level 2: Modern Layouts](../Level-2-Foundation/README.md).

---

## 🧭 Level 3 Syllabus & Lessons Directory

| # | Topic | English Guide | Arabic Guide | Core Concepts Covered |
| :-: | :--- | :--- | :--- | :--- |
| **01** | **CSS Custom Properties** | [`01-css-variables.md`](./Lessons/en/01-css-custom-properties-and-theming.md) | [`01-css-variables.md`](./Lessons/ar/01-css-custom-properties-and-theming.md) | `:root`, `--var-name`, `var()`, fallbacks, runtime dynamic dark theming. |
| **02** | **BEM CSS Architecture** | [`02-bem-architecture.md`](./Lessons/en/02-bem-architecture-and-maintainable-css.md) | [`02-bem-architecture.md`](./Lessons/ar/02-bem-architecture-and-maintainable-css.md) | Block, Element, Modifier, specificity control, avoiding specificity wars. |
| **03** | **Web Accessibility (WCAG)** | [`03-wcag-and-aria.md`](./Lessons/en/03-web-accessibility-wcag-and-aria.md) | [`03-wcag-and-aria.md`](./Lessons/ar/03-web-accessibility-wcag-and-aria.md) | WCAG 2.1 AA, `aria-label`, `aria-hidden`, keyboard navigation, color contrast. |
| **04** | **Iframe Sandboxing** | [`04-iframe-security.md`](./Lessons/en/04-iframe-sandboxing-and-clickjacking.md) | [`04-iframe-security.md`](./Lessons/ar/04-iframe-sandboxing-and-clickjacking.md) | `<iframe>`, `sandbox` flags, Clickjacking UI redress, `frame-ancestors`. |
| **05** | **Dangling Markup** | [`05-dangling-markup.md`](./Lessons/en/05-dangling-markup-injection.md) | [`05-dangling-markup.md`](./Lessons/ar/05-dangling-markup-injection.md) | Unclosed tags stealing CSRF tokens when JavaScript is blocked by CSP! |
| **06** | **CSS Data Exfiltration** | [`06-css-exfiltration.md`](./Lessons/en/06-css-data-exfiltration.md) | [`06-css-exfiltration.md`](./Lessons/ar/06-css-data-exfiltration.md) | Sniffing passwords and tokens via attribute selectors without JavaScript! |
| **07** | **SRI & CSP Foundations** | [`07-sri-and-csp.md`](./Lessons/en/07-subresource-integrity-and-csp-foundations.md) | [`07-sri-and-csp.md`](./Lessons/ar/07-subresource-integrity-and-csp-foundations.md) | `integrity="sha384-..."`, CDN compromise mitigation, Content-Security-Policy. |
| **08** | **Defensive Hardening** | [`08-defensive-hardening.md`](./Lessons/en/08-defensive-html-and-css-hardening.md) | [`08-defensive-hardening.md`](./Lessons/ar/08-defensive-html-and-css-hardening.md) | Comprehensive engineering checklist for bulletproof HTML & CSS. |

---

## 🔬 Hands-on Security Labs

Test and verify HTML/CSS security mechanics locally with zero setup:
- 👉 [Setup Guide & Single-Folder Download (English)](./Labs/SETUP_GUIDE.md) | [دليل التشغيل بالعربية](./Labs/SETUP_GUIDE.ar.md)
- 🧪 **[Lab 1: Guided Walkthrough — Dangling Markup Token Theft](./Labs/Lab-1-Guided-Walkthrough/README.md)** (Fully explained vulnerable app, exploit test, and defense patch)
- 🎯 **[Lab 2: Unsolved Challenge — CSS Attribute Selector Exfiltration](./Labs/Lab-2-Challenge-Unsolved/README.md)** (Independent student challenge + report template)

---

## 🏆 Capstone Project: Hardened Client Security Dashboard
A multi-theme, fully accessible, and defensively hardened client-side dashboard:
- 👉 [English Project Guide & Source Code](./Final-Project/en/README.md)
- 👉 [دليل وكود المشروع بالعربية](./Final-Project/ar/README.md)
