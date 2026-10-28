# 03 - Native Form Validation & Client-Side Security

> 🌐 **Language / اللغة:** [English 🇬🇧](./03-form-validation-and-security.md) | [العربية 🇸🇦](../ar/03-form-validation-and-security.md)

---

## 📌 1. Native HTML5 Constraint Validation

Before HTML5, form validation required writing verbose JavaScript routines. HTML5 provides built-in declarative validation attributes:

```html
<form action="/api/register" method="POST">
    <!-- Mandatory field -->
    <label for="username">Username (Alphanumeric, 4-16 chars):</label>
    <input type="text" 
           id="username" 
           name="username" 
           required 
           minlength="4" 
           maxlength="16" 
           pattern="[a-zA-Z0-9_]+" 
           title="Letters, numbers, and underscores only.">

    <!-- Email validation -->
    <label for="email">Work Email:</label>
    <input type="email" id="email" name="email" required>

    <!-- Range constraints -->
    <label for="age">Security Clearance Level (1 to 5):</label>
    <input type="number" id="age" name="clearance" min="1" max="5" value="1">

    <button type="submit">Submit Request</button>
</form>
```

### Core Validation Attributes:
- **`required`:** Form submission is blocked until this field contains a value.
- **`pattern="regex"`:** Evaluates the input against a client-side regular expression.
- **`minlength` / `maxlength`:** Enforces string length boundaries.
- **`min` / `max` / `step`:** Constrains numeric and date boundaries.

---

## 🎨 2. Styling Validation States with CSS Pseudo-Classes

You can style input fields dynamically as users interact with them:

```css
/* Indicate mandatory fields */
input:required {
    border-left: 3px solid #0284c7;
}

/* Modern pseudo-class: only triggers AFTER the user attempts interaction */
input:user-invalid {
    border-color: #ef4444;
    background-color: #fef2f2;
}

input:user-valid {
    border-color: #10b981;
}
```

---

## 🛡️ 3. The Security Paradox: Client-Side vs Server-Side Validation

> ⚠️ **The Golden Security Axiom:** **Client-side validation is strictly for User Experience (UX), NEVER for Security!**

```
 [ Browser / Client ]                                   [ Backend Server ]
 ┌──────────────────────┐                             ┌──────────────────────┐
 │ HTML5 Validation:    │                             │ Strict Server Logic: │
 │  required, pattern   │ ────(Burp Suite / curl)────►│ MUST Re-validate     │
 │ (Can be disabled in  │  (Bypasses all client rules)│  EVERY SINGLE FIELD! │
 │  1 second by user!)  │                             │                      │
 └──────────────────────┘                             └──────────────────────┘
```

### How Attackers Bypass Client Validation:
1. **DevTools:** Opening the inspector and simply deleting the `required` or `pattern` attribute from the DOM.
2. **Proxy Interception (Burp Suite):** Submitting valid data in the browser, intercepting the HTTP POST request, and modifying the payload to malicious data before sending it to the server.
3. **Direct API Calls:** Sending requests via `curl` or Python `requests` directly to the backend endpoint, completely bypassing HTML.

**Conclusion:** Always treat all incoming form data as hostile on the backend server, regardless of frontend rules.

---

## 🔑 4. Autofill Protection & The `autocomplete` Attribute

Modern browsers store user passwords, addresses, and credit cards for automated autofill. Correctly annotating fields prevents security and privacy hazards:

```html
<!-- Explicitly declare credential context -->
<input type="text" name="username" autocomplete="username">
<input type="password" name="password" autocomplete="current-password">

<!-- New password creation (prevents browser suggesting existing passwords) -->
<input type="password" name="new-password" autocomplete="new-password">

<!-- SMS One-Time-Passcode (2FA / OTP) -->
<input type="text" name="otp" autocomplete="one-time-code" inputmode="numeric">

<!-- Disable autocomplete for sensitive one-time tokens -->
<input type="text" name="api-key" autocomplete="off">
```

---

## 🎯 Summary & Lesson Challenge

- Use `required`, `pattern`, `minlength`, and `maxlength` for rapid user feedback.
- Use `:user-invalid` for smooth validation UI.
- Never rely on client validation for authorization or security decisions.
- Annotate sensitive fields with explicit `autocomplete` directives.

**Challenge:** Create a password reset form with fields for `Current Password`, `New Password` (requiring at least 8 characters with a number using `pattern`), and `autocomplete` attributes. Test disabling the validation in Chrome DevTools to understand client bypasses.

---

**Next Lesson:**  
➡️ [Lesson 04: Flexbox Fundamentals (Containers & 1D Flow)](./04-flexbox-fundamentals.md) | ⬅️ [Lesson 02: Forms & Inputs](./02-html-forms-and-inputs.md)
