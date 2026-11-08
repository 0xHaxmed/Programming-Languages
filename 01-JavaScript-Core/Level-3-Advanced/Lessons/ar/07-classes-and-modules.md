# 07 - الفئات والموديولات: التغليف والحقول الخاصة ومعمارية ESM

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/07-classes-and-modules.md) | [العربية 🇸🇦](./07-classes-and-modules.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تعتمد المشاريع الضخمة والحديثة في جافاسكريبت على ركيزتين معماريتين لتنظيم الكود: **فئات ES6 Classes** للنمذجة الكائنية (Object-Oriented Programming)، و**نظام الموديولات القياسي (ECMAScript Modules - ESM)** لعزل وتوزيع الأكواد عبر ملفات مستقلة.

ورغم أن الفئات (Classes) في جافاسكريبت هي مجرد غطاء نحوي (Syntactic Sugar) فوق وراثة النماذج الأولية (Prototypes)، إلا أن الإضافات الحديثة مثل **الحقول الخاصة الصارمة (`#`)** منحت اللغة قدرة حقيقية على التغليف (Encapsulation). وفي الوقت ذاته، أصبحت معمارية ESM هي المعيار العالمي الموحد في كل من المتصفحات وبيئة Node.js.

بنهاية هذا الدرس، ستتقن تماماً:
1. تفكيك البنية النحوية لـ `class` في جافاسكريبت إلى طبقات الـ Prototype التي تكمن خلفها.
2. الوراثة وإنشاء الفئات الفرعية باستخدام `extends` واستدعاء `super()`.
3. تطبيق التغليف الصارم وحماية البيانات باستخدام **الحقول الخاصة (`#field`)** والدوال الخاصة (`#method`).
4. الخصائص الساكنة (Static Properties) وكتل التهيئة الساكنة (`static { ... }`).
5. معمارية موديولات ESM: التصدير بالاسم (Named Exports) مقابل التصدير الافتراضي (Default)، الاستيراد الديناميكي `import()`، وميزة `await` على مستوى الملف الأعلى (Top-Level await).
6. تشخيص مشكلة الاعتماد الدائري (Circular Dependency) وحلها معمارياً.

---

## 🏛️ 2. فئات ES6: غطاء نحوي فوق الـ Prototypes

تحت الغطاء، لا تمتلك جافاسكريبت نظام وراثة كلاسيكي صارم مثل Java أو C++. تصريح `class` يقوم في الحقيقة بإنشاء دالة بناء (Constructor Function) ويربط الدوال المعرفة بداخلها بالكائن `ClassName.prototype`.

```javascript
// صيغة الفئات الحديثة في ES6+:
class BankAccount {
  constructor(owner, initialBalance = 0) {
    this.owner = owner;
    this.balance = initialBalance;
  }

  deposit(amount) {
    if (amount <= 0) throw new Error('يجب أن يكون المبلغ المودع موجباً');
    this.balance += amount;
    return this.balance;
  }
}

// الكود المكافئ له تماماً بنظام النماذج الأولية القديم (ES5 Prototype):
function BankAccountES5(owner, initialBalance = 0) {
  this.owner = owner;
  this.balance = initialBalance;
}
BankAccountES5.prototype.deposit = function (amount) {
  if (amount <= 0) throw new Error('يجب أن يكون المبلغ المودع موجباً');
  this.balance += amount;
  return this.balance;
};

// إثبات التطابق التام تحت الغطاء:
const account = new BankAccount('Alice', 100);
console.log(typeof BankAccount); // "function"
console.log(account.__proto__ === BankAccount.prototype); // true
console.log(BankAccount.prototype.deposit === account.deposit); // true
```

### الفروقات الجوهرية بين `class` ودوال البناء العادية:
1. **غياب الرفع (No Hoisting):** تقع الفئات في المنطقة الميتة زمنياً (TDZ)، ولا يمكن استخدامها قبل سطر تعريفها.
2. **الوضع الصارم التلقائي (Strict Mode):** كل سطر داخل جسم الـ `class` يعمل تلقائياً تحت وضع `'use strict'`.
3. **منع الاستدعاء دون `new`:** محاولة استدعاء `BankAccount()` مباشرة دون معامل `new` يرمي فوراً خطأ: `TypeError: Class constructor cannot be invoked without 'new'`.

---

## 🔒 3. التغليف الحقيقي: الحقول والدوال الخاصة (`#`)

تاريخياً، كان المطورون يضعون شرطة سفلية في بداية اسم المتغير (مثل `_balance`) للدلالة على أنه خاص. لكن ذلك كان مجرد اتفاق شكلي بلا أي حماية برمجية حقيقية—فأي كود خارجي كان قادراً على قراءة وتعديل `_balance`.

أضافت معايير ECMAScript الحديثة **الحقول الخاصة الصارمة (Hard Private Fields)** التي تبدأ برمز الشباك `#`. هذه الحقول محمية على مستوى المحرك (V8 Engine) ومستحيل الوصول إليها من خارج الفئة:

```javascript
class SecureVault {
  // 1. تعريف الحقول الخاصة الصارمة في قمة الفئة:
  #encryptionKey;
  #accessPin;
  #auditLogs = [];

  constructor(secretKey, pin) {
    this.#encryptionKey = secretKey;
    this.#accessPin = pin;
  }

  // 2. دالة خاصة (Private Method) غير متاحة للخارج:
  #recordAccess(action, success) {
    this.#auditLogs.push({ action, success, timestamp: Date.now() });
  }

  // 3. دالة عامة (Public Method) للتعامل مع الخزنة:
  unlock(pinAttempt) {
    if (pinAttempt !== this.#accessPin) {
      this.#recordAccess('unlock', false);
      throw new Error('فشل المصادقة: رقم PIN غير صحيح');
    }

    this.#recordAccess('unlock', true);
    return `تم فتح الخزنة: [المفتاح: ${this.#encryptionKey.slice(0, 4)}****]`;
  }

  // دالة قراءة لعدد السجلات فقط (Getter):
  get logCount() {
    return this.#auditLogs.length;
  }
}

const myVault = new SecureVault('SUPER_SECRET_KEY_999', '1234');
console.log(myVault.unlock('1234')); // "تم فتح الخزنة: [المفتاح: SUPE****]"

// ❌ محاولة قراءة الحقل الخاص من الخارج تفشل وتولد خطأ أثناء الترجمة والتشغيل:
// console.log(myVault.#accessPin);
// SyntaxError: Private field '#accessPin' must be declared in an enclosing class

// التكرار وفحص خصائص الكائن يتجاهل الحقول الخاصة تماماً:
console.log(Object.keys(myVault)); // []
```

---

## 🧬 4. الوراثة وكتل التهيئة الساكنة (Static Blocks)

```javascript
class PaymentGateway {
  static #supportedCurrencies = new Set(['USD', 'EUR', 'SAR', 'EGP']);

  // كتلة تهيئة ساكنة (تعمل مرة واحدة فقط عند تحميل الفئة في الذاكرة):
  static {
    if (process.env.ENABLE_AED === 'true') {
      PaymentGateway.#supportedCurrencies.add('AED');
    }
  }

  constructor(merchantId) {
    this.merchantId = merchantId;
  }

  static isCurrencySupported(currency) {
    return PaymentGateway.#supportedCurrencies.has(currency);
  }
}

class StripeGateway extends PaymentGateway {
  #apiKey;

  constructor(merchantId, apiKey) {
    // يجب استدعاء super() أولاً قبل استخدام كلمة this:
    super(merchantId);
    this.#apiKey = apiKey;
  }

  processPayment(amount, currency) {
    if (!PaymentGateway.isCurrencySupported(currency)) {
      throw new Error(`العملة غير مدعومة: ${currency}`);
    }
    return `تمت معالجة ${amount} ${currency} عبر بوابة Stripe للتاجر ${this.merchantId}`;
  }
}
```

---

## 📦 5. معمارية موديولات جافاسكريبت (ESM Architecture)

تُعد موديولات ECMAScript (ESM) النظام المعياري الرسمي لتقسيم الأكواد. تعمل ملفات ESM في نطاق معزول (Module Scope)، وتُحمل كنسخ مفردة (Singletons) مرة واحدة في الذاكرة.

### أ. التصدير بالاسم (Named Exports) مقابل التصدير الافتراضي (Default):
```javascript
// mathUtils.js (ملف الموديول المصدر)
export const PI = 3.14159265359;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

// التصدير الافتراضي (يُفضل استخدامه بحذر ومحدودية):
export default class Calculator {
  // ...
}
```

```javascript
// app.js (ملف الاستهلاك)
// 1. استيراد العناصر المحددة بأسمائها:
import Calculator, { add, multiply, PI } from './mathUtils.js';

// 2. استيراد كل الصادرات تحت نطاق موحد (Namespace):
import * as MathOps from './mathUtils.js';
console.log(MathOps.add(10, 20));
```

### ب. الاستيراد الديناميكي (Dynamic `import()`) لتقسيم الكود (Code Splitting):
عبارات `import` الثابتة يجب أن توضع في أعلى الملف. عندما تحتاج إلى موديول ثقيل فقط عند تفاعل محدد من المستخدم (مثل النقر على "تصدير تقرير Excel")، استخدم **`import()` الديناميكي**:

```javascript
async function exportReport(data) {
  // لا يتم تحميل هذا الملف وحجزه في الذاكرة إلا عند استدعاء الدالة:
  const { generateExcelFile } = await import('./heavyExcelExportBundle.js');
  generateExcelFile(data);
}
```

### ج. الـ `await` في قمة الملف (Top-Level `await`):
في ملفات ESM الحديثة، لا تحتاج إلى تغليف كود التهيئة داخل دالة `async`. يمكنك استخدام `await` مباشرة في أعلى الملف لانتظار تهيئة قاعدة البيانات أو تحميل الإعدادات:

```javascript
// dbClient.js
const res = await fetch('https://api.internal/config');
export const dbConfig = await res.json();
```

---

## 🔄 6. معضلة الاعتماد الدائري (Circular Dependencies)

يحدث الاعتماد الدائري عندما يقوم `moduleA.js` باستيراد `moduleB.js`، وفي نفس الوقت يقوم `moduleB.js` باستيراد `moduleA.js`.

```
[ moduleA.js ]  <=============>  [ moduleB.js ]
(يستورد B)                       (يستورد A)
```

في نظام ESM، لا يتسبب الاعتماد الدائري في انهيار مفاجئ للبرنامج، لأن الموديولات تعتمد على روابط حية (Live Bindings) ويتم تجهيزها قبل التنفيذ. ولكن المشكلة تكمن في أن الموديول المستورد قد يقرأ قيمة **`undefined`** لأن المتغير المطلوب لم ينتهِ من مرحلة التهيئة بعد (TDZ).

### الحل المعماري: فصل الأنواع المشتركة أو استخدام حقن التبعيات (Dependency Injection)
```javascript
// ❌ خطأ: اعتماد دائري متبادل
// user.js يستورد orderService.js
// orderService.js يستورد user.js

// ✅ صحيح: فصل العقود والأنواع المشتركة
// 1. types.js (يحتوي الثوابت والواجهات المشتركة)
// 2. userService.js (يستورد types.js)
// 3. orderService.js (يستورد types.js ويستقبل userService عبر الـ Constructor)
```

---

## 🧼 7. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: سلاسل وراثة عميقة ومتشابكة (Fragile Base Class Problem)
class Animal {}
class Mammal extends Animal {}
class Canine extends Mammal {}
class DomesticDog extends Canine {}
class GoldenRetriever extends DomesticDog {}

// ✅ جيد: تفضيل التركيب على الوراثة (Composition over Inheritance)
class Dog {
  constructor(name, abilities = []) {
    this.name = name;
    this.abilities = abilities;
  }

  perform() {
    this.abilities.forEach((ability) => ability.execute(this));
  }
}

// ❌ سيء: التصدير الافتراضي لكائنات مجهولة الهوية (يضر الـ Tree-Shaking والـ Refactoring)
export default {
  getUser,
  updateUser
};

// ✅ جيد: التصدير الصريح بالأسماء لسهولة الإكمال التلقائي وتقليص حجم الحزمة
export { getUser, updateUser };
```

---

## 📝 8. ملخص الدرس والخطوة القادمة

- صيغة `class` في جافاسكريبت هي غلاف فوق الـ Prototype، لكنها تفرض الوضع الصارم وتمنع الاستدعاء دون `new`.
- **الحقول الخاصة (`#field`)** توفر خصوصية حقيقية غير قابلة للاختراق على مستوى المحرك.
- يجب استدعاء `super()` أولاً داخل دوال البناء الفرعية قبل الإشارة إلى `this`.
- يُفضل دائماً استخدام **التصدير بالاسم (Named Exports)** لتسهيل الـ Tree-Shaking والإكمال التلقائي.
- استخدم **`import()` الديناميكي** لتقسيم الحزم البرمجية وتحميل الأكواد الثقيلة عند الحاجة فقط.
- تجنب سلاسل الوراثة العميقة واعتمد على مبدأ التركيب (Composition).
- في الدرس القادم، سنتناول **هندسة إدارة الأخطاء وتحليلها: التسلسل، مكدس الاستدعاءات (Stack Traces) ومعالجة الانهيارات العالمية**.
