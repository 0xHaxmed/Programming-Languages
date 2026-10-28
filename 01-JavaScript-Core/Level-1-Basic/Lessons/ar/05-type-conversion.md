# 05 - تحويل الأنواع والتحويل التلقائي (Type Conversion & Coercion)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./05-type-conversion.md) | [English 🇬🇧](../en/05-type-conversion.md)

---

## 🔄 1. ما هو تحويل الأنواع؟

في كثير من الأحيان، تحتاج لتحويل قيمة من نوع إلى نوع آخر (مثلاً: قراءة رقم مدخل من المستخدم كـ String وتحويله إلى Number لإجراء عمليات حسابية عليه).

يوجد نوعان أساسيان للتحويل في جافا سكريبت:
1. **التحويل الصريح (Explicit Conversion / Type Casting):** يقوم به المبرمج يدوياً باستخدام دوال اللغة.
2. **التحويل الضمني التلقائي (Implicit Conversion / Type Coercion):** يقوم به محرك جافا سكريبت تلقائياً في الخلفية عند تنفيذ عمليات بين أنواع مختلفة.

---

## 🛠️ 2. التحويل الصريح (Explicit Conversion)

### أ. التحويل إلى نصوص (To String)
```javascript
let score = 95;
let str1 = String(score);      // "95"
let str2 = score.toString();   // "95"
let str3 = `${score}`;         // "95" (باستخدام Template Literal)
```

### ب. التحويل إلى أرقام (To Number)
```javascript
let input = "42";
let num1 = Number(input);      // 42
let num2 = parseInt("42.5px"); // 42 (يستخرج الرقم الصحيح ويتجاهل النصوص اللاحقة)
let num3 = parseFloat("3.14m");// 3.14 (يستخرج الرقم العشري)
let num4 = +"50";              // 50 (باستخدام معامل الزائد الأحادي Unary Plus)

// حالات خاصة هامة:
Number("أحمد");     // NaN (Not-a-Number)
Number(true);       // 1
Number(false);      // 0
Number(null);       // 0
Number(undefined);  // NaN
```

### ج. التحويل إلى قيم منطقية (To Boolean)
```javascript
Boolean(1);          // true
Boolean(0);          // false
Boolean("مرحباً");   // true
Boolean("");         // false
Boolean(null);       // false
Boolean(undefined);  // false
```

---

## 🎭 3. التحويل الضمني التلقائي (Type Coercion)

محرك جافا سكريبت يحاول "مساعدتك" أحياناً بتحويل الأنواع ذاتياً، مما قد يؤدي لنتائج غير متوقعة إذا لم تفهم الآلية:

### أ. معامل الجمع (`+`) مع النصوص
إذا كان أحد الطرفين نصاً (String)، تتحول العملية فوراً إلى دمج نصوص (String Concatenation):
```javascript
console.log(5 + "5");       // "55" (تحول الرقم 5 إلى نص)
console.log("النتيجة: " + 10); // "النتيجة: 10"
console.log(1 + 2 + "3");    // "33" (حُسب 1+2=3 أولاً، ثم دمج مع "3")
```

### ب. العمليات الرياضية الأخرى (`-`, `*`, `/`)
تقوم جافا سكريبت دائماً بتحويل النصوص إلى أرقام:
```javascript
console.log("10" - 2);   // 8  (تم تحويل "10" إلى رقم)
console.log("6" * "2");  // 12
console.log("10" / "2");  // 5
console.log("عشرة" - 2); // NaN (لا يمكن تحويل الكلمة لرقم)
```

### ج. تحويل الـ Booleans مع الأرقام
```javascript
console.log(true + 1);  // 2 (true تتحول إلى 1)
console.log(false + 5); // 5 (false تتحول إلى 0)
```

---

## ⚠️ 4. فخاخ مشهورة في Type Coercion

```javascript
console.log([] + []);        // "" (نص فارغ)
console.log([] + {});        // "[object Object]"
console.log(null == undefined); // true (بالمقارنة الضعيفة)
console.log(null === undefined); // false (بالمقارنة الصارمة)
```

> [!TIP]
> **نصيحة أمان برمجية:**  
> لتجنب كوابيس الـ Type Coercion والأخطاء الصعبة في التتبع، استخدم دائماً التحويل الصريح (`Number()`, `String()`) واستخدم عامل المقارنة الصارم `===` بدلاً من `==`.

---

## 🎯 ملخص وتحدي الدرس

- التحويل الصريح: أنت تتحكم في تحويل النوع برمجياً.
- التحويل الضمني (Coercion): المحرك يحول النوع تلقائياً.
- علامة `+` تدمج إذا وُجد نص، بينما `-` و `*` و `/` تجبر التحويل لأرقام.

**التحدي:**  
توقع ناتج العمليات التالية بدون تشغيل الكود أولاً، ثم شغلها في الكونسول لتتأكد من إجابتك:
1. `"" + 1 + 0`
2. `"" - 1 + 0`
3. `true + false`
4. `"4px" - 2`

---

**الانتقال إلى:**  
⬅️ [الدرس السابق: أنواع البيانات](./04-data-types.md) | ➡️ [الدرس التالي: العمليات الحسابية والمنطقية (Operators)](./06-operators.md)
