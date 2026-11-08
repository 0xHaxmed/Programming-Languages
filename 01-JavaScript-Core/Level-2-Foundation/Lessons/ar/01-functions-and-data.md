# 01 - الدوال والتكرار الوظيفي ومعالجة البيانات الحديثة (Functions & Modern Data Manipulation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/01-functions-and-data.md) | [العربية 🇸🇦](./01-functions-and-data.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في المستوى الأول، تعلمت كيفية إنشاء الدوال الأساسية وتخزين العناصر في المصفوفات والكائنات. أما في جافا سكريبت الحديثة المعتمدة في الشركات (ES6+)، تُعامل الدوال كـ "مواطنين من الدرجة الأولى (First-Class Citizens)" وتعتمد معالجة البيانات على النمط الوظيفي التعريفي غير القابل للتعديل المباشر (Declarative & Immutable Paradigms).

بنهاية هذا الدرس، ستتقن:
1. كتابة **الدوال السهمية (Arrow Functions)** بأسلوب برمجي نظيف وموجز.
2. تحويل وتصفية مصفوفات البيانات وظيفياً باستخدام `map()` و `filter()` و `reduce()`.
3. استخراج البيانات بكفاءة واختصار باستخدام **التفكيك (Destructuring Assignment)**.
4. استخدام معاملي **النشر (Spread)** و **التجميع (Rest)** للتحديث الآمن للبيانات.
5. تطبيق معايير الكود النظيف (Clean Code وفق دليل Airbnb) لتجنب أخطاء تعديل البيانات المباشر (Mutation Bugs).

---

## 🏹 2. الدوال السهمية (Arrow Functions `=>`)

قُدمت الدوال السهمية في معيار ES6 لتوفر صياغة برمجية مختصرة وأنيقة لكتابة تعبيرات الدوال (Function Expressions).

### أشكال الصياغة النحوية (Syntax Variations):
```javascript
// 1. دالة سهمية متعددة الأسطر (Multi-line)
const calculateTax = (amount, rate) => {
  const tax = amount * rate;
  return amount + tax;
};

// 2. دالة سهمية من سطر واحد بإرجاع ضمني (Implicit Return):
const double = (n) => n * 2;

// 3. إرجاع كائن مجرد (يجب إحاطة الأقواس المعقوفة بأقواس دائرية لمنع الالتباس):
const makeUser = (id, username) => ({ id, username });

// 4. معامل واحد فقط (الأقواس اختيارية ولكن يفضل كتابتها للتوحيد):
const square = x => x * x;
```

> [!TIP]
> **قاعدة الكود النظيف (معيار Airbnb):**  
> فضل دائماً استخدام الدوال السهمية للدوال المجهولة ودوال رد النداء (Callbacks) مثل التي تُمرر إلى `map` أو معالجات الأحداث لجعل الكود أكثر وضوحاً وانسيابية.

---

## 🔄 3. دوال التكرار الوظيفية: `map`, `filter`, `reduce`

في جافا سكريبت الحديثة، تجنب استخدام حلقات `for` التقليدية ذات العداد لمعالجة البيانات. الطرق الوظيفية تعبر عما ترغب في تحقيقه (Declarative) بدلاً من كتابة تفاصيل خطوات التكرار يدوياً.

### أ) الدالة `Array.prototype.map()`: تحويل العناصر
تُرجع **مصفوفة جديدة** بنفس طول المصفوفة الأصلية، بحيث يكون كل عنصر هو ناتج استدعاء دالة رد النداء:

```javascript
const pricesInUSD = [10, 25, 50, 100];
const exchangeRate = 1.25;

// تحويل الأسعار من الدولار إلى اليورو:
const pricesInEUR = pricesInUSD.map(price => price * exchangeRate);
console.log(pricesInEUR); // [12.5, 31.25, 62.5, 125]
```

### ب) الدالة `Array.prototype.filter()`: ترشيح واختيار العناصر
تُرجع **مصفوفة جديدة** تحتوي فقط على العناصر التي تحقق شرط الدالة (ترجع `true`):

```javascript
const products = [
  { id: 1, name: 'Keyboard', inStock: true, price: 75 },
  { id: 2, name: 'Mouse', inStock: false, price: 25 },
  { id: 3, name: 'Monitor', inStock: true, price: 300 }
];

// استخراج المنتجات المتاحة في المخزن والتي سعرها أقل من 100:
const availableProducts = products.filter(item => item.inStock && item.price < 100);
console.log(availableProducts); 
// [{ id: 1, name: 'Keyboard', inStock: true, price: 75 }]
```

### ج) الدالة `Array.prototype.reduce()`: تجميع المصفوفة في قيمة واحدة
تختزل عناصر المصفوفة في قيمة واحدة نهائية (رقم، نص، كائن، أو مصفوفة جديدة):

```javascript
const cartItems = [
  { title: 'Book', price: 15, quantity: 2 },
  { title: 'Pen', price: 3, quantity: 5 },
  { title: 'Bag', price: 40, quantity: 1 }
];

// حساب إجمالي قيمة السلة:
const cartTotal = cartItems.reduce((accumulator, item) => {
  return accumulator + (item.price * item.quantity);
}, 0); // 0 هي القيمة الابتدائية للمجمع (accumulator)

console.log(cartTotal); // 85
```

### دوال بحث إضافية سريعة:
- `find(fn)`: تُرجع **أول عنصر** يطابق الشرط، أو `undefined` إذا لم يوجد.
- `findIndex(fn)`: تُرجع ترتيب (فهرس) أول عنصر يطابق الشرط، أو `-1`.
- `some(fn)`: ترجع `true` إذا كان **عنصر واحد على الأقل** يطابق الشرط.
- `every(fn)`: ترجع `true` فقط إذا كانت **جميع العناصر** تطابق الشرط.

---

## 📦 4. تفكيك البيانات (Destructuring Assignment)

يتيح لك التفكيك استخراج القيم من المصفوفات أو الخصائص من الكائنات وتعيينها مباشرة إلى متغيرات مستقلة بشكل مختصر.

### تفكيك الكائنات (Object Destructuring):
```javascript
const developer = {
  id: 101,
  fullName: 'أحمد محمود',
  role: 'Frontend Engineer',
  country: 'مصر'
};

// الاستخراج المباشر:
const { fullName, role } = developer;

// الاستخراج مع إعادة التسمية (Aliasing) والقيم الافتراضية (Default Values):
const { role: jobTitle, seniority = 'Junior' } = developer;
console.log(jobTitle);  // 'Frontend Engineer'
console.log(seniority); // 'Junior' (طُبقت القيمة الافتراضية لعدم وجود الخاصية في الكائن)
```

### تفكيك المصفوفات (Array Destructuring):
```javascript
const rgb = [255, 128, 0];

// التعيين حسب الترتيب الموضعي:
const [red, green, blue] = rgb;
console.log(red); // 255

// تخطي عناصر باستخدام الفواصل الفارغة:
const [, onlyGreen] = rgb;
console.log(onlyGreen); // 128
```

---

## ⚡ 5. معاملا التجميع والنشر (Rest & Spread Operators `...`)

كلاهما يستخدم الرموز الثلاثة (`...`)، ولكن المعنى والوظيفة يختلفان تماماً بناءً على سياق الاستخدام:

### أ) معامل التجميع (Rest Operator): جمع العناصر
يقوم بجمع العناصر المتبقية ووضعها داخل مصفوفة:

```javascript
// 1. في معاملات الدوال (بديل حديث لكائن arguments القديم):
function logScores(teamName, ...scores) {
  console.log(`Team: ${teamName}`);
  console.log(`Scores:`, scores); // المتغير scores عبارة عن مصفوفة عادية تماماً
}
logScores('Alpha', 95, 88, 100);

// 2. مع تفكيك المصفوفات:
const [primaryUser, ...otherUsers] = ['علي', 'عمر', 'سارة', 'ريم'];
console.log(primaryUser); // 'علي'
console.log(otherUsers);  // ['عمر', 'سارة', 'ريم']
```

### ب) معامل النشر (Spread Operator): فك ونشر العناصر
يقوم بنشر وتوزيع عناصر مصفوفة أو خصائص كائن داخل مكان جديد:

```javascript
// 1. دمج المصفوفات بصورة آمنة دون تعديل الأصليات:
const frontend = ['HTML', 'CSS', 'JS'];
const backend = ['Node.js', 'PostgreSQL'];
const fullStack = [...frontend, ...backend, 'Docker'];

// 2. تحديث كائن بصورة غير معدلة للأصل (Immutable Object Update):
const baseConfig = { theme: 'dark', language: 'ar', autoSave: true };

// تعديل الخاصية theme مع الحفاظ على باقي الإعدادات:
const userConfig = {
  ...baseConfig,
  theme: 'light'
};
console.log(userConfig);
// { theme: 'light', language: 'ar', autoSave: true }
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: تعديل المعاملات المباشرة للكائن الأصلي في الذاكرة (Mutation)
function applyDiscount(item, discount) {
  item.price = item.price - discount; // يعدل الكائن الأصلي المخزن في الذاكرة!
  return item;
}

// ✅ كود ممتاز: إرجاع كائن جديد تماماً مع الحفاظ على البيانات الأصلية
function applyDiscount(item, discount) {
  return {
    ...item,
    price: item.price - discount
  };
}

// ❌ كود سيئ: استخدام حلقة تقليدية مع مصفوفة مؤقتة ودفع يدوي
const activeNames = [];
for (let i = 0; i < users.length; i++) {
  if (users[i].isActive) {
    activeNames.push(users[i].name);
  }
}

// ✅ كود ممتاز: استخدام السلسلة الوظيفية المباشرة (Chaining) بـ filter و map
const activeNames = users
  .filter(user => user.isActive)
  .map(user => user.name);
```

---

## 📝 7. الخلاصة والدرس التالي
- استخدم **الدوال السهمية** لكتابة دوال دوال رد نداء (Callbacks) سريعة ومعبرة.
- اعتمد على **`map`**, **`filter`**, و **`reduce`** لمعالجة البيانات دون المساس بالمصفوفات الأصلية.
- استفد من **التفكيك (Destructuring)** لتبسيط استخراج الخصائص ومعاملات الدوال.
- وظّف **معامل النشر (`...`)** لنسخ ودمج البيانات بأسلوب الـ Immutability.
- في الدرس التالي، سنغوص في **سياق التنفيذ ونطاق المتغيرات والـ Hoisting (Scope, Hoisting & Execution Context)**.
