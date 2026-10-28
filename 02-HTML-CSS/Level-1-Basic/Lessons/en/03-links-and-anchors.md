# 03 - Hyperlinks & Secure Anchors

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-links-and-anchors.md) | [العربية 🇸🇦](../ar/03-links-and-anchors.md)

---

## 📌 1. The Anchor Element (`<a>`)

Hyperlinks are what make the web a "web". The `<a>` (anchor) tag connects the current document to other documents, specific page sections, or external protocols.

```html
<a href="https://developer.mozilla.org">MDN Web Docs</a>
```

### Essential Attributes:
- **`href` (Hypertext Reference):** The destination URL or target.
- **`target`:** Specifies where to open the linked document (e.g., `_self` default, `_blank` new tab).
- **`download`:** Instructs the browser to download the URL target instead of navigating to it.

---

## 🌐 2. Types of Hyperlink Targets

### A. Absolute vs Relative URLs:
```html
<!-- Absolute URL (External domain) -->
<a href="https://example.com/api/docs">External API Documentation</a>

<!-- Relative URL (Same site, sibling folder) -->
<a href="./about.html">About Us</a>
<a href="../dashboard/settings.html">User Settings</a>
```

### B. In-Page Fragment Anchors (Jump Links):
```html
<!-- Navigation Link -->
<a href="#security-section">Jump to Security Details</a>

<!-- Target Section -->
<h2 id="security-section">Security & Compliance</h2>
```

### C. Protocol Schemes (`mailto:` and `tel:`):
```html
<a href="mailto:security@example.com">Email Security Team</a>
<a href="tel:+18005550199">Call Support Desk</a>
```

---

## 🛡️ 3. Security Warning: The `target="_blank"` Tabnabbing Vulnerability

When an anchor opens a link in a new tab using `target="_blank"`, the newly opened window gets an execution reference to the opener window via `window.opener`:

```
   Original Page (Victim) ───[ target="_blank" ]───► Malicious Site
                                                              │
   Victim Page redirects to Phishing Login ◄── window.opener.location
```

A malicious destination site can execute:
```javascript
window.opener.location = "https://phishing-site.com/fake-login";
```
When the user switches back to the original tab, they see a fake login form and may enter their password!

### 🔒 The Defense: `rel="noopener noreferrer"`
```html
<!-- SECURE: Disconnects the opener context and strips referrer header -->
<a href="https://untrusted-external-site.com" target="_blank" rel="noopener noreferrer">
    Visit External Resource
</a>
```
- **`noopener`:** Sets `window.opener` to `null` in the new browsing context.
- **`noreferrer`:** Prevents the browser from leaking the current page URL in the HTTP `Referer` request header.

---

## ⚠️ 4. Security Warning: Dangerous `javascript:` Links

Never use `href="javascript:..."` for buttons or event triggers:
```html
<!-- DANGEROUS: If user-controlled input lands here, it triggers XSS! -->
<a href="javascript:alert(1)">Click Me</a>

<!-- SECURE: Use a <button> element with an event listener -->
<button type="button" class="action-btn">Safe Action</button>
```

---

## 🎯 Summary & Lesson Challenge

- Always use descriptive link text (never just "click here"—screen reader users list links out of context).
- For external links using `target="_blank"`, **always attach `rel="noopener noreferrer"`**.
- For clickable actions that do not navigate to a new document, use `<button>` instead of `<a>`.

**Challenge:** Create an HTML page containing an internal jump link navigating to the bottom of the page, and a secure external link with `target="_blank"` and `rel="noopener noreferrer"`.

---

**Next Lesson:**  
➡️ [Lesson 04: Media Elements (Images, Audio & Video)](./04-media-images-and-audio-video.md) | ⬅️ [Lesson 02: Headings & Paragraphs](./02-text-headings-and-paragraphs.md)
