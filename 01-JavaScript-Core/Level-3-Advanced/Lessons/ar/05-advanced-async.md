# 05 - البرمجة غير المتزامنة المتقدمة: المجمعات، المهل الزمنية و AbortController

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/05-advanced-async.md) | [العربية 🇸🇦](./05-advanced-async.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في الأنظمة البرمجية الواقعية والمشاريع الإنتاجية (Enterprise Applications)، تتجاوز البرمجة غير المتزامنة مجرد استخدام `async/await` أو استدعاء `fetch()` عادي. تتطلب الأنظمة المتقدمة إدارة دقيقة للمهام المتزامنة، فرض مهل زمنية صارمة (Timeouts)، إلغاء الطلبات غير المرغوب فيها لتوفير الموارد، وتطبيق خوارزميات إعادة المحاولة المقاومة للفشل (Resilient Retries).

بنهاية هذا الدرس، ستتقن تماماً:
1. الفروقات التشغيلية الدقيقة بين مجمعات الوعود الأربعة: **`Promise.all`**, **`Promise.allSettled`**, **`Promise.race`**, و **`Promise.any`**.
2. إلغاء العمليات غير المتزامنة وطلبات الشبكة الجارية بأمان عبر **`AbortController`** و **`AbortSignal`**.
3. بناء خوارزمية احترافية لإعادة المحاولة تعتمد على **Exponential Backoff مع Jitter**.
4. فرض مهل زمنية (Timeouts) على الطلبات دون تسريب للذاكرة.
5. معالجة تدفقات البيانات الضخمة أو الموزعة على دفعات باستخدام **Async Generators** مع **`for await...of`**.

---

## 🔀 2. مقارنة مجمعات الوعود (Promise Combinators)

توفر لغة JavaScript أربع دوال ساكنة (Static Methods) داخل الكائن `Promise` لإدارة مجموعات من الوعود المتزامنة:

| الدالة (Method) | شرط النجاح (Fulfillment) | شرط الفشل (Rejection) | الفشل الفوري؟ (Fail-Fast) | أبرز حالات الاستخدام |
| :--- | :--- | :--- | :--- | :--- |
| **`Promise.all`** | تنجح **جميع** الوعود بلا استثناء. | يفشل **أي وعد واحد** فقط. | **نعم (Fail-Fast)** | جلب بيانات مترابطة إلزامية معاً (مثل: ملف المستخدم وإعدادات حسابه). |
| **`Promise.allSettled`** | تنتهي **جميع** الوعود (سواء بنجاح أو فشل). | **لا تفشل أبداً** (دائماً تُرجع مصفوفة بالنتائج). | **لا** | العمليات المجمعة، لوحات التحكم (Dashboards)، إرسال القياسات (Telemetry). |
| **`Promise.race`** | **أول** وعد ينتهي (سواء بنجاح أو فشل). | **أول** وعد ينتهي (سواء بنجاح أو فشل). | **نعم** | تطبيق المهل الزمنية، اختبار أسرع استجابة بين خادمين. |
| **`Promise.any`** | **أول** وعد **ينجح** (تتجاهل الفاشل حتى تنجح واحدة). | تفشل **جميع** الوعود (تُرجع `AggregateError`). | **لا** (تتجاهل الفشل حتى يفشل الجميع) | التوجيه للخوادم البديلة (Mirrors / CDNs) عند تعطل الخادم الرئيسي. |

### مخطط تدفق العمليات (Visual Comparison):

```
Promise.all([P1, P2, P3])
P1: ----✓ (200ms)
P2: ---------✗ (400ms) ----> يفشل فوراً عند 400ms وتُهمل نتيجة P3
P3: --------------✓ (700ms)

Promise.allSettled([P1, P2, P3])
P1: ----✓ (200ms)
P2: ---------✗ (400ms)
P3: --------------✓ (700ms) ----> ينجح دائماً عند 700ms مع تفاصيل حالة كل عملية

Promise.any([P1, P2, P3])
P1: ----✗ (200ms - يُتجاهل الفشل)
P2: ---------✓ (400ms) ---------> ينجح فوراً عند 400ms بقيمة P2 دون انتظار P3!
P3: --------------✓ (700ms)
```

### أمثلة عملية برمجية:

```javascript
const fetchProfile = fetch('/api/profile').then(r => r.json());
const fetchOrders = fetch('/api/orders').then(r => r.json());
const fetchRecommendations = fetch('/api/recommended').then(r => r.json());

// 1. استخدام Promise.all للبيانات الضرورية الحساسة:
async function loadCriticalDashboard() {
  try {
    const [profile, orders] = await Promise.all([fetchProfile, fetchOrders]);
    console.log('تم تحميل بيانات المستخدم والطلبات:', profile, orders);
  } catch (error) {
    console.error('فشل تحميل البيانات الأساسية، لن يتم عرض الصفحة:', error);
  }
}

// 2. استخدام Promise.allSettled للواجهات المرنة التي تتحمل الفشل الجزئي:
async function loadResilientWidgets() {
  const results = await Promise.allSettled([
    fetchProfile,
    fetchOrders,
    fetchRecommendations
  ]);

  results.forEach((result, idx) => {
    if (result.status === 'fulfilled') {
      console.log(`عنصر الواجهة ${idx} تم بنجاح:`, result.value);
    } else {
      console.warn(`عنصر الواجهة ${idx} تعذر تحميله:`, result.reason);
    }
  });
}

// 3. استخدام Promise.any للخوادم المتطابقة والبديلة (Failover):
async function fetchConfigFromMirrors(mirrorUrls) {
  const requests = mirrorUrls.map(url =>
    fetch(url).then(res => {
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return res.json();
    })
  );

  try {
    const config = await Promise.any(requests);
    return config;
  } catch (aggregateError) {
    console.error('فشلت جميع خوادم التكرار:', aggregateError.errors);
    throw new Error('خدمة الإعدادات غير متاحة حالياً عبر جميع المناطق.');
  }
}
```

---

## 🛑 3. إلغاء المهام باستخدام `AbortController`

قبل اعتماد واجهة `AbortController` كمعيار قياسي، كان إلغاء العمليات غير المتزامنة يتم بطرق ملتوية تؤدي لتسريب الذاكرة (Memory Leaks) واستمرار الطلبات في استهلاك موارد الشبكة. اليوم أصبحت مدعومة في المتصفحات الحديثة، بيئة Node.js (من v15+) وبيئة Deno.

### تشريح `AbortController`:
- `const controller = new AbortController();`
- `controller.signal`: كائن من نوع `AbortSignal` يُمرر للعمليات المطلوب مراقبتها.
- `controller.abort(reason)`: يُطلق حدث الإلغاء ويجعل قيمة `signal.aborted = true`.

### إلغاء طلبات الشبكة (Fetch Cancellation):

```javascript
async function searchProducts(query, signal) {
  const url = `/api/search?q=${encodeURIComponent(query)}`;
  const response = await fetch(url, { signal });
  
  if (!response.ok) {
    throw new Error(`خطأ بالبحث: ${response.status}`);
  }
  return response.json();
}

// الاستخدام في صندوق البحث الحي (Autocomplete Search):
let activeController = null;

async function onUserTyping(searchTerm) {
  // إلغاء الطلب السابق فوراً إذا كان المستخدم ما زال يكتب:
  if (activeController) {
    activeController.abort('تم إدخال حرف جديد من المستخدم');
  }

  activeController = new AbortController();

  try {
    const results = await searchProducts(searchTerm, activeController.signal);
    renderSearchResults(results);
  } catch (error) {
    if (error.name === 'AbortError') {
      console.log('تم إلغاء طلب البحث السابق بأمان لتوفير الباندويث.');
    } else {
      console.error('حدث خطأ فعلي أثناء البحث:', error);
    }
  }
}
```

### فرض مهلة زمنية تلقائية (Enforced Timeouts):
في المعايير الحديثة، توفر جافاسكريبت `AbortSignal.timeout(ms)` لإلغاء أي عملية تلقائياً بعد انقضاء مدة زمنية محددة:

```javascript
async function fetchWithTimeout(url, timeoutMs = 5000) {
  try {
    const response = await fetch(url, {
      signal: AbortSignal.timeout(timeoutMs) // إلغاء تلقائي بعد انتهاء المهلة
    });
    return await response.json();
  } catch (error) {
    if (error.name === 'TimeoutError') {
      throw new Error(`انتهت المهلة المحددة للطلب (${timeoutMs}ms) إلى ${url}`);
    }
    throw error;
  }
}
```

---

## 🔁 4. خوارزمية إعادة المحاولة مع Exponential Backoff و Jitter

عند الاتصال بالخوادم عبر شبكات موزعة، لا مفر من حدوث أخطاء مؤقتة (مثل انقطاع طفيف، أو HTTP 503، أو تجاوز معدل الطلبات 429 Too Many Requests). إعادة المحاولة الفورية الغبية من آلاف المستخدمين في نفس اللحظة تؤدي لانهيار الخادم بالكامل فيما يعرف بمشكلة **Thundering Herd**.

الحل الاحترافي يعتمد على 3 عناصر:
1. **Exponential Backoff:** مضاعفة وقت الانتظار بعد كل محاولة فاشلة (`delay = baseDelay * 2^attempt`).
2. **Full Jitter:** إضافة قيمة عشوائية لوقت الانتظار لتفريق أوقات وصول طلبات العملاء.
3. **تصنيف الأخطاء (Idempotency):** إعادة المحاولة فقط للأخطاء العابرة، وتجنب إعادة محاولة الأخطاء الدائمة مثل 400 Bad Request أو 401 Unauthorized.

```javascript
const wait = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function retryWithBackoff(
  asyncFn,
  {
    maxRetries = 4,
    baseDelayMs = 300,
    maxDelayMs = 5000,
    shouldRetry = (err) => true
  } = {}
) {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await asyncFn();
    } catch (error) {
      const isLastAttempt = attempt === maxRetries;
      if (isLastAttempt || !shouldRetry(error)) {
        throw error; // إذا استنفذت المحاولات أو كان الخطأ غير قابل للإعادة، ارمِ الخطأ
      }

      // حساب التأخير الأسي:
      const exponentialDelay = baseDelayMs * Math.pow(2, attempt);
      const cappedDelay = Math.min(exponentialDelay, maxDelayMs);

      // إضافة الـ Jitter العشوائي:
      const jitteredDelay = Math.random() * cappedDelay;

      console.warn(
        `فشلت المحاولة ${attempt + 1} (${error.message}). إعادة المحاولة بعد ${Math.round(jitteredDelay)}ms...`
      );

      await wait(jitteredDelay);
    }
  }
}

// مثال الاستخدام:
async function loadUserData(userId) {
  return retryWithBackoff(
    async () => {
      const res = await fetch(`/api/users/${userId}`);
      if (res.status === 429 || res.status >= 500) {
        throw new Error(`خطأ بالخادم: HTTP ${res.status}`);
      }
      if (!res.ok) {
        const fatalError = new Error(`خطأ من العميل: HTTP ${res.status}`);
        fatalError.isFatal = true;
        throw fatalError;
      }
      return res.json();
    },
    {
      maxRetries: 3,
      baseDelayMs: 250,
      shouldRetry: (err) => !err.isFatal // لا تعيد المحاولة لأخطاء 4xx القاتلة
    }
  );
}
```

---

## 🌊 5. المولدات غير المتزامنة (Async Generators) مع `for await...of`

عند التعامل مع استعلامات ضخمة مقسمة على صفحات (Pagination) أو تدفق سجلات في الوقت الفعلي، يؤدي تحميل كل البيانات دفعة واحدة في الذاكرة إلى اختناق المتصفح أو السيرفر.

تتيح لك **المولدات غير المتزامنة (Async Generators)** إنتاج وعود عبر الزمن وسحبها دفعة تلو الأخرى:

```javascript
// مولد يسحب البيانات المقسمة إلى صفحات (Pages) عند الطلب فقط:
async function* fetchPaginatedAuditLogs(pageSize = 50) {
  let cursor = null;
  let hasMore = true;

  while (hasMore) {
    const url = cursor 
      ? `/api/logs?limit=${pageSize}&cursor=${cursor}`
      : `/api/logs?limit=${pageSize}`;

    const res = await fetch(url);
    const data = await res.json();

    yield data.items; // إرجاع مصفوفة الدفعة الحالية للمستهلك

    cursor = data.nextCursor;
    hasMore = Boolean(cursor);
  }
}

// استهلاك التدفق دفعة بدفعة:
async function processAllLogs() {
  let totalProcessed = 0;

  for await (const batch of fetchPaginatedAuditLogs(100)) {
    console.log(`معالجة دفعة تحوي ${batch.length} سجل...`);
    for (const log of batch) {
      if (log.severity === 'CRITICAL') {
        alertAdmin(log);
      }
    }
    totalProcessed += batch.length;
    // يمكن لـ Garbage Collector التخلص من الذاكرة الخاصة بالدفعات السابقة فوراً!
  }

  console.log(`تمت معالجة إجمالي ${totalProcessed} سجل بنجاح دون إرهاق الذاكرة.`);
}
```

---

## 🧼 6. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: استخدام await متسلسل داخل Array.prototype.forEach (لا تنتظر العمليات!)
items.forEach(async (item) => {
  await uploadItem(item); // يتم إطلاق العمليات في الخلفية بلا تحكم أو انتظار للنتائج!
});

// ✅ جيد: استخدام Promise.all لانتظار العمليات المتزامنة بشكل منضبط
await Promise.all(items.map((item) => uploadItem(item)));

// ❌ سيء: استدعاء دالة async دون التقاط الخطأ (Floating Promise)
async function syncData() {
  fetch('/api/sync'); // في حال فشل الطلب سيحدث Unhandled Rejection في صمت
}

// ✅ جيد: المعالجة الصريحة للأخطاء ودعم الإلغاء عبر signal
async function syncData(signal) {
  try {
    const res = await fetch('/api/sync', { signal });
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return await res.json();
  } catch (error) {
    if (error.name === 'AbortError') return null; // تم الإلغاء عمداً
    logger.error('فشلت المزامنة:', error);
    throw error;
  }
}
```

---

## 📝 7. ملخص الدرس والخطوة القادمة

- استخدم **`Promise.all`** عندما تكون جميع العمليات لازمة ولا غنى عن أي منها (Fail-Fast).
- استخدم **`Promise.allSettled`** عندما تريد تجميع كل النتائج بغض النظر عن نجاح أو فشل بعضها.
- استخدم **`Promise.any`** عند الاتصال بخوادم بديلة للوصول لأسرع استجابة ناجحة.
- استخدم **`AbortController`** و `AbortSignal.timeout()` لإلغاء العمليات والطلبات المهملة وحماية الشبكة والذاكرة.
- احرص على تطبيق **Exponential Backoff مع Jitter** عند إعادة محاولة العمليات لتجنب مشكلة Thundering Herd.
- في الدرس القادم، سنتعمق داخل **محرك V8 وحلقة الأحداث (Event Loop): Microtasks و Macrotasks وكيفية منع تجميد المتصفح (Starvation)**.
