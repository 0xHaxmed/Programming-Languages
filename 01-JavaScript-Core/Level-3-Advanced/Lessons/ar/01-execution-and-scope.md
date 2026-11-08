# 01 - سياق التنفيذ والبيئات المعجمية والمغلفات (Execution Context & Closures)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/01-execution-and-scope.md) | [العربية 🇸🇦](./01-execution-and-scope.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في المستوى الهندسي المتقدم، لا تُعامل جافا سكريبت كمجرد أسطر كود تُقرأ تتابعياً؛ بل هي منظومة محكمة يديرها محرك التشغيل عبر **سياقات التنفيذ (Execution Contexts)**، **البيئات المعجمية (Lexical Environments)**، و **المغلفات البرمجية (Closures)** المحجوزة في الذاكرة.

إن فهم كيفية تخصيص المحرك (مثل V8 أو SpiderMonkey) لإطارات النطاق على مكدس الاستدعاءات (Call Stack) واحتفاظه بالمتغيرات المغلقة داخل الـ Heap أمر جوهري لبناء معماريات برمجية عالية الأداء وخالية من تسريب الذاكرة (Memory Leaks).

بنهاية هذا الدرس، ستتقن:
1. البنية الهيكلية لـ **سياق التنفيذ (Execution Context)** والبيئة المعجمية (Lexical Environment).
2. آليات **حل المتغيرات المعجمي (Lexical Scope Resolution)** عبر سلسلة النطاقات.
3. ما هو **المغلف البرمجي (Closure)** من المبادئ الأولية لميكانيكا الذاكرة.
4. تطبيق أنماط معمارية متقدمة باستخدام الـ Closures: **إخفاء البيانات (Encapsulation)**، **مصانع الدوال (Factory Functions)**، و **التخزين المؤقت للنتائج (Memoization)**.
5. تشخيص ومعالجة **تسريبات الذاكرة الناتجة عن المغلفات (Closure Memory Leaks)**.

---

## ⚙️ 2. تشريح سياق التنفيذ (Execution Context Anatomy)

عندما يعمل كود جافا سكريبت، فإنه ينفذ دائماً داخل **سياق تنفيذ (Execution Context)**. هناك ثلاثة أنواع:
1. **سياق التنفيذ العام (Global Execution Context - GEC):** ينشأ مرة واحدة عند بدء تشغيل البرنامج.
2. **سياق تنفيذ الدالة (Function Execution Context - FEC):** ينشأ ديناميكياً في كل مرة يتم فيها استدعاء دالة.
3. **سياق Eval:** ينشأ داخل استدعاء `eval()` (غير محبذ استخدامه).

```
+-----------------------------------------------------------------------------------+
|                        سياق تنفيذ الدالة (FUNCTION EXECUTION CONTEXT)             |
+-----------------------------------------------------------------------------------+
| 1. البيئة المعجمية (Lexical Environment):                                          |
|    - سجل البيئة (Environment Record): يخزن متغيرات let و const وتصريحات الدوال.    |
|    - الرابط الخارجي (Outer Reference): يشير للبيئة المعجمية الأب (Lexical Parent). |
|                                                                                   |
| 2. بيئة المتغيرات (Variable Environment):                                         |
|    - تخزن متغيرات var التقليدية ومعاملات الدالة.                                   |
|                                                                                   |
| 3. ارتباط كلمة 'this': يُحدد ديناميكياً حسب طريقة استدعاء الدالة.                 |
+-----------------------------------------------------------------------------------+
```

---

## 🧬 3. ما هو الـ Closure من المبادئ الأولية للذاكرة؟

> [!NOTE]
> **التعريف العلمي الدقيق:**  
> **المغلف البرمجي (Closure)** هو حزمة تجمع بين دالة داخلية والبيئة المعجمية المحيطة بها (Lexical Environment).  
> في جافا سكريبت، تتيح الـ Closures للدالة الداخلية الاحتفاظ بإمكانية الوصول لمتغيرات الدالة الأب الحاوية لها **حتى بعد أن تنتهي الدالة الأب وتخرج تماماً من مكدس الاستدعاءات (Call Stack)**!

### ميكانيكا إدارة الذاكرة:
في لغات مثل C، تُحجز المتغيرات المحلية داخل الـ Stack وتُدمر فور انتهاء الدالة.  
أما في جافا سكريبت، إذا أشارت دالة داخلية إلى متغير في دالة أب، فإن المحرك ينقل ذلك المتغير من **الـ Stack إلى ذاكرة الكومة (Memory Heap)**، محتفظاً به طالما أن الدالة الداخلية ما زال هناك مرجع يشير إليها!

```javascript
function createCounter(initialValue = 0) {
  // المتغير count يُحجز داخل الـ Heap كجزء من البيئة المعجمية المغلقة:
  let count = initialValue;

  return {
    increment() {
      count++;
      return count;
    },
    decrement() {
      count--;
      return count;
    },
    getCount() {
      return count;
    }
  };
}

const counterA = createCounter(10);
console.log(counterA.increment()); // 11
console.log(counterA.increment()); // 12
console.log(counterA.getCount());  // 12

// المتغير 'count' محمي وسري تماماً (Private State)!
// لا توجد أي طريقة نحوية في اللغة لقراءة أو تعديل count من الخارج إلا عبر الدوال المعادة!
console.log(counterA.count); // undefined
```

---

## 🏛️ 4. أنماط التصميم المؤسسية باستخدام الـ Closures

### 1. إخفاء البيانات وحمايتها (Encapsulation / Module Pattern)
قبل ظهور الحقول الخاصة في كلاسات ES2022 (`#`)، كانت الـ Closures هي الطريقة القياسية المعتمدة لعزل البيانات الحساسة:

```javascript
function createSecureTokenManager() {
  let authToken = null; // مخفي تماماً عن كائن window وعن فحص أدوات المطور الخارجية

  return {
    setToken(token) {
      if (typeof token === 'string' && token.length > 20) {
        authToken = token;
      }
    },
    hasValidToken() {
      return authToken !== null;
    },
    clear() {
      authToken = null;
    }
  };
}

const authManager = createSecureTokenManager();
authManager.setToken('secure_jwt_token_abcdef123456');
console.log(authManager.hasValidToken()); // true
```

### 2. التخزين المؤقت عالي الأداء للدوال (Memoization)
تتيح الـ Closures تخزين نتائج العمليات الحسابية الثقيلة مؤقتاً دون تلويث النطاق العام:

```javascript
function memoize(fn) {
  const cache = new Map(); // محفوظ داخل نطاق الـ Closure

  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      console.log('[استرجاع من الذاكرة المؤقتة Cache]');
      return cache.get(key);
    }
    console.log('[حساب النتيجة لأول مرة...]');
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

const heavyMath = (a, b) => a ** b;
const fastMath = memoize(heavyMath);

console.log(fastMath(2, 10)); // حساب النتيجة... 1024
console.log(fastMath(2, 10)); // [استرجاع من الذاكرة المؤقتة] 1024
```

---

## ⚠️ 5. تسريبات الذاكرة الناتجة عن الـ Closures

يحتفظ المغلف بالبيئة المعجمية بالكامل. إذا احتفظ مغلف طويل البقاء بمرجع لمتغير خارجي يحمل مصفوفات ضخمة أو عقد DOM، فإن جامع المهملات (Garbage Collector) **يعجز عن حذفها من الذاكرة**:

```javascript
// ❌ تسريب ذاكرة: الاحتفاظ بمصفوفة ضخمة في الذاكرة للأبد
function attachSearchListener() {
  const giantDataset = new Array(1000000).fill('بيانات ثقيلة'); // تستهلك ~40MB في الذاكرة

  window.addEventListener('scroll', () => {
    // هذا المغلف يحتجز giantDataset في الـ Heap حتى لو لم يستخدمه!
    console.log('المستخدم يقوم بالتمرير');
  });
}
```

### الحل الهندسي:
تصفير المتغيرات غير المستخدمة أو حصرها في كتل برمجية داخلية مستقلة:
```javascript
// ✅ كود سليم: لا يحتجز أي بيانات غير ضرورية في النطاق الخارجي
function attachSearchListener() {
  window.addEventListener('scroll', () => {
    console.log('المستخدم يقوم بالتمرير');
  });
}
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: إنشاء Closures داخل حلقات تكرار باستخدام var (أشهر فخ في المقابلات البرمجية)
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // يطبع: 3, 3, 3! (كل الدوال تشترك في نفس المرجع لـ var i)
  }, 100);
}

// ✅ كود ممتاز: استخدام let لإنشاء ربط معجمي (Lexical Binding) مستقل في كل دورة
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // يطبع: 0, 1, 2! (كل دالة تحتجز نسختها الخاصة من let i)
  }, 100);
}
```

---

## 📝 7. الخلاصة والدرس التالي
- **سياق التنفيذ (Execution Context)** يتابع البيئة المعجمية، وسجل المتغيرات، وارتباط كائن `this`.
- **المغلف (Closure)** يتيح للدوال الاحتفاظ بروابط نطاقها الأصلي حتى بعد خروج الدالة الحاوية من الـ Call Stack.
- تُستخدم المغلفات لتحقيق **عزل وحماية البيانات**، و **مصانع الدوال**، والـ **Memoization**.
- احذر من تسريب الذاكرة عندما تعيش الـ Closures لفترة أطول من المكونات التابعة لها.
- في الدرس التالي، سنشرح واحدة من أكثر آليات جافا سكريبت شهرة وإثارة للجدل: **الكلمة المفتاحية `this` وقواعد الارتباط الأربعة (The `this` Keyword & 4 Binding Rules)**.
