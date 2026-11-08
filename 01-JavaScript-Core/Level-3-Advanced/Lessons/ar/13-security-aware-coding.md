# 13 - البرمجة المراعية للأمان: حماية المصبات، التعقيم والهندسة الدفاعية

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/13-security-aware-coding.md) | [العربية 🇸🇦](./13-security-aware-coding.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

الأمان البرمجي ليس ميزة ثانوية تُضاف إلى الكود بعد اكتماله؛ بل هو انضباط معماري صارم ومنهجية تفكير يجب أن ترافق كل سطر برمجي. ونظراً لأن تطبيقات جافاسكريبت الحديثة تدير جلسات المستخدمين الحساسة، ورموز المصادقة، والعمليات المالية، والبيانات المشفرة، يجب على المطورين كتابة أكواد محصنة بطبيعتها ومقاومة للهجمات منذ اللحظة الأولى.

بنهاية هذا الدرس الختامي، ستتقن تماماً:
1. حظر واجتثاث **مصبات تنفيذ الأكواد الديناميكية الخطيرة (Dynamic Execution Sinks)** مثل `eval` و `new Function`.
2. التحصين ضد ثغرات **حقن الأوامر في المتصفح (DOM XSS)** باستخدام الخصائص الآمنة وواجهة **Trusted Types API** القياسية.
3. حماية الكائنات البرمجية والدوال التكرارية من ثغرة **تلوث النماذج الأولية (Prototype Pollution)**.
4. تأمين الاتصال المتبادل بين النوافذ والإطارات عبر **`postMessage`** والتحقق الصارم من الأصل (Origin).
5. استخدام واجهة التشفير الرسمية **Web Crypto API** لتوليد العشوائية المشفرة وتأمين الرموز البرمجية.

---

## 🚫 2. القضاء على مصبات تنفيذ الأكواد الديناميكية (Dynamic Evaluation)

تقوم أدوات التنفيذ الديناميكي بتحويل النصوص العادية إلى أكواد برمجية قابلة للتشغيل. وإذا كان المهاجم قادراً على التحكم في أي جزء من هذا النص، فإنه يحصل فوراً على صلاحية تنفيذ كود جافاسكريبت عشوائي داخل متصفح الضحية.

### المصبات الخطيرة التي يجب حظرها نهائياً:
```javascript
// ❌ مصب شديد الخطورة: ينفذ النص مباشرة ككود جافاسكريبت
eval(userInput);

// ❌ مصب كارثي: يبني دالة برمجية جديدة من النص
const calculator = new Function('a', 'b', `return ${formulaString};`);

// ❌ خطر ومحظور: تمرير نصوص للمؤقتات الزمنية
setTimeout(`alert("${message}")`, 1000); // يستدعي eval داخلياً!
```

### البدائل الآمنة والمعمارية:
```javascript
// ✅ آمن: تمرير مراجع دوال صريحة للمؤقتات
setTimeout(() => {
  alert(message);
}, 1000);

// ✅ آمن: استخدام JSON.parse لتحليل البيانات بدلاً من eval
const config = JSON.parse(rawConfigString);

// ✅ آمن: جدول توجيه العمليات الحسابية بدلاً من بناء دوال ديناميكية
const mathOperations = {
  '+': (a, b) => a + b,
  '-': (a, b) => a - b,
  '*': (a, b) => a * b,
  '/': (a, b) => (b === 0 ? 0 : a / b)
};

function safeCompute(operator, a, b) {
  const handler = mathOperations[operator];
  if (!handler) throw new Error('عملية حسابية غير مدعومة');
  return handler(a, b);
}
```

---

## 🛡️ 3. حماية مصبات شجرة DOM وواجهة Trusted Types API

تعد ثغرة **DOM XSS** الأكثر انتشاراً في تطبيقات الواجهة الأمامية. وتحدث عندما تصل بيانات غير موثوقة (مثل `location.hash` أو مدخلات البحث) مباشرة إلى أحد مصبات التنفيذ في المتصفح (مثل `element.innerHTML`).

### مقارنة أمان خصائص شجرة DOM:
| المصب / الخاصية | مستوى الأمان | آلية العمل |
| :--- | :--- | :--- |
| **`element.textContent`** | **آمن تماماً (Safe)** | يعامل كافة وسوم HTML كنصوص مجردة دون تشغيل أي وسم. |
| **`element.setAttribute('src', url)`** | **شديد الخطورة** | قد ينفذ كوداً إذا بدأ الرابط بـ `javascript:`. |
| **`element.innerHTML`** | **شديد الخطورة** | يحلل النص إلى عناصر DOM وينفذ الوسوم والأحداث المضمنة فوراً. |

### تفعيل واستخدام معيار Trusted Types:
يمنع معيار Trusted Types مصبات المتصفح الخطيرة من قبول نصوص خام عادية، ويجبرها على قبول كائنات معتمدة فقط من نوع `TrustedHTML` تم إنتاجها عبر سياسة تعقيم موثوقة:

```javascript
// 1. فحص توفر الميزة وإنشاء سياسة التعقيم الرسمية:
let sanitizePolicy = {
  createHTML: (str) => str // بديل للمتصفحات القديمة
};

if (window.trustedTypes && window.trustedTypes.createPolicy) {
  sanitizePolicy = window.trustedTypes.createPolicy('dom-sanitizer', {
    createHTML: (untrustedInput) => {
      // تعقيم النص غير الموثوق باستخدام مكتبة DOMPurify
      return DOMPurify.sanitize(untrustedInput);
    }
  });
}

// 2. إسناد البيانات بأمان للمصب:
const userBio = '<p>مرحباً بك <img src="x" onerror="alert(1)"></p>';
const cleanHtml = sanitizePolicy.createHTML(userBio);

// يقبل المتصفح هذا الإسناد لأن cleanHtml هو كائن TrustedHTML معتمد:
document.getElementById('profile-bio').innerHTML = cleanHtml;
```

---

## 🔒 4. التحصين الدفاعي ضد تلوث النماذج الأولية (Prototype Pollution)

تحدث ثغرة تلوث النماذج الأولية عندما تسمح دوال دمج الكائنات التكرارية (Deep Merge) أو تعيين المسارات للبيانات الخبيثة بتعديل وتلويث الكائن الأصلي `Object.prototype`.

### استراتيجيات التحصين:

#### أ. استخدام كائنات بلا نموذج أولي للقواميس وجداول البحث:
```javascript
// الكائن العادي يرث خصائص Object.prototype تلقائياً:
const normalDict = {};
console.log('toString' in normalDict); // true

// الكائن الخالي من الـ Prototype ليس لديه أي سلسلة وراثة على الإطلاق:
const safeLookup = Object.create(null);
console.log(safeLookup.__proto__); // undefined
console.log('toString' in safeLookup); // false! محصن تماماً من التلوث!
```

#### ب. دالة دمج كائنات تكرارية آمنة (Safe Deep Merge):
```javascript
function safeDeepMerge(target, source) {
  for (const key of Object.keys(source)) {
    // 🛡️ حظر المفاتيح الخطيرة التي تسبب التلوث:
    if (key === '__proto__' || key === 'constructor' || key === 'prototype') {
      continue; // تخطي وتجاهل مفاتيح التلوث فوراً!
    }

    if (
      source[key] &&
      typeof source[key] === 'object' &&
      !Array.isArray(source[key])
    ) {
      if (!target[key]) target[key] = {};
      safeDeepMerge(target[key], source[key]);
    } else {
      target[key] = source[key];
    }
  }
  return target;
}
```

#### ج. تجميد النموذج الأولي العام (Freezing the Prototype):
```javascript
// في الأنظمة الحساسة وفائقة الأمان، يتم تجميد الكائن الأولي عند بدء التطبيق:
Object.freeze(Object.prototype);
```

---

## 📬 5. تأمين التراسل بين النوافذ (`postMessage`)

تتيح واجهة `window.postMessage` التخاطب بين النوافذ والإطارات عبر نطاقات مختلفة. يؤدي الإهمال في فحص هوية المرسل إلى فتح ثغرات خطيرة تسمح بسرقة الجلسات.

### قاعدتا الأمان لـ PostMessage:
1. **لا ترسل بيانات حساسة باستخدام النجمة `*` كنطاق مستهدف أبداً:**
   ```javascript
   // ❌ كارثة أمنية: أي موقع يضع موقعك في iframe يمكنه سرقة هذا الرمز
   popup.postMessage({ token: secretToken }, '*');

   // ✅ آمن: تحديد النطاق الدقيق المسموح له باستلام البيانات
   popup.postMessage({ token: secretToken }, 'https://auth.company.com');
   ```

2. **تحقق دائماً وبصرامة من `event.origin` عند استقبال الرسائل:**
   ```javascript
   // في كود الصفحة المستقبلة:
   window.addEventListener('message', (event) => {
     // 🛡️ الفحص الصارم لنطاق المرسل:
     const TRUSTED_ORIGIN = 'https://auth.company.com';
     if (event.origin !== TRUSTED_ORIGIN) {
       console.warn(`تم حظر رسالة غير موثوقة من النطاق: ${event.origin}`);
       return;
     }

     // فحص بنية البيانات وصحتها قبل معالجتها:
     const { type, payload } = event.data || {};
     if (type === 'AUTH_COMPLETE' && typeof payload?.token === 'string') {
       handleAuthSuccess(payload.token);
     }
   });
   ```

---

## 🎲 6. التشفير والعشوائية: واجهة Web Crypto API

إياك واستخدام `Math.random()` لتوليد رموز الأمان أو معرفات الجلسات أو رموز منع التزوير (CSRF Tokens)، لأن `Math.random()` دالة عشوائية زائفة (PRNG) يمكن للمهاجم توقع نتائجها.

### توليد قيم عشوائية مشفرة وغير قابلة للتنبؤ:
```javascript
// توليد معرف UUID مشفر وآمن قياسياً:
const secureId = crypto.randomUUID();
console.log('معرف آمن مشفر:', secureId);

// توليد بايتات مشفرة عشوائية لرموز المصادقة:
function generateSecureToken(length = 32) {
  const buffer = new Uint8Array(length);
  crypto.getRandomValues(buffer); // أرقام عشوائية مشفرة من نواة النظام
  return Array.from(buffer, (byte) => byte.toString(16).padStart(2, '0')).join('');
}

console.log('رمز أمان مشفر:', generateSecureToken(16));
```

---

## 🧼 7. كود نظيف: قائمة التحقق الأمني (Security Checklist)

```javascript
// ❌ سيء: إعادة توجيه المستخدم مباشرة لرابط يحدده هو في الـ URL (Open Redirect / XSS)
const returnUrl = new URLSearchParams(window.location.search).get('next');
window.location.href = returnUrl; // يمكن للمهاجم إدخال "javascript:stealData()"!

// ✅ جيد: التحقق الصارم من أن الرابط يتبع نفس نطاق الموقع الموثوق حصراً
function safeRedirect(targetUrl) {
  try {
    const parsed = new URL(targetUrl, window.location.origin);
    // التأكد من أن الرابط ينتمي لنفس الأصل:
    if (parsed.origin === window.location.origin) {
      window.location.href = parsed.href;
    } else {
      window.location.href = '/dashboard';
    }
  } catch {
    window.location.href = '/dashboard';
  }
}
```

---

## 🎓 8. تهانينا! لقد أتممت مسار جافاسكريبت الأساسي بالكامل

لقد أتممت بنجاح كافة مستويات **منهج جافاسكريبت الأساسي (Core JavaScript Curriculum)** بمستوياته الثلاثة:
- **المستوى الأول (Level 1 - Basic):** أسس اللغة، أنواع البيانات، المعاملات، والشروط الحسابية.
- **المستوى الثاني (Level 2 - Foundation):** معمارية المتصفح، التحكم في شجرة DOM، العمليات غير المتزامنة، وواجهات التخزين والـ Web APIs.
- **المستوى الثالث (Level 3 - Advanced):** ميكانيكية محرك V8 والذاكرة، حلقة الأحداث والتزامن، الفئات ونظام ESM، هندسة الاختبارات ومراجعة الكود، التجميع الحديث، والبرمجة المراعية للأمان.

أنت الآن مسلح بالمعرفة العميقة والانضباط المعماري الذي يؤهلك لبناء أضخم تطبيقات الويب الحديثة، قيادة مراجعات الكود البرمجي في الفرق التقنية، أو الانتقال بثقة تامة لمسارنا التخصصي المتعمق في **اختبار اختراق تطبيقات الويب وجافاسكريبت (JavaScript-Web-Pentesting)**!
