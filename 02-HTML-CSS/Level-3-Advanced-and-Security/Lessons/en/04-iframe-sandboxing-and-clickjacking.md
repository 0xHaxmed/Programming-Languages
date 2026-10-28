# 04 - Iframe Sandboxing & Clickjacking Defenses

> 🌐 **Language / اللغة:** [English 🇬🇧](./04-iframe-sandboxing-and-clickjacking.md) | [العربية 🇸🇦](../ar/04-iframe-sandboxing-and-clickjacking.md)

---

## 📌 1. The Double-Edged Sword of `<iframe>`

The `<iframe>` (Inline Frame) element embeds an independent nested browsing context into an HTML document. While essential for payment gateways, widget embedding, and third-party media, unconstrained iframes introduce severe client-side vulnerabilities.

---

## 🛡️ 2. The `sandbox` Attribute: Principle of Least Privilege

By default, an embedded iframe executes with full web capabilities: it can run JavaScript, submit forms, open popups, and manipulate top-level navigation.

HTML5 introduced the **`sandbox` attribute**, which places the embedded document into an isolated, unique-origin sandbox:

```html
<!-- MAXIMUM RESTRICTION: JavaScript, forms, cookies, and popups are ALL BLOCKED -->
<iframe src="https://untrusted-partner.com/widget" sandbox></iframe>
```

### Granular Sandbox Flags:
You can selectively restore only the precise permissions required:

| Flag | Capability Enabled |
| :--- | :--- |
| `allow-scripts` | Permits JavaScript execution inside the iframe. |
| `allow-same-origin` | Allows the iframe to retain its normal origin (accessing its cookies/storage). |
| `allow-forms` | Allows form submissions from inside the frame. |
| `allow-popups` | Allows `window.open()` popups. |
| `allow-top-navigation` | Permits the iframe to navigate the top-level parent window. |

```html
<!-- Hardened Sandbox Configuration -->
<iframe src="https://reports.partner.com" 
        sandbox="allow-scripts allow-forms" 
        title="Quarterly Analytics Widget">
</iframe>
```

> ⚠️ **CRITICAL SECURITY HAZARD:** **Never combine `allow-scripts` and `allow-same-origin` together when framing untrusted content from your own domain!** If both are present, malicious scripts inside the iframe can programmatically remove the `sandbox` attribute and break free of all isolation!

---

## 🎭 3. Clickjacking (UI Redress Attack)

**Clickjacking** is an attack where a malicious site loads a legitimate, authenticated victim application (e.g., your bank's "Transfer Funds" or GitHub's "Delete Repository" page) inside a transparent `<iframe>` overlaid directly on top of an enticing lure:

```
  Attacker Page View
 ┌────────────────────────────────────────────────────────┐
 │   "Click Here to Win a Free iPhone!"                   │
 │   ┌───────────────────────────┐                        │
 │   │       [ PLAY NOW ]        │                        │
 │   └───────────────────────────┘                        │
 └────────────────────────────────────────────────────────┘

  What the Victim is ACTUALLY Clicking (Invisible Iframe overlay: opacity: 0; z-index: 9999)
 ┌────────────────────────────────────────────────────────┐
 │   Bank Portal                                          │
 │   ┌───────────────────────────┐                        │
 │   │  [ Confirm $5,000 Wire ]  │ ◄── Click lands here!  │
 │   └───────────────────────────┘                        │
 └────────────────────────────────────────────────────────┘
```

Because the victim is already logged into the bank in their browser, their session cookie is sent with the click, authorizing the wire transfer!

---

## 🔒 4. Defending Your Site Against Clickjacking

To prevent third-party malicious sites from embedding your sensitive pages inside an iframe:

### A. The Modern Standard: Content Security Policy (`frame-ancestors`)
Send this HTTP response header with every HTML page:
```http
# Prevent ANY site from framing your application
Content-Security-Policy: frame-ancestors 'none';

# Or allow framing ONLY by your own domain:
Content-Security-Policy: frame-ancestors 'self';
```

### B. Legacy Fallback: `X-Frame-Options`
For legacy browser support:
```http
X-Frame-Options: DENY
# or
X-Frame-Options: SAMEORIGIN
```

---

## 🎯 Summary & Lesson Challenge

- Always sandbox untrusted iframes using `sandbox`.
- Never combine `allow-scripts` with `allow-same-origin` on your own domain.
- Protect your application from Clickjacking using `Content-Security-Policy: frame-ancestors 'none'`.

**Challenge:** Create a test HTML file that attempts to embed an external website inside an iframe. Inspect the browser console to observe security errors when a site blocks framing via `frame-ancestors`.

---

**Next Lesson:**  
➡️ [Lesson 05: Dangling Markup Injection (Stealing Tokens without JS)](./05-dangling-markup-injection.md) | ⬅️ [Lesson 03: Web Accessibility (WCAG)](./03-web-accessibility-wcag-and-aria.md)
