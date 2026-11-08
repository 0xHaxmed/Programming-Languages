# Level 2 — Foundation | المستوى الثاني: التأسيس والتفاعل مع المتصفح

> 🌐 **اللغة:** **العربية 🇸🇦** | [English Version (README.md)](./README.md)

---

## 🎯 الهدف التعليمي للمستوى الثاني

- **الهدف:** فهم JavaScript الحقيقية داخل المتصفح، وبناء الأساس اللازم لقراءة وتطوير تطبيقات الويب الحية وتحليل سلوكها.
- **التركيز:** الانتقال من كتابة أكواد منفصلة إلى فهم كود يتفاعل مع صفحة ويب، مستخدم حقيقي، وخوادم خارجية عبر الشبكة.

---

## 🧭 مسار دروس المستوى الثاني

| # | المجال | الموضوعات والتفاصيل | الرابط المباشر |
| :-: | :--- | :--- | :---: |
| **01** | **الدوال والبيانات** | الدوال السهمية (Arrow Functions)، دوال المصفوفات (`map`, `filter`, `reduce`)، التفكيك (Destructuring)، و Spread/Rest. | [عربي](./Lessons/ar/01-functions-and-data.md) / [English](./Lessons/en/01-functions-and-data.md) |
| **02** | **النطاق والرفع** | النطاق العام (Global)، نطاق الدالة (Function)، ونطاق الكتلة (Block Scope)، والرفع (Hoisting). | [عربي](./Lessons/ar/02-scope-and-hoisting.md) / [English](./Lessons/en/02-scope-and-hoisting.md) |
| **03** | **الكائنات والمراجع** | الكائنات المتداخلة (Nested Objects)، التمرير بالمرجع، والنسخ السطحي والعميق. | [عربي](./Lessons/ar/03-objects-and-references.md) / [English](./Lessons/en/03-objects-and-references.md) |
| **04** | **رد النداء (Callbacks)** | آليات تمرير الدوال كمعاملات (Callbacks) والتعامل مع الأحداث غير المتزامنة. | [عربي](./Lessons/ar/04-callbacks.md) / [English](./Lessons/en/04-callbacks.md) |
| **05** | **بيئة المتصفح** | كائني `window` و `document`، والفرق بين محرك جافا سكريبت والـ Web APIs. | [عربي](./Lessons/ar/05-browser-environment.md) / [English](./Lessons/en/05-browser-environment.md) |
| **06** | **التحكم بالـ DOM** | شجرة الـ DOM، محددات العناصر (Selectors)، وإنشاء وتعديل وحذف العناصر ديناميكياً. | [عربي](./Lessons/ar/06-dom-manipulation.md) / [English](./Lessons/en/06-dom-manipulation.md) |
| **07** | **نظام الأحداث** | الاستماع للأحداث (EventListeners)، تصاعد الأحداث (Bubbling)، وتفويض الأحداث (Delegation). | [عربي](./Lessons/ar/07-events-system.md) / [English](./Lessons/en/07-events-system.md) |
| **08** | **النماذج والمدخلات** | قراءة وتتبع مدخلات المستخدم ومنع الإرسال التلقائي عبر `preventDefault()`. | [عربي](./Lessons/ar/08-forms-and-validation.md) / [English](./Lessons/en/08-forms-and-validation.md) |
| **09** | **الروابط والتنقل** | التعامل مع `location`، وتحليل المعاملات باستخدام كائن `URLSearchParams`. | [عربي](./Lessons/ar/09-urls-and-navigation.md) / [English](./Lessons/en/09-urls-and-navigation.md) |
| **10** | **بيانات JSON** | تحويل الكائنات إلى نصوص عبر `JSON.stringify` وتحويل النصوص لكائنات عبر `JSON.parse`. | [عربي](./Lessons/ar/10-json.md) / [English](./Lessons/en/10-json.md) |
| **11** | **طلبات الشبكة** | دالة `fetch()`، طلبات HTTP (GET/POST)، قراءة الـ Headers، والتعامل مع الـ Responses. | [عربي](./Lessons/ar/11-network-requests.md) / [English](./Lessons/en/11-network-requests.md) |
| **12** | **البرمجة غير المتزامنة** | أساسيات الوعود (Promises)، وحالاتها، واستخدام `async/await`. | [عربي](./Lessons/ar/12-async-basics.md) / [English](./Lessons/en/12-async-basics.md) |
| **13** | **تخزين المتصفح** | التخزين الدائم (`localStorage`) والمؤقت (`sessionStorage`) وملفات الـ Cookies. | [عربي](./Lessons/ar/13-browser-storage.md) / [English](./Lessons/en/13-browser-storage.md) |
| **14** | **أدوات التصحيح DevTools** | استخدام تبويبات Sources و Network في المتصفح، ووضع نقاط التوقف (Breakpoints). | [عربي](./Lessons/ar/14-devtools-debugging.md) / [English](./Lessons/en/14-devtools-debugging.md) |
| **15** | **أساسيات أمان المتصفح** | سياسة المصدر نفسه (Same-Origin Policy - SOP)، الـ CORS، والحدود الفاصلة بين العميل والسيرفر. | [عربي](./Lessons/ar/15-browser-security-basics.md) / [English](./Lessons/en/15-browser-security-basics.md) |

---

## 🧪 معامل وتطبيقات الـ DOM العملية
طبق المهارات بشكل عملي في بيئة المتصفح:
- 👉 [معمل التطبيقات العملية باللغة العربية](./Practical-Applications/ar/README.md)
- 👉 [Applied Browser Labs (English)](./Practical-Applications/en/README.md)

---

## 🏆 مشروع نهاية المستوى: API Explorer
قم ببناء وتشغيل أداة تفاعلية لإرسال طلبات الـ HTTP وفحص بيانات الـ JSON والأخطاء:
- 👉 [دليل وكود المشروع بالعربية](./Final-Project/ar/README.md)
- 👉 [English Project Guide & Architecture](./Final-Project/en/README.md)

---

## ✅ قائمة التحقق قبل الانتقال للمستوى الثالث
- [ ] تفهم لماذا يُعتبر استخدام `innerHTML` خطراً أمنياً مع مدخلات المستخدم.
- [ ] تستطيع إرسال طلبات `fetch()` والتعامل مع حالات الخطأ باستخدام `try/catch`.
- [ ] تعرف كيفية إيقاف تنفيذ الكود مؤقتاً عبر نقاط التوقف (Breakpoints) في المتصفح.
- [ ] تفهم أن الـ CORS هي سياسة يفرضها المتصفح لحماية المستخدم وليست خطأ في لغة JS نفسها.
