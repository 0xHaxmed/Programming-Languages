# Level 3 — Advanced | المستوى الثالث: العمق الداخلي والتحليل الأمني

> 🌐 **اللغة:** **العربية 🇸🇦** | [English Version (README.md)](./README.md)

---

## 🎯 الهدف التعليمي للمستوى الثالث

- **الهدف:** فهم السلوك الداخلي الدقيق لمحركات JavaScript، وتتبع وقراءة الأكواد المعقدة، والتحليل الأمني المتقدم قبل التخصص في اختبار الاختراق.
- **التركيز:** آليات التنفيذ والذاكرة، سلسلة الـ Prototypes، حلقة الأحداث Event Loop، ومراجعة الأكواد الكبيرة للبحث عن الثغرات والأنماط الخطرة.

---

## 🧭 مسار دروس المستوى الثالث

| # | المجال | الموضوعات والتفاصيل | الروابط المباشرة |
| :-: | :--- | :--- | :--- |
| **01** | **التنفيذ والنطاق** | البيئة المعجمية (Lexical Environment)، الإغلاقات (Closures)، وفهم الـ Hoisting المتقدم. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **02** | **كلمة `this`** | قواعد تحديد `this` الأربعة: الافتراضي، الضمني، الصريح (`call`, `apply`, `bind`)، والدوال السهمية. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **03** | **النماذج والوراثة** | كائن `__proto__` و `prototype`، سلسلة النماذج، والآلية البرمجية لثغرات Prototype Pollution. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **04** | **الدوال المتقدمة** | الدوال العليا (Higher-Order Functions)، الـ Currying، وتركيب الدوال (Function Composition). | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **05** | **التزامن المتقدم** | تسلسل الوعود (Promise Chaining)، انتشار ومعالجة الأخطاء، و `Promise.allSettled`. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **06** | **حلقة الأحداث Event Loop** | محرك المتصفح: الـ Call Stack، الـ Web APIs، طابور الـ Tasks و Microtasks وترتيب التنفيذ الدقيق. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **07** | **الكلاسات والوحدات** | الأصناف (Classes)، الوراثة، ونظام الوحدات الحديث (ES Modules: `import` / `export`). | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **08** | **تحليل الأخطاء** | قراءة وتحليل الـ Stack Traces المعقدة، كائنات الخطأ المخصصة، وتتبع حالات الانهيار. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **09** | **مراجعة الأكواد وتدفق البيانات** | قراءة المشاريع الكبيرة، تتبع مسارات تدفق البيانات (Data Flow)، وإعادة الهيكلة الآمنة. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **10** | **الاختبارات والشروط** | بناء اختبارات الوحدة (Unit Testing)، شروط التحقق الصارمة، وتغطية الحالات الشاذة (Edge Cases). | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **11** | **التعابير النمطية Regex** | التعابير النمطية المتقدمة، فحص المدخلات، وتجنب هجمات الحرمان من الخدمة ReDoS. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **12** | **حزم البناء و Source Maps** | التعامل مع الأكواد المضغوطة (Minified)، أدوات الحزم (Bundlers)، واستخدام ملفات Source Maps. | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |
| **13** | **البرمجة الواعية أمنياً** | التعامل الآمن مع نصوص وخصائص الـ DOM ومراجعة الـ APIs الحساسة (`eval`, `postMessage`). | [دليل الدروس](./Lessons/ar/README.md) / [English](./Lessons/en/README.md) |

---

## 🔍 معامل المراجعة والاختبار الأمني المتقدم
طور مهاراتك في التدقيق البرمجي:
- 👉 [معمل مراجعة الأكواد باللغة العربية](./Testing-and-Review/ar/README.md)
- 👉 [Level 3 Testing & Security Review (English)](./Testing-and-Review/en/README.md)

---

## 🏆 مشروع نهاية المستوى: JavaScript Security Analyzer
بناء وتشغيل أداة فحص كود ساكن (SAST) تكشف الأنماط البرمجية الخطرة:
- اكتشاف مسارات التنفيذ الخطرة (`eval`, `new Function`).
- فحص مستمعي رسائل `postMessage` غير المؤمنة.
- رصد محاولات التلاعب بالـ DOM وسلسلة الـ Prototype.
- 👉 [دليل وكود الأداة بالعربية](./Final-Project/ar/README.md)
- 👉 [English Project Guide & Engine](./Final-Project/en/README.md)

---

## 🎓 معيار التخرج النهائي من المسار
بإتمامك المستوى الثالث بنجاح، تكون قد أتممت **مسار JavaScript Core الأساسي بالكامل**، وأصبحت جاهزاً تماماً للبدء في مسار اختبار الاختراق واكتشاف الثغرات في تطبيقات الويب: **JavaScript for Web Pentesting**!
