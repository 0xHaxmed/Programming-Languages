# قسم مراجعة الكود والاختبارات المتقدمة (Level 3 Testing & Code Review)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./README.md) | [English 🇬🇧](../en/README.md)

---

## 🔍 أهداف قسم المراجعة والاختبار

يهدف هذا القسم لتدريبك على قراءة مشاريع جافا سكريبت الكبيرة، وبناء اختبارات دقيقة، والتمييز بين الخلل المنطقي العادي (Functional Bug) والمشكلة التي يمكن استغلالها كثغرة أمنية (Security Vulnerability):

### 1. تحليل تدفق البيانات وتتبع مسار المدخلات (Data Flow & Sink Tracing)
- كيف تتعقب مسار مدخلات المستخدم بدءاً من الـ Sources (مثل `location.search` أو `window.addEventListener("message")`) وحتى وصولها إلى الـ Execution Sinks (مثل `element.innerHTML` أو `eval`).

### 2. مراجعة واستكشاف ثغرات الـ Prototype Pollution برمجياً
- فهم الآلية البرمجية لكيفية وصول دمج الكائنات غير الآمن (Recursive Object Merge) إلى تعديل خصائص كائن `Object.prototype`، وكيف يؤثر ذلك على كائنات البرنامج ككل.

### 3. تدريبات كتابة اختبارات الحالات غير المتوقعة (Edge Case Testing)
- كتابة اختبارات تتحقق من قيم `null`, `undefined`, الأرقام السالبة، النصوص الطويلة جداً، والكائنات غير المتوقعة للتأكد من صمود الكود البرمجي في أسوأ السيناريوهات.

---

⬅️ [دروس المستوى الثالث](../../Lessons/ar/README.md) | 🏆 [الانتقال للمشروع النهائي: JavaScript Security Analyzer](../../Final-Project/ar/README.md)
