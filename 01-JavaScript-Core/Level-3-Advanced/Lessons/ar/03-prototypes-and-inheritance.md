# 03 - النماذج الأولية وسلسلة الوراثة (Prototypes & Inheritance)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/03-prototypes-and-inheritance.md) | [العربية 🇸🇦](./03-prototypes-and-inheritance.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

على عكس لغات مثل Java أو C++ التي تعتمد على الوراثة الكلاسيكية القائمة على الأصناف (حيث تكون الكلاسات بمثابة مخططات ثابتة تُنسخ منها الكائنات)، تعتمد لغة جافا سكريبت على **الوراثة القائمة على النماذج الأولية (Prototypal Inheritance)**.

في جافا سكريبت، ترث الكائنات مباشرة من كائنات حية أخرى عبر رابط داخلي يُعرف باسم **سلسلة النماذج الأولية (Prototype Chain)**. إن إتقان كيفية انتقال الخصائص عبر هذه السلسلة هو الأساس المعماري لبناء كود عالي الكفاءة ولتأمين التطبيقات ضد ثغرات التلويث (Prototype Pollution).

بنهاية هذا الدرس، ستتقن:
1. الفارق المعماري بين التفويض القائم على النماذج (Delegation) والوراثة الكلاسيكية.
2. التمييز الدقيق بين `[[Prototype]]` و `__proto__` و `Constructor.prototype`.
3. كيفية تتبع محرك جافا سكريبت للخصائص عبر **سلسلة النماذج الأولية (Prototype Chain)**.
4. ميكانيكا **حجب الخصائص (Property Shadowing)** وفحص الملكية بـ `Object.hasOwn()`.
5. إنشاء كائنات قواميس مجردة بلا نماذج أولية عبر `Object.create(null)`.
6. لماذا يُعد التعديل على النماذج المدمجة في اللغة (**Monkey-Patching**) ممارسة برمجية محظورة.

---

## 🔗 2. سلسلة النماذج الأولية: مبدأ التفويض (Delegation)

يمتلك كل كائن في جافا سكريبت خاصية داخلية مخفية تُسمى `[[Prototype]]` تشير إلى كائن آخر (أو `null`):

```
+------------------+         +-------------------------+         +--------------------+         +------+
|   userInstance   | ------> |  UserProfile.prototype  | ------> |  Object.prototype  | ------> | null |
|  { name: 'علي' } | [[proto]]|  { getRole: fn() }     | [[proto]]|  { toString: fn() }| [[proto]]|      |
+------------------+         +-------------------------+         +--------------------+         +------+
```

### كيف يبحث المحرك عن الخصائص؟
عندما تطلب `userInstance.getRole()`:
1. هل دالة `getRole` موجودة مباشرة كخاصية مملوكة للكائن `userInstance`؟ **لا.**
2. يتبع المحرك الرابط الداخلي `[[Prototype]]` إلى `UserProfile.prototype`. هل هي موجودة هناك؟ **نعم!**
3. ينفذ المحرك الدالة مع ربط `this` بالكائن الأصلي المستدعي `userInstance`.
4. لو صعد المحرك حتى نهاية السلسلة (`Object.prototype`) ولم يجد الخاصية، فإنه يُرجع `undefined`.

---

## 🧬 3. التمييز بين `prototype` و `__proto__` و `Object.getPrototypeOf()`

```
المصطلح                        وظيفته الدقيقة
Constructor.prototype         خاصية معرّفة على دوال البناء (Constructors) فقط، وتُستخدم
                              كنموذج أولي [[Prototype]] لأي كائن يُنشأ عبر 'new Constructor()'.

obj.__proto__                 طريقة قديمة (Getter/Setter) على Object.prototype تكشف [[Prototype]].
                              (منبوذة ومحظورة في المعايير الحديثة؛ تجنب استخدامها).

Object.getPrototypeOf(obj)    الدالة القياسية الرسمية والحديثة في اللغة لفحص نموذج الكائن الأولي.
```

### التطبيق العملي لدوال البناء:
```javascript
function Device(brand) {
  this.brand = brand; // خاصية مملوكة لكل كائن على حدة (Own Property)
}

// إضافة الدوال المشتركة إلى النموذج الأولي (تُحجز مرة واحدة فقط في الذاكرة!):
Device.prototype.powerOn = function () {
  console.log(`جهاز ${this.brand} يعمل الآن.`);
};

const laptop = new Device('ThinkPad');
const phone = new Device('Pixel');

// كلا الجهازين يتشاركان نفس مرجع الدالة تماماً في الذاكرة:
console.log(laptop.powerOn === phone.powerOn); // true!
laptop.powerOn(); // "جهاز ThinkPad يعمل الآن."
```

---

## 👥 4. حجب الخصائص (Shadowing) والتحقق بـ `Object.hasOwn()`

إذا قمت بتعريف خاصية مباشرة على الكائن بنفس اسم خاصية موجودة في نموذجه الأولي، فإن الخاصية المملوكة **تحجب (Shadows)** الخاصية الموروثة دون المساس بالأصل:

```javascript
laptop.powerOn = function () {
  console.log('تشغيل مخصص وفائق السرعة لـ ThinkPad!');
};

laptop.powerOn(); // "تشغيل مخصص..." (تم حجب الدالة الموروثة)
phone.powerOn();  // "جهاز Pixel يعمل الآن." (النموذج الأولي الأصلي سليم تماماً)
```

### فحص ملكية الخصائص عبر `Object.hasOwn()`
أُضيفت دالة `Object.hasOwn(obj, prop)` في معيار **ES2022** كبديل آمن وحديث للدالة القديمة `obj.hasOwnProperty()`:

```javascript
console.log(Object.hasOwn(laptop, 'brand'));   // true (خاصية مملوكة للكائن)
console.log(Object.hasOwn(laptop, 'powerOn')); // true (خاصية مملوكة حجبت الأصل)
console.log(Object.hasOwn(phone, 'powerOn'));  // false (موروثة من النموذج وليست مملوكة)
```

> [!TIP]
> **لماذا تفوقت `Object.hasOwn`؟**  
> إذا كان الكائن منشأً بـ `Object.create(null)` أو كان يحتوي على خاصية مخصصة تسمى `hasOwnProperty`، فإن استدعاء `obj.hasOwnProperty()` سينهار بخطأ `TypeError`. دالة `Object.hasOwn()` محصنة تماماً ضد هذا الانهيار.

---

## 🛡️ 5. قواميس بلا وراثة: `Object.create(null)`

ترث الكائنات العادية خصائص مثل `toString` و `valueOf` و `constructor` تلقائياً من `Object.prototype`:
```javascript
const normalObj = {};
console.log('toString' in normalObj); // true (موروثة من Object.prototype!)
```

في السياقات الحساسة أمنياً (مثل جداول التجزئة، وتخزين المفاتيح والقيم، ومحللات الروابط)، يمكن أن تسبب هذه الخصائص الموروثة أخطاء أو تمهد لثغرات Prototype Pollution. يمكنك إنشاء **قاموس نظيف تماماً لا يرث أي شيء**:

```javascript
const secureMap = Object.create(null);

console.log(Object.getPrototypeOf(secureMap)); // null
console.log('toString' in secureMap);          // false! صفحة بيضاء نقية تماماً

// آمن تماماً من تصادم الخصائص الموروثة:
secureMap['item1'] = 'بيانات';
```

---

## 🚫 6. النمط الكارثي: تعديل النماذج المدمجة (Monkey-Patching)

إياك والتعديل على النماذج الأولية المدمجة في لغة جافا سكريبت (`Array.prototype`, `Object.prototype`, `String.prototype`):

```javascript
// ❌ كود خطير ومرفوض تماماً (Monkey-Patching):
Array.prototype.last = function() {
  return this[this.length - 1];
};

// لماذا يُحظر هذا الأسلوب في الشركات العالمية؟
// 1. يتصادم مع أي تحديثات رسمية مستقبلية لمعايير ECMAScript.
// 2. يسبب انهيار مكتبات الطرف الثالث التي تعمل في نفس البيئة.
// 3. يفتح ثغرات أمنية كارثية (Prototype Pollution).
```

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: فحص وجود الخاصية بمعامل 'in' (يفحص الخصائص الموروثة أيضاً!)
if ('toString' in requestPayload) {
  // يرجع true حتى لو لم يرسل المستخدم خاصية toString أبداً!
}

// ✅ كود ممتاز: استخدام Object.hasOwn للتأكد من الخصائص المملوكة حصراً
if (Object.hasOwn(requestPayload, 'targetId')) {
  processTarget(requestPayload.targetId);
}

// ❌ كود سيئ: إعادة إنشاء نفس الدوال داخل دالة البناء (يهدر ذاكرة الـ Heap في كل كائن)
function User(name) {
  this.name = name;
  this.login = function() { /* تنشئ نسخة جديدة لكل مستخدم */ };
}

// ✅ كود ممتاز: وضع الدوال المشتركة على النموذج الأولي Prototype أو استخدام ES6 Classes
function User(name) {
  this.name = name;
}
User.prototype.login = function() { /* مرجع واحد مشترك في الذاكرة */ };
```

---

## 📝 8. الخلاصة والدرس التالي
- جافا سكريبت تعتمد على **التفويض عبر النماذج الأولية (Prototypal Delegation)**.
- تحدد **`Constructor.prototype`** النموذج الأولي للكائنات المنشأة بـ `new`.
- استخدم **`Object.hasOwn(obj, prop)`** للتحقق الموثوق من الخصائص المملوكة للكائن.
- استخدم **`Object.create(null)`** لإنشاء قواميس آمنة ومحصنة من التلويث.
- **تجنب التعديل على النماذج المدمجة** (`Monkey-Patching`) نهائياً.
- في الدرس التالي، سننتقل إلى البرمجة الوظيفية المتقدمة: **الدوال المتقدمة، التركيب، والتجزئة (Currying, Composition & Higher-Order Functions)**.
