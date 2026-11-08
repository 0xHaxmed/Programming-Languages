# 11 - طلبات الشبكة والتواصل مع الخوادم عبر واجهة Fetch API

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/11-network-requests.md) | [العربية 🇸🇦](./11-network-requests.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

لا تحتاج تطبيقات الويب الحديثة إلى إعادة تحميل الصفحة بالكامل لتحديث محتواها. بدلاً من ذلك، تتبادل البيانات ديناميكياً في الخلفية مع واجهات برمجة التطبيقات (REST أو GraphQL APIs).

بينما كانت التطبيقات القديمة تعتمد على كائن `XMLHttpRequest` المعقد، تعتمد جافا سكريبت الحديثة على واجهة **`Fetch API`** الأنيقة والمبنية على الوعود (Promises).

بنهاية هذا الدرس، ستتقن:
1. دورة حياة طلبات واستجابات الـ HTTP بين العميل والخادم.
2. تنفيذ طلبات الشبكة بمختلف أنواعها (`GET`, `POST`, `PUT`, `DELETE`) عبر `fetch()`.
3. قراءة وضبط ترويسات الـ HTTP (مثل `Content-Type` و `Authorization`).
4. قراءة وفك تدفق البيانات المستلمة (`response.json()`, `response.text()`, `response.blob()`).
5. **الفخ الأكبر في `fetch()`:** لماذا لا ترمي الدالة خطأً عند استلام أكواد 404 أو 500، وكيفية استخدام `response.ok`.
6. إلغاء طلبات الشبكة المعلقة وضبط مهلة الانتظار باستخدام **`AbortController`**.

---

## 🌐 2. دورة حياة طلبات الـ HTTP بين العميل والخادم

عندما يطلب المتصفح بيانات من الخادم، يرسل طلب HTTP Request وينتظر استلام استجابة HTTP Response:

```
[ المتصفح (العميل - Client) ]                         [ خادم الويب (Backend API) ]
         |                                                       |
         | --- 1. طلب HTTP (نوع العملية، الرابط، الترويسات) ---> |
         |                                                       | (معالجة واستعلام قاعدة البيانات)
         | <--- 2. استجابة HTTP (كود الحالة، الترويسات، البيانات)- |
         v                                                       v
```

### أفعال الـ HTTP الأساسية (HTTP Verbs):
- **`GET`:** جلب واسترجاع البيانات (لا يرسل جسم طلب `body`).
- **`POST`:** إرسال بيانات جديدة لحفظها (مثل إنشاء حساب أو تسجيل طلب شراء).
- **`PUT` / `PATCH`:** تحديث أو استبدال مورد موجود مسبقاً.
- **`DELETE`:** حذف مورد معين من الخادم.

---

## 🚀 3. جلب البيانات عبر `fetch()`

تأخذ دالة `fetch()` رابط الـ URL ومعاملات تكوين اختيارية، وتُرجع **وعداً (Promise)** ينتهي بكائن الاستجابة `Response`.

### العملية اللاتزامنية المكونة من خطوتين:
1. **الخطوة الأولى:** يستلم المتصفح ترويسات الـ HTTP وكود الحالة (`response`).
2. **الخطوة الثانية:** قراءة وفك تدفق محتوى الاستجابة بشكل لاتزامني عبر (`response.json()`).

```javascript
// طلب GET قياسي:
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => {
    // ⚠️ التأكد من أن كود الحالة يقع في نطاق النجاح (200-299):
    if (!response.ok) {
      throw new Error(`خطأ في الشبكة! كود الحالة: ${response.status}`);
    }
    return response.json(); // قراءة وتحويل تدفق الـ JSON إلى كائن جافا سكريبت
  })
  .then(data => {
    console.log('عنوان المنشور:', data.title);
  })
  .catch(error => {
    console.error('فشل في الاتصال أو التحليل:', error.message);
  });
```

---

## ⚠️ 4. الفخ الأكبر في دالة Fetch: خاصية `response.ok`

> [!CAUTION]
> **قاعدة حاسمة يقع فيها أغلب المطورين:**  
> الوعد (Promise) الذي ترجعه دالة `fetch()` **لن يفشل (Will NOT Reject) عند استقبال أكواد الأخطاء 404 أو 500!**  
> تفشل `fetch()` فقط إذا كان هناك **عطل مادي في الاتصال بالشبكة** (مثل انقطاع الإنترنت تماماً، أو فشل الـ DNS، أو حظر المتصفح للطلب بسبب سياسة الـ CORS).
> 
> يجب عليك دائماً فحص الخاصية **`response.ok`** يدوياً في كل طلب!

```javascript
// فحص بيانات الاستجابة:
fetch('/api/user/999')
  .then(response => {
    console.log(response.status);     // مثلاً: 404
    console.log(response.statusText); // 'Not Found'
    console.log(response.ok);         // false (لأن الكود خارج نطاق 200-299)

    if (!response.ok) {
      throw new Error(`أرجع الخادم كود الخطأ: ${response.status}`);
    }
    return response.json();
  });
```

---

## 📤 5. إرسال البيانات إلى الخادم: `POST`, `PUT`, `DELETE`

لإرسال بيانات إلى الخادم، نمرر كائن إعدادات كمعامل ثانٍ:

```javascript
async function createNewProduct(productData) {
  try {
    const response = await fetch('https://api.example.com/products', {
      method: 'POST',
      headers: {
        // ضروري جداً: إعلام الخادم بأن محتوى البيانات بتنسيق JSON:
        'Content-Type': 'application/json',
        // ترويسة مصادقة المستخدم (Token):
        'Authorization': 'Bearer YOUR_ACCESS_TOKEN'
      },
      // تحويل كائن جافا سكريبت إلى نص JSON:
      body: JSON.stringify(productData)
    });

    if (!response.ok) {
      throw new Error(`فشل إنشاء المنتج: ${response.status}`);
    }

    const createdRecord = await response.json();
    console.log('معرف المنتج الجديد:', createdRecord.id);
    return createdRecord;
  } catch (error) {
    console.error('فشل الطلب بالكامل:', error);
  }
}

// استدعاء الدالة:
createNewProduct({ name: 'لوحة مفاتيح ميكانيكية', price: 120 });
```

---

## ⏱️ 6. إلغاء طلبات الشبكة المعلقة: `AbortController`

إذا غادر المستخدم الصفحة، أو كان يكتب بسرعة في حقل بحث فوري (Autocomplete)، يجب إلغاء الطلبات السابقة لتوفير موارد الشبكة ومنع تضارب الردود:

```javascript
const controller = new AbortController();
const { signal } = controller;

// تمرير إشارة الإلغاء للدالة:
fetch('https://api.example.com/large-dataset', { signal })
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => {
    if (err.name === 'AbortError') {
      console.log('تم إلغاء الطلب بنجاح بواسطة المستخدم.');
    } else {
      console.error('خطأ شبكة آخر:', err);
    }
  });

// إلغاء الطلب إذا استغرق أكثر من 3 ثوانٍ (Timeout):
setTimeout(() => {
  controller.abort(); // يطلق خطأ AbortError فوراً داخل الطلب!
}, 3000);
```

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: استدعاء fetch دون فحص response.ok
fetch('/api/profile')
  .then(res => res.json()) // إذا أعاد الخادم صفحة خطأ HTML 500، سينهار الكود بخطأ SyntaxError!
  .then(data => render(data));

// ✅ كود ممتاز: بناء دالة مساعدة متينة تفحص كود الحالة
async function fetchJson(endpoint) {
  const response = await fetch(endpoint);
  if (!response.ok) {
    throw new Error(`فشل الطلب إلى ${endpoint} بكود: ${response.status}`);
  }
  return response.json();
}

// ❌ كود سيئ: نسيان ترويسة Content-Type عند إرسال طلب POST
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'أحمد' }) // سيفشل الخادم في قراءة البيانات لغياب الترويسة!
});

// ✅ كود ممتاز: تحديد ترويسة Content-Type بدقة
fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'أحمد' })
});
```

---

## 📝 8. الخلاصة والدرس التالي
- دالة **`fetch()`** حديثة ومبنية بالكامل على الوعود (Promises).
- قراءة الرد تتطلب خطوتين: استلام الترويسات أولاً ثم فك تدفق البيانات بـ **`response.json()`**.
- افحص دائماً **`response.ok`**؛ فأخطاء 4xx و 5xx لا تتسبب في رفض الوعد تلقائياً.
- حدد دائماً `'Content-Type': 'application/json'` عند إرسال بيانات بالـ POST أو الـ PUT.
- استخدم **`AbortController`** لإلغاء الطلبات وإدارة مهلات الانتظار (Timeouts).
- في الدرس التالي، سنغوص في قلب البرمجة اللاتزامنية في جافا سكريبت: **الوعود وصيغة async/await وسلسلة المعالجة (Async Basics)**.
