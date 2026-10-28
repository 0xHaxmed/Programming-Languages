# Level 3 — Advanced | المستوى الثالث: العمق الداخلي والتحليل الأمني

> 🌐 **اللغة / Language:** [العربية 🇸🇦](#-نظرة-عامة) | [English 🇬🇧](#-overview-english)

---

## 🇸🇦 نظرة عامة

- **الهدف:** فهم السلوك الداخلي الدقيق لمحركات JavaScript، وتتبع وقراءة الأكواد المعقدة، والتهيؤ الأمني المتين قبل الانتقال لاختبار الاختراق المتقدم.
- **التركيز:** آليات التنفيذ، إدارة الذاكرة، الـ Prototype Chain، الـ Event Loop، ومراجعة الأكواد الكبيرة للبحث عن الثغرات والأنماط الخطرة.

### 📋 قائمة الموضوعات (Syllabus)

| # | المجال | الموضوعات والتفاصيل |
| :-: | :--- | :--- |
| **01** | **Execution & Scope** | البيئة المعجمية (Lexical Environment)، الإغلاقات (Closures)، والرفع (Hoisting) بشكل معمق. |
| **02** | **The `this` Keyword** | قواعد تحديد `this` الأربعة، والدوال المرتبطة: `call`, `apply`, `bind`. |
| **03** | **Prototypes & Inheritance** | سلسلة النماذج (Prototype Chain)، الوراثة، والآلية البرمجية لـ Prototype Pollution. |
| **04** | **Advanced Functions** | الدوال العليا (Higher-Order Functions)، تركيب الدوال (Composition)، Currying والبرمجة الوظيفية. |
| **05** | **Advanced Async** | تسلسل الوعود (Promise Chaining)، انتشار الأخطاء (Error Propagation)، و `Promise.allSettled`. |
| **06** | **Event Loop & Concurrency** | مكدس الاستدعاء (Call Stack)، الـ Web APIs، طابور الـ Tasks و Microtasks، وتوقيت التنفيذ. |
| **07** | **Classes & ES Modules** | الأصناف (Classes)، الوراثة الكلاسيكية، وتصدير واستيراد الوحدات (`import` / `export`). |
| **08** | **Error Analysis & Stack Traces** | تتبع مسارات الأخطاء (Stack Traces)، كائنات الخطأ المخصصة، وتحليل حالات الانهيار. |
| **09** | **Code Review & Flow** | قراءة المشاريع الكبيرة، إعادة الهيكلة (Refactoring)، وتتبع تدفق البيانات (Data Flow Analysis). |
| **10** | **Testing & Assertions** | كتابة اختبارات الوحدة (Unit Testing)، شروط التحقق (Assertions)، واختبار الحالات الشاذة (Edge Cases). |
| **11** | **Regex & Pattern Matching** | التعابير النمطية (Regular Expressions) المتقدمة ومعالجة النصوص وتجنب ثغرات ReDoS. |
| **12** | **Modern Bundling & Minification** | التعامل مع الأكواد المضغوطة (Minified)، حزم البناء (Bundlers)، واستخدام ملفات الـ Source Maps. |
| **13** | **Security-Aware Coding** | التعامل الآمن مع نصوص وخصائص الـ DOM والـ APIs الحساسة (`eval`, `Function`, `postMessage`). |

---

### 🎯 المهارات التي ستخرج بها من هذا المستوى
- [x] فهم سبب وصول دالة معينة لقيمة ما بدقة، حتى مع تشابك مسارات التنفيذ المعقدة.
- [x] تتبع Callbacks و Promises ومعرفة الترتيب الزمني الدقيق للـ Microtasks والـ Tasks.
- [x] قراءة كائن معقد وتتبع الـ Prototype Chain واكتشاف التعديلات المشبوهة على النماذج.
- [x] تحليل ملفات JavaScript الضخمة وتحديد الدوال والمسارات الحساسة للبيانات (Sinks & Sources).
- [x] كتابة اختبارات قوية والتفريق بين خطأ منطقي عادي (Bug) وثغرة أمنية محتملة (Vulnerability).
- [x] فهم الآليات البرمجية وراء الأنماط الخطرة (Unsafe Patterns) والقدرة على مراجعتها وتصحيحها.

---

### 🏆 مشروع نهاية المستوى: JavaScript Security Analyzer
- **الوصف:** أداة تحليل كود ساكن (Static Code Analysis Tool) تفحص ملفات JavaScript بحثاً عن أنماط أمنية مريبة (مثل استخدام `innerHTML` غير الآمن، `eval`، أو استقبال أحداث `message` بدون التحقق من `origin`)، وتعرض سطر الشبهة، وسبب الخطورة، والبديل الآمن المقترح، مع التفريق بين الشبهة وإثبات الثغرة.
- 📁 **دليل المشروع بالعربية:** [`Final-Project/ar/README.md`](./Final-Project/ar/README.md)
- 📁 **English Project Guide:** [`Final-Project/en/README.md`](./Final-Project/en/README.md)

---

## 🇬🇧 Overview (English)

- **Goal:** Deep-dive into JavaScript internal mechanics, execution contexts, prototype pollution primitives, event loop microtasks, and static code security auditing.
- **Folders:**
  - `Lessons/`: In-depth guides in both [`/ar`](./Lessons/ar/) and [`/en`](./Lessons/en/).
  - `Testing-and-Review/`: Code review worksheets, testing drills, and reverse analysis.
  - `Final-Project/`: The **JavaScript Security Analyzer** static auditing engine.
