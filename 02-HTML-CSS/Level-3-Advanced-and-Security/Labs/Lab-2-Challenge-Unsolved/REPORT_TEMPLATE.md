# Security Assessment Report: CSS Data Exfiltration Finding

**Assessment Target:** Vanguard Cloud Key Vault  
**Vulnerability Type:** CSS Data Exfiltration via Attribute Selectors  
**Assessed By:** Security Engineer / Pentester  
**Date:** 2026-10-28  

---

## 1. Executive Summary
Briefly explain the business impact of allowing unsanitized CSS injection in an enterprise dashboard and how it enabled non-JavaScript token theft.

---

## 2. Technical Vulnerability Details
- **Vulnerability Title:** Sensitive Token Disclosure via CSS Attribute Prefix Selectors
- **CWE Classification:** CWE-200: Exposure of Sensitive Information to an Unauthorized Actor
- **CVSS v3.1 Score:** 6.5 (Medium / High)
- **Vector String:** `CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:U/C:H/I:N/A:N`

---

## 3. Step-by-Step Proof of Concept (PoC)
1. Navigate to the Custom Dashboard Theme Injector.
2. Inject the following CSS rules:
   ```css
   /* Paste your working PoC rules here */
   ```
3. Observe outbound HTTP requests in the Network sniffer log.
4. Extracted Flag Token:
   `FLAG{...}`

---

## 4. Root Cause Analysis
Explain why the layout engine evaluates `background-image: url(...)` only when the attribute selector matches, and why strict CSP without `style-src 'self'` failed to block this.

---

## 5. Strategic Remediation Guidance
1. **CSP Hardening:** Remove `'unsafe-inline'` from `style-src` and restrict `img-src` to `'self'`.
2. **Input Sanitization:** Sanitize or disallow user-controlled CSS stylesheets.
3. **DOM Attribute Protection:** Never reflect raw secret tokens in client-side HTML attributes.
