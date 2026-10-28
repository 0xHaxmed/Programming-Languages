# Level 2 — Foundation | المستوى الثاني: التأسيس والتفاعل مع المتصفح

> 🌐 **اللغة / Language:** [العربية 🇸🇦](#-نظرة-عامة) | [English 🇬🇧](#-overview-english)

---

## 🇸🇦 نظرة عامة

- **الهدف:** تفهم سلوك JavaScript الحقيقية داخل المتصفح وبناء الأساس اللازم لقراءة تطبيقات الويب وتحليلها.
- **التركيز:** الانتقال من كتابة كود منفصل إلى فهم كود يتفاعل مع صفحة ويب حية، مستخدم، وخادم خارجي (Server).

### 📋 قائمة الموضوعات (Syllabus)

| # | المجال | الموضوعات والتفاصيل |
| :-: | :--- | :--- |
| **01** | **Functions & Data** | الدوال السهمية (Arrow Functions)، دوال المصفوفات (`map`, `filter`, `reduce`)، التفكيك (Destructuring)، ومُعامِلي Spread و Rest. |
| **02** | **Scope & Hoisting** | النطاق العام (Global)، نطاق الدالة (Function)، ونطاق الكتلة (Block Scope)، والرفع (Hoisting). |
| **03** | **Objects & References** | الكائنات المتداخلة (Nested Objects)، تمرير الكائنات بالمرجع (By Reference)، ومعالجة الخصائص. |
| **04** | **Callbacks** | آليات تمرير الدوال كمعاملات (Passing Functions as Arguments) ومعالجة التزامن البدائي. |
| **05** | **Browser Environment** | كائني `window` و `document`، والفرق الجوهري بين لغة JS والـ Browser Web APIs. |
| **06** | **DOM Manipulation** | شجرة الـ DOM، محددات العناصر (Selectors)، وإنشاء وتعديل وحذف العناصر ديناميكياً. |
| **07** | **Events System** | الاستماع للأحداث (Event Listeners)، تصاعد الأحداث (Bubbling)، والالتقاط (Capturing)، وتفويض الأحداث (Delegation). |
| **08** | **Forms & Inputs** | قراءة وتتبع مدخلات المستخدم والتحقق منها (Client-Side Validation). |
| **09** | **URLs & Navigation** | التعامل مع `window.location`، وكائنات `URL` و `URLSearchParams`، والتنقل بين الصفحات. |
| **10** | **JSON** | تحويل وتنسيق البيانات بين الكائنات و `JSON.stringify()` و `JSON.parse()`. |
| **11** | **Network Requests** | دالة `fetch()`، طلبات HTTP (GET/POST)، قراءة الـ Headers، والتعامل مع الـ Responses. |
| **12** | **Asynchronous Basics** | أساسيات الوعود (Promises)، وحالاتها، واستخدام `async/await`. |
| **13** | **Browser Storage** | تخزين البيانات محلياً عبر `localStorage` و `sessionStorage`، ومقدمة عن ملفات تعريف الارتباط (Cookies). |
| **14** | **DevTools Debugging** | استخدام تبويبات Sources و Network في أدوات المتصفح، ووضع نقاط التوقف (Breakpoints). |
| **15** | **Browser Security Basics** | سياسة المصدر نفسه (Same-Origin Policy - SOP)، مشاركة الموارد (CORS)، والحدود الأمنية الفاصلة بين العميل والسيرفر. |

---

### 🎯 المهارات التي ستخرج بها من هذا المستوى
- [x] فتح أي موقع وقراءة أكواد JavaScript المسؤولة عن تشغيل الواجهة وفهمها.
- [x] تتبع مسار بيانات المستخدم من الـ URL أو الفورم حتى وصولها لكود المعالجة.
- [x] إرسال طلبات الـ API واستقبال الردود ومعالجة حالات النجاح والفشل.
- [x] استخدام Browser DevTools باحترافية لفحص المتغيرات والأحداث والشبكة.
- [x] التمييز بين التحقق الشكلي في المتصفح والتحقق الأمني الإلزامي على السيرفر.
- [x] فهم أساسيات الـ DOM ومعرفة متى تكون طرق حقن البيانات خطرة أمنياً (مثل `innerHTML`).

---

### 🏆 مشروع نهاية المستوى: API Explorer
- **الوصف:** واجهة تفاعلية لعرض وإرسال طلبات HTTP المختلفة وتنسيق نتائج JSON والأخطاء، مع دعم بيانات تجريبية محلية (Mock Data) والتحكم في الـ Headers.
- 📁 **دليل المشروع بالعربية:** [`Final-Project/ar/README.md`](./Final-Project/ar/README.md)
- 📁 **English Project Guide:** [`Final-Project/en/README.md`](./Final-Project/en/README.md)

---

## 🇬🇧 Overview (English)

- **Goal:** Master real-world JavaScript in browser environments, DOM manipulation, asynchronous communication, and client-server boundaries.
- **Folders:**
  - `Lessons/`: Step-by-step guides in both [`/ar`](./Lessons/ar/) and [`/en`](./Lessons/en/).
  - `Practical-Applications/`: Interactive DOM and browser exercises.
  - `Final-Project/`: The **API Explorer** application.
