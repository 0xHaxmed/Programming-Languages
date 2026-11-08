# 08 - Forms, Input Tracking & Validation

> 🌐 **Language / اللغة:** [English 🇬🇧](./08-forms-and-validation.md) | [العربية 🇸🇦](../ar/08-forms-and-validation.md)

---

## 🎯 1. Overview & Learning Objectives

HTML Forms are the primary vehicle for user input in web applications. Historically, forms triggered a full-page HTTP POST reload. In modern Single-Page Applications (SPAs), JavaScript intercepts form submissions, validates inputs asynchronously, and transmits data via JSON APIs.

By the end of this lesson, you will master:
1. Intercepting form submissions cleanly with `submit` and `e.preventDefault()`.
2. Extracting form values declaratively using the modern **`FormData` API**.
3. The behavioral differences between `input`, `change`, `focus`, and `blur` events.
4. Leveraging the browser's native **HTML5 Constraint Validation API**.
5. The critical security boundary: **Client-side UX validation vs Server-side security validation**.
6. Building accessible, production-grade validation architectures.

---

## 🛑 2. Intercepting Form Submissions

A classic mistake is binding a `click` listener to the submit button. Always listen for the **`submit` event on the `<form>` element itself**:

```javascript
const registrationForm = document.querySelector('#register-form');

registrationForm.addEventListener('submit', (event) => {
  // 1. Halt full-page browser refresh:
  event.preventDefault();

  console.log('Form submission intercepted by JavaScript.');
});
```

> [!NOTE]
> Listening to `submit` on the `<form>` automatically handles both **button clicks** and when the user presses **Enter** inside any input field.

---

## 📦 3. Reading Input Values: `FormData` vs Manual Lookups

### The Modern Way: `FormData` API
Instead of querying every single `<input>` element manually by ID, the `FormData` object gathers all inputs possessing a `name` attribute in a single step:

```html
<form id="user-form">
  <input type="text" name="username" value="alex99">
  <input type="email" name="email" value="alex@example.com">
  <input type="checkbox" name="subscribe" checked>
</form>
```

```javascript
const form = document.querySelector('#user-form');
const formData = new FormData(form);

// 1. Retrieve individual fields by 'name' attribute:
const username = formData.get('username'); // 'alex99'
const isSubscribed = formData.get('subscribe') === 'on'; // true

// 2. Convert the entire form to a clean JavaScript Object for JSON API requests:
const payload = Object.fromEntries(formData.entries());
console.log(payload);
// { username: 'alex99', email: 'alex@example.com', subscribe: 'on' }
```

---

## ⚡ 4. Input Tracking Events: `input` vs `change` vs `blur`

Choosing the correct event determines whether validation feels responsive or frustrating to the user:

```javascript
const emailInput = document.querySelector('#email-field');

// 1. 'input': Fires synchronously on EVERY single keystroke or character deletion:
emailInput.addEventListener('input', (e) => {
  // Perfect for live character counters or password strength meters:
  console.log('Current value:', e.target.value);
});

// 2. 'change': Fires only when the user commits the value (presses Enter or tabs out of the input):
emailInput.addEventListener('change', (e) => {
  console.log('Value committed:', e.target.value);
});

// 3. 'blur': Fires when the input LOSES focus:
emailInput.addEventListener('blur', (e) => {
  // Industry Standard: Run field validation on blur so users aren't interrupted while typing!
  validateEmailField(e.target);
});
```

---

## 🛡️ 5. The HTML5 Constraint Validation API

Modern browsers feature a built-in validation engine using standard HTML attributes:
- `required`
- `minlength="4"` / `maxlength="20"`
- `type="email"` / `type="url"`
- `pattern="^[A-Za-z0-9_]+$"` (Regular Expression match)

You can interface with this engine programmatically via JavaScript:

```javascript
const input = document.querySelector('#username-input');

// 1. checkValidity(): Returns true if valid, false if invalid:
if (!input.checkValidity()) {
  console.warn('Field is invalid!');
  
  // 2. validity object: Inspect the specific failure reason:
  if (input.validity.valueMissing) {
    console.log('Error: This field is required.');
  } else if (input.validity.tooShort) {
    console.log('Error: Username must be longer.');
  } else if (input.validity.patternMismatch) {
    console.log('Error: Alphanumeric characters only.');
  }
}

// 3. Custom error messages:
input.setCustomValidity('This username is already taken. Please choose another.');
```

---

## 🚨 6. The Golden Rule of Form Security

> [!CAUTION]
> **Client-Side Validation is for User Experience (UX) ONLY!**  
> Client-side validation can be bypassed with trivial ease:
> 1. An attacker can open DevTools and delete `required` or `pattern` attributes from the HTML.
> 2. An attacker can disable JavaScript entirely in their browser.
> 3. An attacker can send HTTP requests directly using `curl` or Postman, completely bypassing the browser.
> 
> **Never trust client-side validation for security! The backend server MUST independently validate, sanitize, and authorize every single field submitted.**

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Manual individual element querying for large forms
const name = document.querySelector('#name').value;
const email = document.querySelector('#email').value;
const role = document.querySelector('#role').value;
const city = document.querySelector('#city').value;

// ✅ GOOD: Declarative FormData extraction
const formData = new FormData(event.target);
const userProfile = Object.fromEntries(formData.entries());

// ❌ BAD: Showing aggressive validation errors on every single keystroke while typing
input.addEventListener('input', () => {
  if (!isValid(input.value)) showError('INVALID!'); // Frustrating UX!
});

// ✅ GOOD: Validate on blur or form submit, and clear error on input
input.addEventListener('blur', () => {
  if (!isValid(input.value)) showError('Please enter a valid format.');
});
input.addEventListener('input', () => {
  clearError(); // Remove error message as soon as user starts correcting it
});
```

---

## 📝 8. Summary & Next Steps
- Always listen to **`submit` on `<form>`**, not `click` on `<button>`.
- Always call **`event.preventDefault()`** to prevent full-page browser reloads.
- Use **`FormData`** and **`Object.fromEntries()`** to extract clean JSON payloads.
- Validate on **`blur`** for optimal user experience.
- Remember: **Client validation is UX; server validation is security.**
- In the next lesson, we will explore **URLs, Query String Parsing, and Navigation via `URLSearchParams`**.
