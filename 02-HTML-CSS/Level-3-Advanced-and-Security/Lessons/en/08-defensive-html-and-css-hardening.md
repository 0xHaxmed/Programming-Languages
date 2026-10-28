# 08 - Defensive HTML & CSS Hardening Checklist

> 🌐 **Language / اللغة:** [English 🇬🇧](./08-defensive-html-and-css-hardening.md) | [العربية 🇸🇦](../ar/08-defensive-html-and-css-hardening.md)

---

## 📌 1. The Production Security Baseline

Securing client-side web applications requires a holistic, defensive mindset. Below is the industry-standard **HTML & CSS Defensive Hardening Checklist** synthesized across Levels 1, 2, and 3.

---

## 🛡️ 2. The Comprehensive Hardening Checklist

### 1. Document & Metadata Hygiene
- [ ] Document starts with standard `<!DOCTYPE html>`.
- [ ] Root `<html lang="...">` declares explicit language for screen readers and SEO.
- [ ] `<meta charset="UTF-8">` is declared before any content to prevent UTF-7 encoding bypasses.
- [ ] Mobile viewport is set: `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.

### 2. Hyperlinks & Navigation
- [ ] Every external link with `target="_blank"` includes `rel="noopener noreferrer"`.
- [ ] Zero occurrences of `href="javascript:..."` (replaced with semantic `<button type="button">`).
- [ ] Text links are descriptive (no vague "click here" text).

### 3. Media & Assets
- [ ] Every non-decorative `<img>` has a meaningful `alt` description (`alt=""` for decorative).
- [ ] Explicit `width` and `height` attributes on images to prevent Cumulative Layout Shift (CLS).
- [ ] Untrusted user-uploaded SVG files are sanitized or served with `Content-Disposition: attachment` to eliminate Stored XSS.

### 4. Forms & Input Controls
- [ ] Every form input is bound to an explicit `<label for="...">`.
- [ ] Sensitive forms use `method="POST"` (never `GET`).
- [ ] Forms with file uploads declare `enctype="multipart/form-data"`.
- [ ] Sensitive credential fields define `autocomplete="current-password"`, `new-password`, or `one-time-code`.
- [ ] Backend server re-validates and sanitizes 100% of form inputs (zero blind trust in client validation).

### 5. Iframes & Framing
- [ ] Third-party iframes declare explicit `sandbox` attributes.
- [ ] `allow-scripts` and `allow-same-origin` are NEVER combined on untrusted content from the same domain.
- [ ] HTTP response header enforces `Content-Security-Policy: frame-ancestors 'none'` to block Clickjacking.

### 6. CSS Architecture & Injection Prevention
- [ ] Global `box-sizing: border-box` applied to eliminate sizing bugs.
- [ ] BEM methodology used to maintain flat `(0, 0, 1, 0)` specificity without `!important`.
- [ ] Application does not render unsanitized user-controlled CSS strings (blocking CSS Data Exfiltration).
- [ ] Visible focus indicators preserved with high-contrast `:focus-visible` styling.
- [ ] High-contrast color ratios comply with WCAG 2.1 AA (minimum 4.5:1).

### 7. Supply Chain & Headers
- [ ] All external CDN scripts and stylesheets declare `integrity` (SRI hashes) and `crossorigin="anonymous"`.
- [ ] Strict Content Security Policy blocks `'unsafe-inline'` styles and scripts.
- [ ] `base-uri 'self'` and `form-action 'self'` defined in CSP.

---

## 🎯 3. How to Audit Any Page in 5 Minutes

```
 5-Minute HTML/CSS Security Audit Workflow
 ┌────────────────────────────────────────────────────────────┐
 │ 1. Check HTTP Headers: CSP, frame-ancestors, X-Frame-Opt   │
 │ 2. Inspect <a> tags: Look for target="_blank" without rel  │
 │ 3. Check Form Inputs: Look for secrets pre-populated in DOM│
 │ 4. Search for inline styles: <style> or style="..."        │
 │ 5. Inspect <iframe> elements: Verify sandbox flags         │
 └────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary & Track Milestone

You have completed the entire theoretical curriculum of the HTML5 & CSS3 Master Engineering Track!

Now, put your defensive skills to the test in the **Level 3 Security Labs** and the **Capstone Dashboard**!

---

**Next Steps:**  
🧪 [Lab 1: Guided Walkthrough — Dangling Markup Token Theft](../Labs/Lab-1-Guided-Walkthrough/README.md)  
🎯 [Lab 2: Unsolved Challenge — CSS Data Exfiltration](../Labs/Lab-2-Challenge-Unsolved/README.md)  
🏆 [Level 3 Capstone: Hardened Client Security Dashboard](../Final-Project/en/README.md)
