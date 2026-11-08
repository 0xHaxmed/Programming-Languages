# 10 - تسلسل وتبادل البيانات: مواصفات كائن JSON (Data Serialization)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/10-json.md) | [العربية 🇸🇦](./10-json.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تعد صيغة **JSON (JavaScript Object Notation)** المعيار العالمي الموحد لتبادل البيانات ونقلها عبر شبكة الإنترنت. ورغم أنها مشتقة نحوياً من طريقة كتابة الكائنات في جافا سكريبت، إلا أن JSON صيغة نصية مجردة ومستقلة تماماً عن أي لغة برمجة (معتمدة رسمياً تحت المعيار **RFC 8259**).

بنهاية هذا الدرس، ستتقن:
1. الفروق الهيكلية الصارمة بين كائنات جافا سكريبت العادية ونصوص صيغة الـ JSON الصالحة.
2. تسلسل البيانات وتحويلها إلى نصوص عبر `JSON.stringify()` مع دوال التصفية وتنسيق المسافات.
3. تفكيك نصوص الـ JSON بأمان عبر `JSON.parse()` وبناء حواجز معالجة الأخطاء (Error Boundaries).
4. استعادة الأنواع المعقدة (مثل كائنات التواريخ `Date`) باستخدام دالة الإحياء (Reviver Function).
5. تجنب الأفخاخ البرمجية الشائعة: المراجع الدائرية، فقدان البيانات غير المدعومة، ومخاطر حقن النماذج الأولية.

---

## 📜 2. كائن جافا سكريبت مقابل نص JSON الصالح

من الأخطاء الشائعة الخلط بين كائن جافا سكريبت وبين صيغة JSON. إن صيغة JSON هي **دائماً نص مجرد (String)**:

```
+------------------------------------+      +-----------------------------------------+
| كائن جافا سكريبت العادي            |      | نص JSON سليم ومعتمد                     |
+------------------------------------+      +-----------------------------------------+
| const user = {                     |      | '{"name":"أحمد","age":28,"roles":[]}'   |
|   name: 'أحمد', // علامات فردية    |      |                                         |
|   age: 28,                         |      | القواعد الصارمة:                        |
|   roles: [],                       |      | 1. المفاتيح يجب أن تُحاط باقتباس مزدوج  |
|   active: true,                    |      | 2. النصوص يجب أن تكون باقتباس مزدوج     |
|   greet() { console.log('Hi'); }   |      | 3. يُمنع وجود فاصلة في نهاية الكائن     |
| };                                 |      | 4. التعليقات ممنوعة تماماً داخل النص    |
|                                    |      | 5. لا تُقبل الدوال أو undefined أو Symbol|
+------------------------------------+      +-----------------------------------------+
```

### أنواع البيانات المدعومة في JSON:
- `string` (نصوص بين علامتي اقتباس مزدوجتين `" "`)
- `number` (أرقام صحيحة وعشرية، ولا تُقبل `NaN` أو `Infinity`)
- `boolean` (`true` أو `false`)
- `null` (قيمة فارغة)
- `array` (مصفوفات مثل `[1, 2, 3]`)
- `object` (كائنات مثل `{ "id": 1 }`)

---

## 🔄 3. تسلسل البيانات وتحويلها لنص: `JSON.stringify()`

تقوم دالة `JSON.stringify(value, replacer, space)` بتحويل قيم وكائنات جافا سكريبت إلى نصوص JSON قياسية.

### الاستخدام الأساسي:
```javascript
const session = {
  id: 402,
  user: 'حسن',
  isAdmin: false,
  cart: ['حاسوب', 'فأرة']
};

const jsonString = JSON.stringify(session);
console.log(typeof jsonString); // "string"
console.log(jsonString); // '{"id":402,"user":"حسن","isAdmin":false,"cart":["حاسوب","فأرة"]}'
```

### ماذا يحدث للبيانات غير المدعومة أثناء التحويل؟
تقوم `JSON.stringify` بتجريد وحذف أي خاصية لا تدعمها مواصفات JSON تلقائياً:
```javascript
const weirdObject = {
  title: 'تجربة',
  secretToken: undefined,          // يُحذف تماماً!
  calculateHash: () => 'hash123',  // تُحذف الدالة تماماً!
  uniqueId: Symbol('id'),          // يُحذف الرمز تماماً!
  items: [undefined, () => {}]     // داخل المصفوفات، تُستبدل بـ null: [null, null]
};

console.log(JSON.stringify(weirdObject)); // '{"title":"تجربة","items":[null,null]}'
```

### خيارات متقدمة: دالة التصفية وتنسيق القراءة
```javascript
const product = { id: 10, title: 'طاولة', cost: 120, markup: 30 };

// 1. مصفوفة المفاتيح المسموحة: تحويل خصائص محددة فقط:
console.log(JSON.stringify(product, ['title', 'cost']));
// '{"title":"طاولة","cost":120}'

// 2. دالة الاستبدال (Replacer Function): فلترة أو تعديل القيم ديناميكياً:
const filtered = JSON.stringify(product, (key, value) => {
  if (key === 'cost') return undefined; // استبعاد خاصية التكلفة
  return value;
});

// 3. تنسيق العرض بالمسافات البادئة (ممتاز للمعاينة وتتبع السجلات):
console.log(JSON.stringify(product, null, 2));
/*
{
  "id": 10,
  "title": "طاولة",
  "cost": 120,
  "markup": 30
}
*/
```

---

## 📥 4. تفكيك النصوص وتحويلها لكائنات: `JSON.parse()`

تقوم دالة `JSON.parse(text, reviver)` بتحويل نصوص الـ JSON إلى كائنات جافا سكريبت حية في الذاكرة.

### ⚠️ قاعدة إلزامية: غلف الاستدعاء دائماً داخل `try...catch`!
إذا كان النص يحتوي على خطأ نحوي بسيط (مثل علامة اقتباس فردية أو قوس ناقص)، فإن `JSON.parse()` ترمي استثناء `SyntaxError` يتسبب في توقف تنفيذ السكربت بالكامل ما لم يتم اصطياده:

```javascript
function safeJsonParse(rawString, fallback = null) {
  try {
    return JSON.parse(rawString);
  } catch (error) {
    console.error('فشل تحليل نص الـ JSON:', error.message);
    return fallback; // إرجاع قيمة بديلة آمنة بدلاً من انهيار التطبيق
  }
}

const badInput = "{ 'user': 'أحمد' }"; // نص غير صالح (اقتباس مفرد)
const result = safeJsonParse(badInput, {});
console.log(result); // {} (تم التعامل مع الخطأ بسلاسة)
```

### دالة الإحياء (Reviver): استعادة الكائنات المعقدة
لا تحتوي صيغة JSON على نوع مخصص للتواريخ؛ بل تتحول التواريخ إلى نصوص ISO مجردة. نستخدم دالة الـ Reviver لإعادة بنائها ككائنات `Date` أصلية أثناء التفكيك:

```javascript
const serverPayload = '{"title":"إصدار جديد","createdAt":"2026-11-08T20:30:00.000Z"}';

const record = JSON.parse(serverPayload, (key, value) => {
  if (key === 'createdAt') {
    return new Date(value); // إعادة بناء كائن التاريخ في الذاكرة
  }
  return value;
});

console.log(record.createdAt instanceof Date); // true
console.log(record.createdAt.getFullYear());   // 2026
```

---

## 💥 5. المشاكل الشائعة والمخاطر الأمنية

### 1. المراجع الدائرية (Circular References)
إذا كان الكائن يحتوي على مرجع يشير إلى نفسه، يفشل التحويل فوراً ويرمي `TypeError`:
```javascript
const node = { name: 'الأب' };
node.self = node; // مرجع دائري يشير لنفسه!

// JSON.stringify(node); 
// ❌ Uncaught TypeError: Converting circular structure to JSON
```

### 2. تلويث النموذج الأولي عبر JSON في جانب العميل
عندما يستقبل التطبيق بيانات JSON من مصادر خارجية غير موثوقة ويقوم بدمجها عودياً في كائنات النظام دون فحص:
```javascript
// حمولة JSON خبيثة:
const untrustedJson = '{"__proto__": {"isAdmin": true}}';
const parsed = JSON.parse(untrustedJson);

// إذا تم تمريرها لدالة دمج عادية غير محمية، قد تؤدي لتلويث Object.prototype!
```
*(احرص دائماً على استبعاد وتصفية المفاتيح المحجوزة مثل `__proto__` و `constructor` عند دمج الكائنات).*

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: افتراض أن ردود الشبكة تأتي دائماً كـ JSON سليم دون معالجة أخطاء
const data = JSON.parse(responseBody); // ينهار التطبيق إذا أعاد الخادم صفحة خطأ HTML 500!

// ✅ كود ممتاز: استخدام الفحص الدفاعي باستخدام try/catch
let data;
try {
  data = JSON.parse(responseBody);
} catch {
  data = { error: 'استجابة غير صالحة من الخادم' };
}

// ❌ كود سيئ: وضع دوال داخل كائنات مرسلة للـ API
const payload = {
  id: 1,
  execute: () => doWork() // سيتم حذفها بصمت دون أن تُرسل للخادم!
};
```

---

## 📝 7. الخلاصة والدرس التالي
- **JSON هو صيغة نصية** لتبادل البيانات؛ بينما **كائنات جافا سكريبت هي هياكل بيانات** حية في الذاكرة.
- المفاتيح والنصوص في JSON **يجب أن تستخدم علامات الاقتباس المزدوجة (`"`)**.
- دالة `JSON.stringify()` تحذف الدوال والرموز وقيم `undefined` تلقائياً.
- **لا تستدعِ `JSON.parse()` أبداً دون حمايتها بـ `try...catch`** في التطبيقات الإنتاجية.
- في الدرس التالي، سنصل إلى شريان تطبيقات الويب الحديثة: **طلبات الشبكة والتواصل مع الخوادم عبر `Fetch API`**.
