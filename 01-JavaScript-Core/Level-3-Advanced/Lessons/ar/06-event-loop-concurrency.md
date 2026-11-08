# 06 - حلقة الأحداث والتزامن: Microtasks و Macrotasks وظاهرة التجمد (Starvation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/06-event-loop-concurrency.md) | [العربية 🇸🇦](./06-event-loop-concurrency.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تُعرف لغة جافاسكريبت بأنها لغة ذات **خيط معالجة واحد (Single-Threaded)**، غير حاجزة (Non-Blocking)، وتدعم البرمجة المتزامنة غير المتزامنة (Concurrent Asynchronous). على الرغم من أن محرك جافاسكريبت لا يمكنه تنفيذ سوى تعليمة برمجية واحدة في اللحظة الواحدة على مكدس الاستدعاءات (**Call Stack**)، فإن بيئة التشغيل المحيطة به (المتصفح أو Node.js) تدير التزامن ببراعة من خلال **حلقة الأحداث (Event Loop)**.

بنهاية هذا الدرس، ستتقن تماماً:
1. البنية المعمارية الداخلية لمحرك جافاسكريبت: **Call Stack**، **Memory Heap**، واجهات الخلفية **Web APIs / libuv**، طابور **Microtasks**، وطابور **Macrotasks**.
2. الترتيب الزمني الصارم لدورة حلقة الأحداث الواحدة (Event Loop Tick).
3. الفروقات الجوهرية بين **Microtasks** (`Promise`, `queueMicrotask`, `MutationObserver`) و **Macrotasks** (`setTimeout`, `setInterval`, I/O).
4. تشخيص ومنع ظاهرة تجمد الشاشة وحرمان النظام من الموارد (**Microtask Starvation**).
5. تجزئة المهام الحسابية الثقيلة وتمرير التحكم للمتصفح (Yielding) باستخدام تقطيع الوقت وواجهة **`scheduler.yield()`** الحديثة.

---

## 🏗️ 2. البنية المعمارية لحلقة الأحداث (Event Loop Architecture)

تتألف بيئة تشغيل جافاسكريبت من مكونات متعاونة ومنظمة بدقة:

```
+-------------------------------------------------------------------------+
|                         محرك جافاسكريبت (V8 Engine)                     |
|                                                                         |
|   +-----------------------+               +-----------------------+     |
|   | كومة الذاكرة (Heap)   |               | مكدس الاستدعاء (Stack)|     |
|   | (حجز الكائنات والبيانات) |               |  (تنفيذ الدوال الحالية) |     |
|   +-----------------------+               +-----------+-----------+     |
+-------------------------------------------------------|-----------------+
                                                        |
                                                        v
+------------------------------------+      +-----------------------------+
|    واجهات البيئة المضيفة في الخلفية |      |     بوابة حلقة الأحداث      |
| (المؤقتات، طلبات Fetch، أحداث DOM)   |      |        (Event Loop)         |
+-----------------+------------------+      +--------------+--------------+
                  |                                        |
                  v                                        v
+------------------------------------+      +-----------------------------+
|       طابور الـ Microtasks         |      |    طابور المهام (Macrotasks)|
| (Promise.then, queueMicrotask)     |      | (setTimeout, setInterval,   |
| * يتم تفريغه بالكامل في كل دورة!     |      |  أحداث واجهة المستخدم النقرات)  |
+------------------------------------+      +-----------------------------+
```

### الترتيب الزمني لتنفيذ الدورة الواحدة (The Event Loop Tick):
1. **تنفيذ الكود التزامني (Synchronous):** يعمل المكدس `Call Stack` حتى يفرغ تماماً من كل الدوال المتزامنة.
2. **تفريغ طابور الـ Microtasks بالكامل:** بمجرد فراغ المكدس، ينفذ المحرك أول مهمة من طابور Microtask، ويستمر في تفريغ الطابور **حتى يصبح فارغاً تماماً بنسبة 0%**. (نقطة حرجة: لو قامت Microtask بإضافة Microtask أخرى، فستعمل في **نفس** الدورة قبل أي رسم للشاشة!).
3. **مرحلة تحديث ورسم الواجهة (Browser Render Pipeline):** يقوم المتصفح بحساب التخطيط (Layout)، الأنماط (Style)، وإعادة رسم الشاشة (Paint) إذا لزم الأمر.
4. **تنفيذ مهمة Macrotask واحدة فقط:** يسحب المحرك **مهمة واحدة فقط** من طابور Macrotask وينفذها على المكدس.
5. **التكرار:** يعود المحرك فوراً إلى الخطوة 2 لتفريغ الـ Microtasks التي قد تكون نتجت عن الخطوة 4.

---

## 🔬 3. مقارنة عملية: تتبع مسار التنفيذ خطوة بخطوة

تأمل هذا اللغز البرمجي الشهير في المقابلات التقنية. هل تستطيع توقع ترتيب طباعة الرسائل في الـ Console بدقة؟

```javascript
console.log('1: Synchronous start');

setTimeout(() => {
  console.log('2: Macrotask (setTimeout 0)');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('3: Microtask 1 (Promise.then)');
  })
  .then(() => {
    console.log('4: Microtask 2 (Chained Promise.then)');
  });

queueMicrotask(() => {
  console.log('5: Microtask 3 (queueMicrotask)');
});

console.log('6: Synchronous end');
```

### تحليل التتبع الزمني (Trace Breakdown):
1. `console.log('1: Synchronous start')` تُنفذ فوراً على الـ Call Stack. -> **النتيجة: `1`**
2. `setTimeout(..., 0)` تسجل مؤقتاً في خلفية المتصفح. وعند انقضاء 0ms تُدرج الدالة داخل طابور **Macrotask Queue**.
3. `Promise.resolve().then(...)` تُدرج دالتها مباشرة في طابور **Microtask Queue**.
4. `queueMicrotask(...)` تُدرج دالتها مباشرة في طابور **Microtask Queue**.
5. `console.log('6: Synchronous end')` تُنفذ فوراً على الـ Call Stack. -> **النتيجة: `6`**
6. *المكدس فارغ الآن!* حان وقت تفريغ طابور **Microtasks**:
   - أول Microtask تُطبع `3: Microtask 1`، وتُرجع undefined مما يُدرج الـ `.then()` المتسلسلة التابعة لها داخل طابور Microtask. -> **النتيجة: `3`**
   - الـ Microtask التالية في الطابور تُطبع `5: Microtask 3`. -> **النتيجة: `5`**
   - الـ Microtask المتسلسلة تُطبع `4: Microtask 2`. -> **النتيجة: `4`**
7. *طابور الـ Microtasks فارغ تماماً الآن!* المتصفح يحدث الشاشة إن تطلب الأمر.
8. يسحب المحرك **مهمة واحدة فقط** من طابور الـ Macrotask:
   - دالة `setTimeout` تعمل وتطبع `2: Macrotask`. -> **النتيجة: `2`**

**المخرجات النهائية بالترتيب الصارم:**
```
1: Synchronous start
6: Synchronous end
3: Microtask 1 (Promise.then)
5: Microtask 3 (queueMicrotask)
4: Microtask 2 (Chained Promise.then)
2: Macrotask (setTimeout 0)
```

---

## 🛑 4. ظاهرة تجمد النظام (Microtask Starvation)

نظراً لأن حلقة الأحداث **تصر على تفريغ كل الـ Microtasks** حتى آخر واحدة قبل الانتقال لرسم الشاشة أو معالجة نقرات الفأرة، فإن إنشاء سلسلة لانهائية أو استدعاء تكراري (Recursive) داخل Microtask سيؤدي إلى **شلل تام في المتصفح (Starvation)**.

تتوقف الصفحة تماماً عن الاستجابة، وتتوقف الرسوم المتحركة، ويصبح المتصفح غير قادر على استقبال أي نقرة من المستخدم!

```javascript
// ⚠️ خطر فادح: Microtask Starvation (يجمد الصفحة ويمنع المتصفح من العمل)
function freezeTheBrowser() {
  queueMicrotask(() => {
    // يضيف مهمة microtask جديدة قبل أن تنتهي الدورة الحالية أبداً!
    freezeTheBrowser();
  });
}

// بالمقارنة مع التكرار عبر Macrotask (لا يجمد المتصفح إطلاقاً!):
function safeMacrotaskLoop() {
  setTimeout(() => {
    // المتصفح يقوم برسم الشاشة واستقبال نقرات المستخدم بين كل دورة وأخرى!
    safeMacrotaskLoop();
  }, 0);
}
```

> **قاعدة ذهبية:** لا تستخدم دوال تكرارية لا نهائية داخل الوعود (Promises) أو `queueMicrotask` دون شرط توقف حاسم.

---

## ⏱️ 5. المهام الطويلة (Long Tasks) وإتاحة وقت للمتصفح (Yielding)

تُعرف المعايير القياسية لـ Web Vitals **المهمة الطويلة (Long Task)** بأنها أي عملية تحتل خيط المعالجة الرئيسي لمدة تزيد عن **50 مللي ثانية**. تؤدي المهام الطويلة إلى تدهور تقييم **Interaction to Next Paint (INP)** وشعور المستخدم بثقل وبطء في الاستجابة.

### المشكلة: المعالجة الحسابية الكتلية المباشرة
```javascript
// يجمد الخيط الرئيسي لعدة ثوانٍ متواصلة:
function processOneMillionRecords(records) {
  for (let i = 0; i < records.length; i++) {
    heavyMathComputation(records[i]);
  }
}
```

### الحل: تقسيم العمل وتمرير التحكم للمتصفح (Cooperative Multitasking)

نقوم بتقسيم المعالجة إلى فترات زمنية محددة، ثم نترك المجال لحلقة الأحداث لترسم الشاشة وتلتقط النقرات، ثم نكمل العمل.

#### المعيار الحديث: `scheduler.yield()` (Chrome 129+ / مسودة W3C القياسية)
```javascript
async function processRecordsWithYield(records) {
  for (let i = 0; i < records.length; i++) {
    heavyMathComputation(records[i]);

    // التنازل عن الخيط الرئيسي كل 50 عملية:
    if (i % 50 === 0) {
      if ('scheduler' in window && 'yield' in window.scheduler) {
        await window.scheduler.yield(); // أحدث واجهة برمجية معيارية
      } else {
        // بديل شامل يعمل على كافة المتصفحات القديمة:
        await new Promise((resolve) => setTimeout(resolve, 0));
      }
    }
  }
}
```

#### دالة تقسيم العمل باحترافية وفق الميزانية الزمنية (Time-Slicing Utility):
```javascript
async function timeSlicedTask(items, processor, maxExecutionChunkMs = 16) {
  let index = 0;

  while (index < items.length) {
    const chunkStart = performance.now();

    // استمر في العمل طالما لم نتجاوز ميزانية الـ 16ms (معدل 60 إطاراً في الثانية):
    while (index < items.length && (performance.now() - chunkStart) < maxExecutionChunkMs) {
      processor(items[index]);
      index++;
    }

    // إتاحة فرصة للمتصفح لرسم الإطار ومعالجة النقرات:
    if (index < items.length) {
      await new Promise((resolve) => setTimeout(resolve, 0));
    }
  }
}
```

---

## 🧼 6. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: استخدام setTimeout(fn, 0) عندما ترغب في مزامنة فورية عالية الأولوية
function notifyObservers(data) {
  setTimeout(() => {
    // بطيء نسبياً: يُلقى خلف أحداث الواجهة ومهام Macrotasks الأخرى
    listeners.forEach((fn) => fn(data));
  }, 0);
}

// ✅ جيد: استخدام queueMicrotask لمزامنة الحالة فور انتهاء الدالة وقبل رسم الشاشة
function notifyObservers(data) {
  queueMicrotask(() => {
    listeners.forEach((fn) => fn(data));
  });
}

// ❌ سيء: تشغيل عمليات ثقيلة مباشرة داخل مستمع النقر
button.addEventListener('click', () => {
  const result = computeMassiveDataset(); // تجميد الشاشة لـ 800ms فور النقر
  renderResult(result);
});

// ✅ جيد: إظهار مؤشر التحميل ونقل الحساب الثقيل إلى Web Worker
button.addEventListener('click', async () => {
  showSpinner();
  const result = await runInWorker('computeWorker.js');
  renderResult(result);
  hideSpinner();
});
```

---

## 📝 7. ملخص الدرس والخطوة القادمة

- محرك جافاسكريبت يعمل بخيط واحد، وتتم إدارة التزامن بواسطة **حلقة الأحداث (Event Loop)**.
- الكود **التزامني** يُنفذ أولاً مباشرة على **Call Stack**.
- عند فراغ المكدس، يتم تفريغ **جميع مهام Microtasks** بنسبة 100% حتى ينتهي الطابور.
- يقوم المتصفح بمرحلة **تحديث ورسم الشاشة (Rendering)** بين الدورات.
- مهام **Macrotasks** تُنفذ **واحدة تلو الأخرى** في كل دورة.
- تكرار الـ Microtasks بلا توقف يؤدي لظاهرة **Microtask Starvation** وتجميد الصفحة.
- قسّم العمليات الحسابية الضخمة باستخدام **`scheduler.yield()`** أو تقطيع الوقت للحفاظ على تجاوب التطبيق.
- في الدرس القادم، سنتعمق في **الفئات البرمجية (Classes) والموديلات الحديثة (ES Modules): التغليف والحقول الخاصة `#` وميكانيكية عمل ESM**.
