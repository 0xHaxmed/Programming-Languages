# 02 - HTML5 Forms & Accessible Input Elements

> 🌐 **Language / اللغة:** [English 🇬🇧](./02-html-forms-and-inputs.md) | [العربية 🇸🇦](../ar/02-html-forms-and-inputs.md)

---

## 📌 1. The Anatomy of a Web Form (`<form>`)

Forms are the primary interactive channel enabling users to send data to servers.

```html
<form action="/api/v1/auth/login" method="POST" novalidate>
    <!-- Form Controls -->
</form>
```

### Essential Form Attributes:
- **`action`:** The endpoint URL where form data is submitted.
- **`method`:** HTTP request method:
  - `GET`: Appends fields into the URL query string (`/search?q=security`). **Never use GET for passwords or sensitive data!**
  - `POST`: Sends payload in the HTTP request body.
- **`enctype`:** Content encoding type:
  - `application/x-www-form-urlencoded`: Default key-value pairs.
  - `multipart/form-data`: **Mandatory** when the form includes file uploads (`<input type="file">`).

---

## 🏷️ 2. The `<label>` Element & Explicit Binding

> ♿ **Golden Accessibility Rule:** Every form input **MUST** have an associated `<label>` using the `for` attribute matching the input's `id`.

```html
<!-- ACCESSIBLE & USABLE: Clicking label focuses the input! -->
<label for="user-email">Email Address</label>
<input type="email" id="user-email" name="email" required>
```

### Why Placeholders Are NOT Labels:
- `placeholder="Enter email"` is not a replacement for a label! Placeholders vanish once the user starts typing, causing cognitive disorientation and failing accessibility guidelines.

---

## 🎛️ 3. Modern Input Types (`<input>`)

HTML5 provides specialized input types that automatically trigger optimized keyboards on mobile devices and provide native format parsing:

| Input Type | Mobile Keyboard Triggered | Native Behavior |
| :--- | :--- | :--- |
| `type="text"` | Standard text keyboard | Default single-line text |
| `type="email"` | Keyboard with `@` and `.com` | Validates email syntax structure |
| `type="password"` | Masks characters (`•••`) | Prevents shoulder surfing |
| `type="number"` | Numeric keypad | Accepts `min`, `max`, `step` |
| `type="tel"` | Telephone dial pad | Ideal for phone numbers |
| `type="url"` | Keyboard with `.com`, `/` | Validates valid protocol URI |
| `type="date"` | Native date picker calendar | Returns `YYYY-MM-DD` |
| `type="checkbox"` | Checkbox toggle | Binary or multi-select |
| `type="radio"` | Circular radio button | Single selection per shared `name` |
| `type="file"` | File browser dialog | Supports `accept=".png,.pdf"` |

---

## 📦 4. Complex Controls: Select, Textarea & Fieldsets

### A. Radio Groups with `<fieldset>` and `<legend>`:
```html
<fieldset>
    <legend>Select Security Notification Channel</legend>
    <label>
        <input type="radio" name="channel" value="sms" checked> SMS
    </label>
    <label>
        <input type="radio" name="channel" value="email"> Email
    </label>
</fieldset>
```

### B. Dropdown Select (`<select>`):
```html
<label for="role-select">Account Role</label>
<select id="role-select" name="role">
    <option value="" disabled selected>Choose a role...</option>
    <option value="analyst">Security Analyst</option>
    <option value="engineer">DevSecOps Engineer</option>
</select>
```

### C. Multiline Text (`<textarea>`):
```html
<label for="issue-desc">Incident Description</label>
<textarea id="issue-desc" name="description" rows="4" cols="50"></textarea>
```

---

## 🔘 5. Buttons in Forms

- `<button type="submit">`: Submits the parent form (default behavior if `type` is omitted).
- `<button type="button">`: Generic button for JavaScript hooks (prevents unintended form submission).
- `<button type="reset">`: Clears form controls back to defaults (rarely used).

---

## 🎯 Summary & Lesson Challenge

- Always pair inputs with `<label for="...">`.
- Use specific input types (`email`, `tel`, `number`) to optimize mobile usability.
- Use `POST` with `multipart/form-data` for file uploads.

**Challenge:** Build an accessible registration form containing inputs for full name, email, password, role dropdown, terms agreement checkbox, and a submit button.

---

**Next Lesson:**  
➡️ [Lesson 03: Native Form Validation & Client-Side Security](./03-form-validation-and-security.md) | ⬅️ [Lesson 01: Semantic Layout](./01-semantic-html5-layout.md)
