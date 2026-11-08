# 04 - دوال رد النداء والبرمجة اللاتزامنية التأسيسية (Callbacks & Concurrency)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/04-callbacks.md) | [العربية 🇸🇦](./04-callbacks.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في لغة جافا سكريبت، تُعامل الدوال كـ **"مواطنين من الدرجة الأولى (First-Class Citizens)"**: يمكن تخزينها في متغيرات، وتمريرها كوسائط لدوال أخرى، وإرجاعها كقيم من دوال أخرى.

الدالة التي يتم تمريرها إلى دالة أخرى ليتم استدعاؤها في وقت لاحق تُعرف باسم **دالة رد النداء (Callback Function)**. تشكل الـ Callbacks العمود الفقري التاريخي والمعماري لطبيعة جافا سكريبت غير المعطلة والقائمة على الأحداث (Event-Driven & Non-blocking).

بنهاية هذا الدرس، ستتقن:
1. آليات عمل **الدوال عالية الرتبة (Higher-Order Functions)** واستدعاء الـ Callbacks.
2. الفارق الجوهري بين دوال رد النداء **المتزامنة (Synchronous)** و **اللاتزامنية (Asynchronous)**.
3. إدارة التنفيذ المؤجل والمجدول باستخدام `setTimeout()` و `setInterval()`.
4. تطبيق نمط معالجة الأخطاء القياسي **(Error-First Callback Pattern)**.
5. تشخيص ومعالجة مشكلة التداخل الكارثي الشهيرة المعروفة بـ **جحيم دوال رد النداء (Callback Hell / Pyramid of Doom)**.

---

## 🎭 2. الدوال ككائنات والدوال عالية الرتبة (Higher-Order Functions)

**الدالة عالية الرتبة** هي أي دالة تحقق أحد الشرطين:
- تقبل دالة أخرى كمعامل، أو
- تُرجع دالة كناتج استدعائها.

```javascript
// 1. تمرير دالة كمعامل لدالة أخرى:
function executeWithLogging(operationFn, a, b) {
  console.log(`[تسجيل] بدء العملية بالمعاملين: ${a}, ${b}`);
  const result = operationFn(a, b);
  console.log(`[تسجيل] النتيجة: ${result}`);
  return result;
}

const add = (x, y) => x + y;
const multiply = (x, y) => x * y;

executeWithLogging(add, 5, 10);      // يطبع السجلات ويرجع 15
executeWithLogging(multiply, 4, 3); // يطبع السجلات ويرجع 12
```

---

## ⏱️ 3. دوال رد النداء المتزامنة مقابل اللاتزامنية

### أ) دوال رد النداء المتزامنة (Synchronous Callbacks)
تُنفذ فوراً أثناء تشغيل الدالة الحاوية لها، وتعطل السطور التالية حتى تنتهي:
```javascript
console.log('الخطوة 1: البداية');

[1, 2, 3].forEach((num) => {
  console.log(`معالجة الرقم: ${num}`); // يُنفذ بشكل متزامن وفوري!
});

console.log('الخطوة 2: النهاية');
// ترتيب الإخراج: الخطوة 1 -> معالجة 1, 2, 3 -> الخطوة 2
```

### ب) دوال رد النداء اللاتزامنية (Asynchronous Callbacks)
تُجدول للتنفيذ في وقت لاحق بعد فراغ مكدس الاستدعاءات الرئيسي (Call Stack) واكتمال الحدث الخارجي (مؤقت زمني، شبكة، أو نقرة مستخدم):
```javascript
console.log('الخطوة 1: البداية');

// تأجيل التنفيذ لمدة ثانية واحدة (1000 ميلي ثانية):
setTimeout(() => {
  console.log('الخطوة 2: اكتمل المؤقت (لاتزامني)');
}, 1000);

console.log('الخطوة 3: نهاية السكربت المباشر');
// ترتيب الإخراج: الخطوة 1 -> الخطوة 3 -> الخطوة 2 (بعد ثانية واحدة)
```

> [!IMPORTANT]
> حتى لو حددت زمناً مقداره `0` ميلي ثانية (`setTimeout(fn, 0)`), فإن دالة رد النداء ستُنفذ **بشكل لاتزامني** فقط بعد انتهاء جميع الأسطر المتزامنة في السكربت بالكامل!

---

## 🔄 4. المؤقتات المجدولة: `setTimeout` و `setInterval`

توفر بيئة المتصفح و Node.js دوالاً مدمجة للتحكم في التوقيت:

### دالة `setTimeout(callback, delayMs, ...args)`
تُنفذ الدالة **مرة واحدة فقط** بعد انقضاء الوقت المحدد:
```javascript
const timerId = setTimeout((user) => {
  console.log(`انتهت جلسة المستخدم: ${user}`);
}, 3000, 'علي');

// إلغاء المؤقت قبل أن ينطلق:
clearTimeout(timerId);
```

### دالة `setInterval(callback, intervalMs, ...args)`
تُنفذ الدالة **بشكل متكرر ودوري** كل فترة زمنية حتى يتم إيقافها:
```javascript
let counter = 1;

const intervalId = setInterval(() => {
  console.log(`ثانية رقم ${counter}`);
  counter++;

  if (counter > 3) {
    clearInterval(intervalId); // ضروري جداً: يمنع الحلقات اللانهائية في الخلفية واستنزاف الذاكرة!
  }
}, 1000);
```

---

## 🛡️ 5. نمط معالجة الأخطاء أولاً (The Error-First Callback Pattern)

قبل ظهور الوعود (Promises)، اعتمدت بيئة Node.js وجافا سكريبت معياراً موحداً لإرجاع البيانات والأخطاء:
- **المعامل الأول** الممرر لدالة الـ Callback مخصص دائماً لكائن الخطأ `err` (أو `null` في حالة النجاح).
- **المعاملات التالية** تحتوي على البيانات المسترجعة في حالة النجاح.

```javascript
function fetchUserData(userId, callback) {
  // محاكاة جلب بيانات من قاعدة بيانات أو شبكة:
  setTimeout(() => {
    if (userId <= 0) {
      callback(new Error('معرف المستخدم غير صالح: يجب أن يكون رقماً موجباً'), null);
      return;
    }

    const mockUser = { id: userId, username: 'ahmed_dev' };
    callback(null, mockUser); // لا يوجد خطأ، نمرر null متبوعاً بالبيانات
  }, 500);
}

// استخدام الدالة:
fetchUserData(10, (err, user) => {
  if (err) {
    console.error('فشلت العملية:', err.message);
    return;
  }
  console.log('تم جلب المستخدم بنجاح:', user.username);
});
```

---

## 🌪️ 6. جحيم دوال رد النداء (Callback Hell / Pyramid of Doom)

عندما تعتمد عدة عمليات لاتزامنية متتالية على نتائج العمليات السابقة، تتداخل الدوال داخل بعضها أفقياً مشكلة "هرم الموت":

```javascript
// ❌ كود معقد وسيئ: تداخل عميق، صعوبة في تتبع الأخطاء، وهشاشة برمجية
getUser(1, function(err, user) {
  if (err) return handleError(err);
  getOrders(user.id, function(err, orders) {
    if (err) return handleError(err);
    getOrderDetails(orders[0].id, function(err, details) {
      if (err) return handleError(err);
      getShippingStatus(details.shippingId, function(err, status) {
        if (err) return handleError(err);
        console.log('الحالة النهائية للشحنة:', status);
      });
    });
  });
});
```

### كيف نعالج هذا التعقيد؟
1. **التقسيم إلى دوال مسماة (Modularization):** تفكيك الدوال المتداخلة إلى دوال مستقلة ذات أسماء معبرة.
2. **الحل الحديث:** في الدرس 12، سنتعلم كيف نستبدل هذا التداخل تماماً باستخدام **الوعود (Promises)** وصيغة **`async/await`** الأنيقة.

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: دوال مجهولة متداخلة تصعب قراءتها وتتبع أخطائها
setTimeout(function() {
  doTaskA(function() {
    doTaskB(function() {
      console.log('اكتملت المهام');
    });
  });
}, 1000);

// ✅ كود ممتاز: تفكيك الدوال إلى معالجات مسماة وقابلة للاختبار المستقل
function handleTaskB() {
  console.log('اكتملت المهام');
}

function handleTaskA() {
  doTaskB(handleTaskB);
}

setTimeout(() => doTaskA(handleTaskA), 1000);
```

---

## 📝 8. الخلاصة والدرس التالي
- الدوال يمكن **تمريرها كمعاملات** واستدعاؤها لاحقاً كـ **Callbacks**.
- دوال الـ Callback المتزامنة تعطل التنفيذ؛ بينما اللاتزامنية تعمل بعد فراغ الـ Call Stack.
- احرص دائماً على إلغاء المؤقتات الدورية بـ **`clearInterval()`** لتفادي تسريب الذاكرة (Memory Leaks).
- في الدوال التقليدية، افحص دائماً **المعامل الأول (`err`)** للتأكد من خلو العملية من الأخطاء.
- في الدرس التالي، سننتقل إلى بيئة المتصفح واكتشاف **كائن window، كائن document، والفرق بين محرك JS و Web APIs**.
