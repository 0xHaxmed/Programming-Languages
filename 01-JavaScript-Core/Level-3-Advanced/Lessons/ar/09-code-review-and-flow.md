# 09 - هندسة مراجعة الكود وتدفق البيانات: التحليل الساكن، إعادة الهيكلة وروائح الكود

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/09-code-review-and-flow.md) | [العربية 🇸🇦](./09-code-review-and-flow.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

كتابة كود برمجي يعمل ويؤدي الغرض فقط هي مجرد البداية وليست المعيار الحقيقي في هندسة البرمجيات الاحترافية. في فرق العمل والشركات الكبرى، يعتمد نجاح المشروع واستمراريته على قابلية الصيانة (Maintainability)، سهولة القراءة (Readability)، وسلاسة تدفق البيانات دون تعقيد غير ضروري. تهدف مراجعة الكود (Code Review) إلى تتبع مسارات البيانات من المصادر غير الموثوقة، خفض التعقيد الحسابي، والقضاء على روائح الكود (Code Smells) قبل اعتماد التعديلات ودمجها في بيئة الإنتاج.

بنهاية هذا الدرس، ستتقن تماماً:
1. إجراء **تحليل تدفق البيانات الساكن (Static Data Flow Analysis)** من المصادر (Sources) إلى مصبات العمليات (Sinks).
2. تطبيق المبادئ المعمارية الجوهرية: **المسؤولية الواحدة (SRP)** وفصل الاهتمامات (**Separation of Concerns**) في جافاسكريبت.
3. قياس وتقليص **التعقيد الدوري (Cyclomatic Complexity)** باستخدام شروط الحماية (Guard Clauses).
4. التعرف على أشهر روائح الكود (God Functions، Primitive Obsession، معاملات القيم المنطقية الغامضة، والتعديل الخفي على الكائنات) وإعادة هيكلتها (Refactoring).
5. تطبيق قائمة فحص مراجعة الكود الاحترافية وفق معايير الشركات العالمية (Airbnb-Style Checklist).

---

## 🌊 2. تحليل تدفق البيانات الساكن: من المصادر إلى المصبات

يتتبع تحليل تدفق البيانات كيف تدخل المعلومة إلى البرنامج، وكيف تتحول عبر الطبقات المختلفة، وأين تستقر في النهاية:

```
[ مصدر غير موثوق SOURCE ]           [ خط أنابيب المعالجة والتحويل ]              [ المصب النهائي SINK ]
مدخلات المستخدم / معاملات الرابط  --->  التعقيم -> التحليل -> التحقق من الصحة  --->  شجرة DOM / قاعدة البيانات
(بيانات قد تحوي هجمات)                 (منطقة الدفاع والحماية الصارمة)             (عملية ذات أثر حاسم)
```

### القواعد الثلاث لتدفق بيانات نظيف وآمن:
1. **لا تثق أبداً بالبيانات عند الحدود الخارجية:** أي بيانات قادمة من نماذج الإدخال، أو روابط الـ URL، أو رسائل `postMessage` هي بيانات غير موثوقة افتراضياً.
2. **تحقق فوراً وافحص بدقة:** قم بالتحقق من هيكل البيانات وصحتها (Validation) عند الحافة الخارجية مباشرة قبل تمريرها لمنطق الأعمال الداخلي.
3. **اعزل مصبات التأثير الحاسم:** اجعل عمليات تخزين البيانات (مثل `localStorage.setItem` أو استعلامات الشبكة) خلف واجهات ومستودعات محددة (Repositories) بدلاً من بعثرتها عشوائياً داخل مستمعات أحداث الواجهة.

---

## 👃 3. أشهر روائح الكود (Code Smells) وطرق إعادة هيكلتها

### أ. رائحة الكود: "الدالة الخارقة" (God Function)
دالة تفعل كل شيء في نفس الوقت: تجمع بيانات النموذج، وتفحص صحتها، وترسل طلب الشبكة، وتعدل التخزين المحلي، وتحدث شجرة DOM، وترسل بيانات التحليلات!

```javascript
// ❌ سيء: دالة خارقة تقوم بـ 5 مسؤوليات مختلفة تماماً
async function handleUserSignup(e) {
  e.preventDefault();
  const email = document.getElementById('email').value;
  const password = document.getElementById('pass').value;

  if (!email.includes('@') || password.length < 8) {
    alert('بيانات غير صحيحة');
    return;
  }

  const res = await fetch('/api/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  const data = await res.json();

  localStorage.setItem('user_token', data.token);
  document.getElementById('welcome').innerText = `أهلاً بك، ${data.user.name}`;
  analytics.track('signup_success', { userId: data.user.id });
}
```

```javascript
// ✅ جيد: تفكيك الكود إلى وحدات صغيرة ومستقلة وقابلة للاختبار الفردي
// 1. منطق فحص صحة المدخلات (دالة نقية Pure Function):
function validateSignupForm(email, password) {
  if (!email || !email.includes('@')) return { valid: false, error: 'البريد الإلكتروني غير صالح' };
  if (!password || password.length < 8) return { valid: false, error: 'كلمة المرور قصيرة جداً' };
  return { valid: true };
}

// 2. عميل الشبكة للاتصال بالخادم (Data Access Layer):
async function signupUserApi(credentials) {
  const res = await fetch('/api/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(credentials)
  });
  if (!res.ok) throw new Error('فشلت عملية إنشاء الحساب');
  return res.json();
}

// 3. منسق العمليات (يربط الواجهة بمنطق الأعمال):
async function onSignupSubmitted(formElements) {
  const { email, password } = formElements;
  const validation = validateSignupForm(email, password);
  if (!validation.valid) return ui.showError(validation.error);

  try {
    const session = await signupUserApi({ email, password });
    authStorage.setToken(session.token);
    ui.renderWelcome(session.user.name);
    analytics.track('signup_success', { userId: session.user.id });
  } catch (err) {
    ui.showError(err.message);
  }
}
```

---

### ب. رائحة الكود: تمرير معاملات القيم المنطقية الغامضة (Boolean Flags)
تمرير قيم منطقية مجردة `fn(user, true, false, true)` يجعل مراجعة الكود مستحيلة، حيث لا يعرف أي مراجع ماذا تعني `true` أو `false` دون الذهاب لملف تعريف الدالة نفسه.

```javascript
// ❌ سيء: ماذا تعني true و false هنا؟ كود غامض تماماً
createUser('Ahmed', true, false, true);

// ✅ جيد: إعادة الهيكلة باستخدام كائن خيارات صريح (Options Object)
function createUser(name, { isAdmin = false, sendWelcomeEmail = true, requireMfa = false } = {}) {
  // ...
}

// المعنى في سطر الاستدعاء شديد الوضوح لأي مراجع:
createUser('Ahmed', {
  isAdmin: true,
  sendWelcomeEmail: false,
  requireMfa: true
});
```

---

### ج. رائحة الكود: التعديل المباشر على الكائنات الممررة (In-Place Mutation)
تعديل الكائنات داخل الدوال بدلاً من إنشاء نسخ جديدة يؤدي إلى أخطاء كارثية يصعب تتبعها في البيئات غير المتزامنة.

```javascript
// ❌ سيء: يعدل على الكائن الأصلي ويحدث آثاراً جانبية غير مرئية
function applyDiscount(cart, discountPercent) {
  cart.items.forEach((item) => {
    item.price = item.price * (1 - discountPercent); // تعديل مباشر في الذاكرة!
  });
  cart.discountApplied = true;
  return cart;
}

// ✅ جيد: إرجاع كائن جديد مع الحفاظ على الكائن الأصلي دون تشويه (Immutability)
function applyDiscountPure(cart, discountPercent) {
  const multiplier = 1 - discountPercent;
  return {
    ...cart,
    discountApplied: true,
    items: cart.items.map((item) => ({
      ...item,
      price: Math.round(item.price * multiplier * 100) / 100
    }))
  };
}
```

---

## 📉 4. خفض التعقيد الدوري (Guard Clauses)

يقيس **التعقيد الدوري (Cyclomatic Complexity)** عدد المسارات البرمجية المتفرعة داخل الكود. كلما زاد هذا الرقم، كلما تضاعفت احتمالية وجود أخطاء، وصار اختبار الكود شبه مستحيل.

### هرم الشروط المتداخلة (Arrow Anti-Pattern):
```javascript
// ❌ سيء: هرم متداخل من الشروط يعقد التتبع (High Complexity)
function processPayment(user, order) {
  if (user) {
    if (user.isActive) {
      if (order) {
        if (order.total > 0) {
          if (user.balance >= order.total) {
            executeCharge(user, order);
            return { success: true };
          } else {
            return { success: false, reason: 'الرصيد غير كافٍ' };
          }
        } else {
          return { success: false, reason: 'إجمالي الطلب غير صحيح' };
        }
      } else {
        return { success: false, reason: 'الطلب مفقود' };
      }
    } else {
      return { success: false, reason: 'الحساب غير نشط' };
    }
  } else {
    return { success: false, reason: 'المستخدم غير موجود' };
  }
}
```

### الحل: شروط الحماية والإنهاء المبكر (Guard Clauses):
```javascript
// ✅ جيد: كود مسطح ومقروء وسهل الاختبار بنسبة 100%
function processPayment(user, order) {
  if (!user) return { success: false, reason: 'المستخدم غير موجود' };
  if (!user.isActive) return { success: false, reason: 'الحساب غير نشط' };
  if (!order) return { success: false, reason: 'الطلب مفقود' };
  if (order.total <= 0) return { success: false, reason: 'إجمالي الطلب غير صحيح' };
  if (user.balance < order.total) return { success: false, reason: 'الرصيد غير كافٍ' };

  executeCharge(user, order);
  return { success: true };
}
```

---

## 📋 5. قائمة فحص مراجعة الكود الاحترافية (Airbnb-Style Checklist)

عند مراجعة أي Pull Request، اتبع المحاور الأربعة التالية:

### 1. الصحة والمعمارية البرمجية (Correctness & Architecture)
- [ ] هل تلتزم كل دالة بمبدأ المسؤولية الواحدة (SRP)؟
- [ ] هل تم تمرير التبعيات الخارجية أو حقنها بدلاً من تثبيتها يدوياً؟
- [ ] هل تم التعامل مع أخطاء الوعود عبر `try/catch` وتفادي الوعود المنسية؟
- [ ] هل يتم فحص صحة المدخلات قبل معالجتها في منطق الأعمال؟

### 2. قابلية القراءة والصيانة (Readability & Maintainability)
- [ ] هل أسماء المتغيرات والدوال واضحة وتكشف الهدف منها (مثل `isUserAuthenticated` بدلاً من `chk`)؟
- [ ] هل تم استخراج "الأرقام السحرية" كـ ثوابت مسماة (`const MAX_ATTEMPTS = 5`)؟
- [ ] هل تم استبدال المعاملات المنطقية المتعددة بكائن خيارات مسمى (Options Object)؟
- [ ] هل تم استبدال الشروط المتداخلة بشروط الحماية (Guard Clauses)؟

### 3. الأداء والذاكرة (Performance & Memory)
- [ ] هل يتم حذف مستمعات الأحداث (Event Listeners) عند عدم الحاجة إليها؟
- [ ] هل تم تقنين استدعاءات التمرير والبحث السريع عبر Debounce أو Throttle؟
- [ ] هل يتفادى الكود إنشاء كائنات ضخمة متكررة داخل الحلقات التكرارية السريعة؟

### 4. الأمان الدفاعي (Security & Defensive Rigor)
- [ ] هل تم تجنب الإدخال المباشر للنصوص داخل `innerHTML` دون تعقيم؟
- [ ] هل الملفات خالية تماماً من الرموز السرية أو مفاتيح API الخاصة؟
- [ ] هل تم وضع قيود على أحجام المدخلات لحماية الخادم من التجميد أو ثغرات ReDoS؟

---

## 🧼 6. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: أرقام سحرية ومتغيرات مبهمة يصعب فهمها
if (u.st === 1 && d.diff > 86400000) {
  p(u.id);
}

// ✅ جيد: ثوابت صريحة وأسماء مقروءة تعبر عن نفسها
const MILLISECONDS_IN_A_DAY = 24 * 60 * 60 * 1000;
const USER_STATUS_ACTIVE = 1;

const isSubscriptionOverdue = user.status === USER_STATUS_ACTIVE && timeDifference > MILLISECONDS_IN_A_DAY;
if (isSubscriptionOverdue) {
  promptAccountRenewal(user.id);
}
```

---

## 📝 7. ملخص الدرس والخطوة القادمة

- تتبع تدفق البيانات دائماً من **المصادر** (Sources) إلى **المصبات** (Sinks) لضمان الأمان والوضوح.
- اقضِ على **الدوال الخارقة** عبر الفصل الدقيق بين التحقق، واتصالات الشبكة، وإدارة الواجهة.
- استبدل المعاملات المنطقية المبهمة بـ **كائنات الخيارات المحددة (Options Objects)**.
- احرص على مبدأ الثبات وتفادي التعديل المباشر على الكائنات في الذاكرة.
- بسط التعقيد الدوري وحوّل الأهرامات المتداخلة إلى **شروط حماية مسطحة (Guard Clauses)**.
- التزم بمحاور المراجعة الأربعة لضمان جودة الكود البرمجي في الفريق.
- في الدرس القادم، سنتعمق في **هندسة الاختبارات والتحقق: محرك اختبارات Node.js المدمج والمحاكاة (Mocking)**.
