# 04 - الدوال المتقدمة: التركيب، التجزئة، وإدارة معدل التنفيذ (Advanced Functions)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/04-advanced-functions.md) | [العربية 🇸🇦](./04-advanced-functions.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تتيح أنماط البرمجة الوظيفية (Functional Programming) للمهندسين بناء أنظمة برمجية معقدة عبر تجميع وحدات برمجية صغيرة ونقية وقابلة لإعادة الاستخدام. في المعماريات المتقدمة، تُنظم الدوال داخل خطوط معالجة متسلسلة (Pipelines)، ويتم تخصيصها عبر التجزئة (Currying)، وضبط معدل تكرارها لحماية أداء المتصفح.

بنهاية هذا الدرس، ستتقن:
1. كتابة **الدوال النقية (Pure Functions)** والقضاء التام على الآثار الجانبية (Side Effects).
2. بناء خطوط تحويل ومعالجة البيانات المتسلسلة باستخدام **`pipe()`** و **`compose()`**.
3. تحويل الدوال متعددة المعاملات إلى دوال أحادية عبر **التجزئة (Currying)** والتطبيق الجزئي.
4. ميكانيكا وبناء دالة **تأخير التنفيذ حتى توقف النشاط (Debounce)**.
5. ميكانيكا وبناء دالة **تقييد وتحديد وتيرة التنفيذ (Throttle)**.

---

## 🧼 2. الدوال النقية (Pure Functions) وعدم التعديل (Immutability)

لكي تُصنف الدالة على أنها **دالة نقية (Pure Function)**، يجب أن تحقق شرطين صارمين:
1. **حتمية المخرجات (Deterministic):** عند إعطائها نفس المدخلات، ستُرجع **دائماً** نفس النتيجة دون أي تغيير.
2. **انعدام الآثار الجانبية (Zero Side Effects):** لا تعدل أي متغير خارج نطاقها، ولا تعدل المعاملات الممررة إليها، ولا تنفذ عمليات إدخال وإخراج ملحوظة (مثل طلبات الشبكة أو تعديل الـ DOM).

```javascript
// ❌ دالة غير نقية (Impure): تعدل كائناً خارجياً وتعتمد على حالة زمنية متغيرة
let minimumAge = 18;
function canVote(user) {
  user.checkedAt = Date.now(); // أثر جانبي: تعديل مباشر لبيانات المعامل!
  return user.age >= minimumAge; // غير نقية: تعتمد على متغير خارجي قابل للتغير!
}

// ✅ دالة نقية 100%: مكتفية بذاتها، حتمية، وخالية من الآثار الجانبية
function canVotePure(userAge, requiredAge = 18) {
  return userAge >= requiredAge;
}
```

---

## 🔗 3. تركيب الدوال وخطوط المعالجة (`pipe` و `compose`)

يقوم تركيب الدوال (Function Composition) بدمج دوال بسيطة ومفردة الهدف لبناء خط معالجة معقد يتدفق فيه الخرج من مرحلة لأخرى:

```
البيانات المدخلة  ---> [ دالة 1 ] ---> [ دالة 2 ] ---> [ دالة 3 ] ---> البيانات الناتجة
```

### بناء أداة `pipe()` الوظيفية:
تقوم `pipe()` بتمرير البيانات من **اليسار إلى اليمين** (وفق اتجاه القراءة الطبيعي):

```javascript
// أداة pipe عالية الأداء باستخدام Array.prototype.reduce:
const pipe = (...functions) => (initialValue) => {
  return functions.reduce((currentValue, currentFunction) => {
    return currentFunction(currentValue);
  }, initialValue);
};

// اللبنات البرمجية المفردة:
const trimString = (str) => str.trim();
const normalizeUsername = (str) => str.toLowerCase();
const addPrefix = (str) => `@${str}`;

// تركيب خط المعالجة الكامل:
const formatHandle = pipe(
  trimString,
  normalizeUsername,
  addPrefix
);

console.log(formatHandle('   ALEX_DEV99   ')); // "@alex_dev99"
```

---

## 🍛 4. التجزئة والتطبيق الجزئي (Currying & Partial Application)

**التجزئة (Currying)** هي تقنية تحويل دالة تقبل معاملات متعددة `fn(a, b, c)` إلى سلسلة من الدوال المتداخلة التي يقبل كل منها معاملاً واحداً فقط: `fn(a)(b)(c)`.

### تطبيق عملي: نظام تسجيل الأحداث القابل للتخصيص
```javascript
// دالة غير مجزأة:
// function log(level, prefix, message) { ... }

// الدالة بصيغة Currying:
const log = (level) => (prefix) => (message) => {
  console.log(`[${level}] [${prefix}]: ${message}`);
};

// تخصيص دوال مسبقة الإعداد عبر تثبيت المعاملات الأولى:
const errorLog = log('ERROR');
const authErrorLog = errorLog('AUTH_SERVICE');

// الاستدعاء النهائي بالرسالة فقط:
authErrorLog('توقيع التوكن غير صالح'); 
// الناتج: "[ERROR] [AUTH_SERVICE]: توقيع التوكن غير صالح"
```

### دالة التجزئة التلقائية العامة (Auto-Curry Utility):
```javascript
function curry(fn) {
  return function curried(...args) {
    // إذا اكتمل عدد المعاملات المطلوبة للدالة الأصلية، نفذها فوراً:
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    // وإلا، أرجع دالة تستقبل باقي المعاملات وتجمعها:
    return (...nextArgs) => curried.apply(this, [...args, ...nextArgs]);
  };
}

const addThreeNumbers = (a, b, c) => a + b + c;
const curriedAdd = curry(addThreeNumbers);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
```

---

## ⏱️ 5. إدارة معدل التنفيذ: الـ Debounce مقابل الـ Throttle

الأحداث عالية التردد (مثل التمرير `scroll`، وتغيير حجم النافذة `resize`، والكتابة `keyup`) تنطلق مئات المرات في الثانية، مما يتسبب في تجميد الصفحة إن لم تتم إدارتها:

```
سيل الأحداث المتسارع: |||||||||||||||||||||||||||||||| (أحداث مستمرة)

تقنية DEBOUNCE:                                    |   (تنطلق مرة واحدة بعد توقف المستخدم)
تقنية THROTTLE:     |       |       |       |       |   (تنطلق على فترات زمنية ثابتة ومحكومة)
```

### أ) تقنية Debounce: "انتظر حتى يتوقف المستخدم تماماً"
مثالية لـ: **حقول البحث الفوري التلقائي (Autocomplete)**، الحفظ التلقائي للنماذج، وإعادة حساب أبعاد الصفحة.

```javascript
function debounce(callback, delayMs = 300) {
  let timerId = null;

  return function (...args) {
    // إعادة ضبط وتصفير المؤقت مع كل ضغطة زر:
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      callback.apply(this, args);
    }, delayMs);
  };
}

// استخدامها مع حقل البحث:
const handleSearch = debounce((query) => {
  console.log('إرسال طلب البحث عبر الـ API لكلمة:', query);
}, 400);

// كتابة "JavaScript" ستطلق الدالة مرة واحدة فقط بعد 400ms من توقف المستخدم عن الكتابة!
```

### ب) تقنية Throttle: "لا تنفذ أكثر من مرة واحدة كل X ميلي ثانية"
مثالية لـ: **التحميل اللانهائي أثناء التمرير (Infinite Scroll)**، وعدادات شريط قراءة المقالات.

```javascript
function throttle(callback, limitMs = 250) {
  let inThrottle = false;

  return function (...args) {
    if (!inThrottle) {
      callback.apply(this, args);
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false;
      }, limitMs);
    }
  };
}

// استخدامها مع التمرير:
window.addEventListener('scroll', throttle(() => {
  console.log('فحص موضع التمرير لتحديث الواجهة...');
}, 200));
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: دالة غير نقية تعتمد على متغيرات بيئية خفية
let taxRate = 0.15;
function calculateInvoiceTotal(subtotal) {
  return subtotal + (subtotal * taxRate); // تعتمد على متغير خارجي!
}

// ✅ كود ممتاز: دالة نقية تقبل كافة العوامل كمعاملات افتراضية
function calculateInvoiceTotal(subtotal, taxRate = 0.15) {
  return subtotal + (subtotal * taxRate);
}

// ❌ كود سيئ: كتابة منطق المؤقتات يدوياً داخل معالجات الواجهة وتلويث النطاق
input.addEventListener('keyup', (e) => {
  clearTimeout(window.searchTimer);
  window.searchTimer = setTimeout(() => sendQuery(e.target.value), 300);
});

// ✅ كود ممتاز: استخدام دالة debounce نظيفة وموحدة
input.addEventListener('input', debounce((e) => sendQuery(e.target.value), 300));
```

---

## 📝 7. الخلاصة والدرس التالي
- **الدوال النقية (Pure Functions)** حتمية النتيجة وخالية من الآثار الجانبية.
- استخدم **`pipe()`** لتركيب خطوط معالجة وتحويل البيانات بسلاسة.
- تتيح **التجزئة (Currying)** تحويل الدوال متعددة المعاملات لدوال أحادية مسبقة التكوين.
- استخدم **`debounce()`** عندما تريد تأجيل التنفيذ حتى يتوقف المستخدم عن النشاط.
- استخدم **`throttle()`** لتقييد وتيرة تكرار العمليات المستمرة على فترات منتظمة.
- في الدرس التالي، سننتقل للبرمجة اللاتزامنية المتقدمة: **مجمعات الوعود، التحكم في مهلات الشبكة، وخطوط التدفق اللاتزامنية (Advanced Async)**.
