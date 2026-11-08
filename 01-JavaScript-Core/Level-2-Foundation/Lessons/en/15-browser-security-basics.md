# 15 - Browser Security Architecture: SOP, CORS & Trust Boundaries

> 🌐 **Language / اللغة:** [English 🇬🇧](./15-browser-security-basics.md) | [العربية 🇸🇦](../ar/15-browser-security-basics.md)

---

## 🎯 1. Overview & Learning Objectives

The web browser is a uniquely hostile execution environment. A single browser application concurrently executes code from your banking portal, a social media feed, and untrusted third-party advertising networks.

To prevent untrusted code from stealing private user data, the web relies on fundamental architectural sandboxes: the **Same-Origin Policy (SOP)** and **Cross-Origin Resource Sharing (CORS)**.

By the end of this lesson, you will master:
1. Calculating an exact **Web Origin** (`Protocol` + `Host` + `Port`).
2. What the **Same-Origin Policy (SOP)** protects and restricts.
3. How **CORS** functions: Simple requests, Preflight checks (`OPTIONS`), and headers.
4. The #1 developer misconception about CORS enforcement.
5. Securing third-party libraries using **Subresource Integrity (SRI)**.
6. Foundational defensive headers: **Content Security Policy (CSP)**.

---

## 🌐 2. Defining a Web Origin

An **Origin** is defined strictly by the tuple of three components:

```
  https :// example.com : 443
  \___/     \_________/   \__/
Protocol       Host       Port
```

If **any single one** of these three parts differs, the two URLs belong to **completely different origins**:

### Origin Comparison Matrix (Compared to `http://example.com/app`):

| Target URL | Same Origin? | Reason |
| :--- | :---: | :--- |
| `http://example.com/other/page` | ✅ **YES** | Exact match: Protocol (`http`), Host (`example.com`), Port (`80`) |
| `https://example.com/app` | ❌ **NO** | Protocol mismatch (`http` vs `https`) |
| `http://example.com:8080/app` | ❌ **NO** | Port mismatch (`80` vs `8080`) |
| `http://api.example.com/app` | ❌ **NO** | Host mismatch (Subdomain `api.` is a different host) |

---

## 🛡️ 3. The Same-Origin Policy (SOP)

The **Same-Origin Policy** is the cornerstone of web security. It prevents JavaScript running on `https://malicious-site.com` from accessing private data belonging to `https://bank.com`.

### What SOP Restricts:
1. **DOM Tree Access:** Code inside an `iframe` cannot read the `document` or inspect forms of another origin.
2. **Storage Segregation:** `localStorage`, `sessionStorage`, and `IndexedDB` are completely sandboxed per origin.
3. **Network Read Isolation:** A website can *send* requests cross-origin (e.g. loading an `<img>` or submitting a form), but JavaScript *cannot read the response body* without explicit server permission.

---

## 🔄 4. Cross-Origin Resource Sharing (CORS)

Modern applications frequently separate frontend assets (`https://app.example.com`) from backend APIs (`https://api.example.com`). Because they have different origins, the browser blocks the frontend from reading the API responses under SOP.

**CORS** is an HTTP-header-based mechanism that allows a server to explicitly whitelist which external origins are permitted to read its responses.

```
[ FRONTEND (https://app.com) ]                      [ BACKEND API (https://api.com) ]
              |                                                   |
              | --- 1. GET /data (Origin: https://app.com) -----> |
              |                                                   |
              | <--- 2. Access-Control-Allow-Origin: https://app.com |
              v                                                   v
   Browser confirms header matches origin ➔ Delivers data to JavaScript!
```

### The Preflight Request (`OPTIONS`)
When a request uses non-standard methods (`PUT`, `DELETE`) or custom headers (`Content-Type: application/json`, `Authorization`), the browser automatically dispatches an **`OPTIONS` preflight check** before sending the real request:

```http
OPTIONS /api/orders HTTP/1.1
Origin: https://app.example.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type, Authorization
```

The server must reply with approval headers:
```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

> [!IMPORTANT]
> **The #1 CORS Misconception:**  
> CORS is **enforced by the browser to protect users**, NOT by the server to protect itself!  
> A malicious attacker using `curl` or Python does not enforce CORS and can send requests freely. CORS exists strictly to stop hostile websites from using a victim's browser to read private authenticated API data.

---

## 📦 5. Third-Party Scripts & Subresource Integrity (SRI)

When you include a script from an external Content Delivery Network (CDN):
```html
<script src="https://cdn.example.com/library.js"></script>
```
That third-party script executes with the **full privileges of your application origin**! If the CDN is compromised, attackers can inject malicious code directly into your users' sessions.

### The Defensive Fix: Subresource Integrity (SRI)
SRI allows browsers to verify that fetched files have not been maliciously modified:
```html
<script 
  src="https://cdn.example.com/library.js"
  integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"
  crossorigin="anonymous">
</script>
```
If the file content differs by even one single byte, the cryptographic hash fails, and the browser refuses to execute the script!

---

## 🔒 6. Content Security Policy (CSP) Fundamentals

**Content Security Policy (CSP)** is an HTTP response header that restricts where resources (scripts, styles, images, frames) can be loaded and executed:

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-cdn.com;
```
- Disallows execution of inline scripts (`<script>alert(1)</script>` or `onclick="..."`).
- Prevents loading external scripts from unauthorized third-party servers.
- Dramatically mitigates Cross-Site Scripting (XSS) impacts.

---

## 🏆 Summary of Level 2 Foundation

Congratulations! You have completed all 15 lessons of **Level 2 Foundation**:
1. **Lesson 01:** Functions, Functional Iterators & Modern Data Manipulation
2. **Lesson 02:** Scope, Hoisting & The Temporal Dead Zone (TDZ)
3. **Lesson 03:** Objects, References & Memory Allocation
4. **Lesson 04:** Callbacks & Foundational Asynchronous Concurrency
5. **Lesson 05:** The Browser Environment: Window, BOM & Web APIs
6. **Lesson 06:** The Document Object Model (DOM Manipulation)
7. **Lesson 07:** The Events System: Listeners, Bubbling & Delegation
8. **Lesson 08:** Forms, Input Tracking & Validation
9. **Lesson 09:** URLs, Query Strings & Client-Side Navigation
10. **Lesson 10:** Data Serialization: The JSON Specification
11. **Lesson 11:** Network Requests & The Fetch API
12. **Lesson 12:** Asynchronous Foundations: Promises & async/await
13. **Lesson 13:** Client-Side Persistence: Web Storage & Cookies
14. **Lesson 14:** Advanced DevTools Debugging & Runtime Forensics
15. **Lesson 15:** Browser Security Architecture: SOP, CORS & Trust Boundaries

---

## 🚀 Next Steps
Advance to **[Level 2 Practical Applications](../../Practical-Applications/en/README.md)** and the **[Level 2 Final Project](../../Final-Project/en/README.md)** to apply these foundational skills in production-grade codebases!
