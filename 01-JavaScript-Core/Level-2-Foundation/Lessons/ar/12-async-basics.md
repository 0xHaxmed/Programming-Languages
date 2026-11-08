# 12 - البرمجة اللاتزامنية التأسيسية: الوعود وصيغة async/await (Async Basics)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/12-async-basics.md) | [العربية 🇸🇦](./12-async-basics.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

جافا سكريبت هي لغة أحادية خيط المعالجة (Single-Threaded) تعتمد على دورة أحداث غير معطلة (Non-blocking Event Loop). التعامل مع العمليات التي تستغرق وقتاً طويلاً — مثل طلبات الشبكة، أو قراءة الملفات، أو المؤقتات — يتطلب نمط برمجة لاتزامني محكم.

بعد أن كانت تتم قديماً عبر دوال الـ Callbacks المتداخلة، تعتمد جافا سكريبت الحديثة على **الوعود (Promises)** والصياغة الأنيقة لـ **`async` و `await`** لكتابة أكواد لاتزامنية تُقرأ بسلاسة ووضوح مثل الأكواد المتزامنة العادية.

بنهاية هذا الدرس، ستتقن:
1. الحالات الثلاث لدورة حياة **الوعد (Promise States)**: `Pending` و `Fulfilled` و `Rejected`.
2. إنشاء وسلسلة الوعود باستخدام `.then()` و `.catch()` و `.finally()`.
3. كتابة أكواد لاتزامنية سهلة القراءة باستخدام الكلمتين المفتاحيتين `async` و `await`.
4. معالجة الأخطاء اللاتزامنية باحترافية عبر كتل `try...catch`.
5. مضاعفة الأداء: **التنفيذ التتابعي البطيء** مقابل **التنفيذ المتوازي السريع** بـ `Promise.all()` و `Promise.allSettled()`.

---

## ⏳ 2. ما هو الوعد (Promise)؟

**الوعد (Promise)** هو كائن في جافا سكريبت يمثل قيمة مستقبلية لعملية لاتزامنية لم تنتهِ بعد، إما بالنجاح أو بالفشل.

```
                     +---------------------------------------+
                     |                PROMISE                |
                     |         [ الحالة: قيد الانتظار ]       |
                     +-------------------+-------------------+
                                         |
                       +-----------------+-----------------+
                       |                                   |
                       v (اكتملت العملية بنجاح)            v (فشلت العملية)
             +--------------------+              +--------------------+
             | [ الحالة: مكتملة ] |              | [ الحالة: مرفوضة ] |
             | القيمة: البيانات   |              | القيمة: كائن الخطأ |
             +--------------------+              +--------------------+
                       |                                   |
                       v                                   v
                    .then()                             .catch()
```

### الحالات الثلاث للوعد (متبادلة ولا رجعة فيها):
1. **`Pending` (قيد الانتظار):** الحالة الابتدائية فور إنشاء الوعد.
2. **`Fulfilled` (مكتمل ومقبول):** نجحت العملية واستدعت الدالة `resolve(data)`.
3. **`Rejected` (مرفوض):** فشلت العملية واستدعت الدالة `reject(error)`.

---

## 🛠️ 3. إنشاء واستخدام الوعود (Promises)

### إنشاء وعد مخصص:
```javascript
function delay(ms) {
  return new Promise((resolve, reject) => {
    if (ms < 0) {
      reject(new Error('يجب أن تكون المدة رقماً موجباً'));
      return;
    }
    setTimeout(() => {
      resolve(`تم الانتظار بنجاح لمدة ${ms} ميلي ثانية`);
    }, ms);
  });
}
```

### الاستهلاك باستخدام `.then()`, `.catch()`, `.finally()`:
```javascript
delay(1000)
  .then(message => {
    console.log('نجاح:', message);
    return delay(500); // إرجاع وعد جديد يتيح التسلسل النظيف!
  })
  .then(secondMessage => {
    console.log('اكتملت الخطوة الثانية:', secondMessage);
  })
  .catch(error => {
    console.error('تم اصطياد خطأ في أي مرحلة من السلسلة:', error.message);
  })
  .finally(() => {
    console.log('انتهى التنفيذ (يعمل دائماً سواء نجحت العملية أو فشلت)');
  });
```

---

## ✨ 4. الصياغة الحديثة: `async` و `await`

قُدمت صيغة `async/await` في معيار ES2017 كغلاف نحوي أنيق (Syntactic Sugar) فوق الوعود، مما يقضي تماماً على سلاسل `.then()` المتداخلة.

### 1. الكلمة المفتاحية `async`:
توضع قبل الإعلان عن أي دالة. تضمن أن الدالة **تُرجع وعداً (Promise) دائماً**:
```javascript
async function getStatus() {
  return 'النظام يعمل بكفاءة'; // يُغلف تلقائياً بـ Promise.resolve()
}
getStatus().then(console.log); // "النظام يعمل بكفاءة"
```

### 2. الكلمة المفتاحية `await`:
لا يمكن استخدامها إلا داخل دالة معرّفة بـ `async`. تقوم بإيقاف تنفيذ هذه الدالة مؤقتاً حتى يستقر الوعد، ثم تُرجع القيمة الناتجة مباشرة:
```javascript
async function runSequence() {
  try {
    console.log('بدء العمليات...');
    const result1 = await delay(1000);
    console.log(result1);

    const result2 = await delay(500);
    console.log(result2);

    console.log('اكتملت جميع العمليات المتتالية!');
  } catch (error) {
    console.error('تم اصطياد الخطأ عبر try/catch:', error.message);
  } finally {
    console.log('تنظيف الموارد وإغلاق الاتصالات');
  }
}

runSequence();
```

---

## 🚀 5. الأداء: التنفيذ التتابعي مقابل التنفيذ المتوازي

من أخطاء المطورين الشائعة استخدام `await` تتابعياً لعمليات مستقلة لا تعتمد على بعضها، مما يضاعف وقت التحميل دون داعٍ:

### أ) الأسلوب البطيء: التنفيذ التتابعي (Waterfall)
```javascript
// ❌ بطيء جداً: كل طلب ينتظر انتهاء الطلب السابق له بالكامل
async function loadDashboardSlow() {
  const user = await fetch('/api/user');       // يستغرق ثانية
  const feed = await fetch('/api/feed');       // يستغرق ثانية
  const metrics = await fetch('/api/metrics'); // يستغرق ثانية
  // إجمالي الوقت المستغرق: 3 ثوانٍ كاملة!
}
```

### ب) الأسلوب السريع: التنفيذ المتوازي عبر `Promise.all()`
إذا كانت الطلبات مستقلة تماماً، أطلقها جميعاً في نفس اللحظة:
```javascript
// ✅ سريع وفائق الكفاءة: انطلاق جميع الطلبات دفعة واحدة في نفس الوقت!
async function loadDashboardFast() {
  const [userRes, feedRes, metricsRes] = await Promise.all([
    fetch('/api/user'),
    fetch('/api/feed'),
    fetch('/api/metrics')
  ]);
  // إجمالي الوقت المستغرق: ثانية واحدة فقط (مدة أطول طلب منفرد)!
}
```

> [!WARNING]
> **قاعدة فشل `Promise.all` الفوري:**  
> إذا فشل **أي طلب واحد**، تفشل الدالة `Promise.all` فوراً وترمي خطأ، متجاهلة باقي الطلبات الناجحة.  
> إذا كنت بحاجة لجمع كل النتائج حتى لو فشل بعضها، استخدم **`Promise.allSettled()`**:

```javascript
const results = await Promise.allSettled([
  fetch('/api/main-service'),
  fetch('/api/optional-widget')
]);

results.forEach(res => {
  if (res.status === 'fulfilled') {
    console.log('قيمة الطلب الناجح:', res.value);
  } else {
    console.warn('سبب فشل الطلب:', res.reason);
  }
});
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: خلط سلاسل .then() مع async/await في نفس المكان
async function getUserData() {
  const user = await fetchUser().then(res => res.json()); // أسلوب مربك وغير متناسق
  return user;
}

// ✅ كود ممتاز: تناسق تام باستخدام async/await
async function getUserData() {
  const response = await fetchUser();
  const user = await response.json();
  return user;
}

// ❌ كود سيئ: نسيان try/catch حول استدعاءات await (يؤدي لـ Uncaught Promise Rejection)
async function deleteItem(id) {
  const res = await apiDelete(id); // ينهار السكربت عند انقطاع الشبكة!
}

// ✅ كود ممتاز: حماية العملية بحاجز أخطاء واضح
async function deleteItem(id) {
  try {
    const res = await apiDelete(id);
    return res;
  } catch (err) {
    logger.error('فشل حذف العنصر:', { id, error: err });
    throw err;
  }
}
```

---

## 📝 7. الخلاصة والدرس التالي
- يمتلك **الوعد (Promise)** ثلاث حالات: `Pending`, `Fulfilled`, و `Rejected`.
- الكلمة **`async`** تحول الدالة لترجع وعداً؛ والكلمة **`await`** تستخرج القيمة المحلولة للوعد وتنتظر انتهائه.
- غلف استدعاءات `await` دائماً بـ **`try...catch`** للتعامل مع حالات الفشل بمرونة.
- استخدم **`Promise.all()`** لتشغيل المهام اللاتزامنية المستقلة بالتوازي لمضاعفة سرعة التطبيق.
- استخدم **`Promise.allSettled()`** عندما تريد استقبال كل النتائج مع أسباب الفشل.
- في الدرس التالي، سنشرح آليات حفظ البيانات داخل المتصفح: **التخزين المحلي وملفات الكوكيز (Browser Storage & Cookies)**.
