# 12 - قراءة الأخطاء وأدوات التصحيح (Error Handling & Debugging)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./12-error-handling.md) | [English 🇬🇧](../en/12-error-handling.md)

---

## 💥 1. لماذا تحدث الأخطاء؟

الأخطاء (Errors / Bugs) هي جزء طبيعي ويومي من حياة أي مبرمج. المبرمج المحترف لا يخاف من ظهور الخطأ، بل يمتلك المهارة لقراءة رسالة الخطأ ومعرفة سببها وتصحيحها في دقائق.

---

## 🔍 2. أشهر أنواع الأخطاء في جافا سكريبت

عندما يواجه المحرك مشكلة تمنعه من الاستمرار، يُلقي (Throws) كائن خطأ يحمل اسماً ورسالة توضيحية:

### أ. `ReferenceError` (خطأ مرجعي)
يحدث عندما تحاول استخدام متغير لم يتم تعريفه أو الإعلان عنه:
```javascript
console.log(userToken); // ReferenceError: userToken is not defined
```

### ب. `TypeError` (خطأ في نوع العملية)
يحدث عندما تحاول تنفيذ عملية على نوع بيانات غير مناسب (مثل استدعاء متغير كدالة، أو قراءة خاصية من `null` أو `undefined`):
```javascript
let user = null;
console.log(user.name); // TypeError: Cannot read properties of null (reading 'name')

let count = 42;
count(); // TypeError: count is not a function
```

### ج. `SyntaxError` (خطأ نحوي في كتابة الكود)
يحدث قبل تشغيل الكود أصلاً (أثناء مرحلة الـ Parsing)، بسبب نسيان قوس، أو علامة تنصيص، أو استخدام كلمة محجوزة بشكل خاطئ:
```javascript
// let for = 10; // SyntaxError: Unexpected token 'for'
```

---

## 🛠️ 3. قراءة تتبع الخطأ (Stack Trace)

عند حدوث خطأ، يطبع المتصفح أو بيئة Node.js تقريراً يسمى **Stack Trace**. أهم جزئيتين فيه:
1. **اسم الخطأ ورسالته:** (مثال: `TypeError: Cannot read properties of undefined`).
2. **رقم السطر واسم الملف:** في أقصى اليمين (مثال: `app.js:24:12`)؛ يخبرك بدقة أن الخطأ حدث في ملف `app.js` عند السطر 24 والعمود 12.

---

## 🧰 4. أسرار كائن الـ `console`

معظم المبتدئين يكتفون بـ `console.log`، لكن كائن `console` يقدم أدوات تشخيص خارقة:

```javascript
// 1. تحذير بلون أصفر:
console.warn("تنبيه: حجم الملف كبير!");

// 2. خطأ بلون أحمر:
console.error("فشل الاتصال بقاعدة البيانات!");

// 3. عرض مصفوفة الكائنات في جدول أنيق (ميزة مذهلة!):
const users = [
    { id: 1, name: "كريم", role: "admin" },
    { id: 2, name: "ياسمين", role: "editor" }
];
console.table(users);

// 4. قياس سرعة تنفيذ الكود:
console.time("LoopSpeed");
for (let i = 0; i < 1000000; i++) {}
console.timeEnd("LoopSpeed"); // LoopSpeed: 3.2ms
```

---

## 🛡️ 5. الحماية من انهيار البرنامج (`try...catch`)

تُستخدم جملة `try...catch` لاحتواء الكود الذي يُحتمل أن يفشل (مثل قراءة بيانات خارجية أو اتصال بشبكة)، لمنع التطبيق من التوقف المفاجئ (Crashing):

```javascript
function parseUserData(jsonString) {
    try {
        console.log("محاولة معالجة البيانات...");
        const data = JSON.parse(jsonString); // قد يسبب خطأ لو كان النص غير صالح
        console.log("تمت المعالجة بنجاح:", data);
        return data;
    } catch (error) {
        // يتم تنفيذ هذا الجزء فقط في حال حدوث خطأ في الـ try
        console.error("حدث خطأ أثناء معالجة البيانات:", error.message);
        return null; // قيمة بديلة آمنة
    } finally {
        // يتم تنفيذه دائماً سواء نجحت العملية أو فشلت
        console.log("اكتمل فحص العملية.");
    }
}

parseUserData('{"name": "سعد"}'); // صحيح
parseUserData('نص غير صالح');      // سيعالجه الـ catch بهدوء دون توقف التطبيق
```

---

## 🎯 ملخص وتحدي الدرس

- اقرأ اسم الخطأ ورقم السطر دائماً قبل أي شيء.
- فرق بين `ReferenceError` و `TypeError`.
- استخدم `console.table()` لفحص البيانات المعقدة.
- استخدم `try/catch` للتعامل الآمن مع الأخطاء وتوفير تجربة مستخدم مستقرة.

**التحدي:**  
اكتب دالة باسم `safeDivide(a, b)`:  
- إذا كان `b === 0`، قم بإلقاء خطأ باستخدام `throw new Error("لا يمكن القسمة على الصفر!")`.
- استدعِ الدالة داخل كتلة `try...catch` لاصطياد رسالة الخطأ وطباعتها بأمان دون توقف البرنامج.

---

**الانتقال إلى:**  
⬅️ [الدرس السابق: النصوص](./11-string-handling.md) | 🎯 [تمارين المستوى الأول](../../Exercises/ar/README.md) | 🏆 [مشروع نهاية المستوى](../../Final-Project/ar/README.md)
