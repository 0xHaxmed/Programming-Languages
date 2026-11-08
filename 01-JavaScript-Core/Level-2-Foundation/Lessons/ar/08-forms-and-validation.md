# 08 - النماذج وتتبع المدخلات والتحقق من صحة البيانات (Forms & Validation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/08-forms-and-validation.md) | [العربية 🇸🇦](./08-forms-and-validation.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تعد نماذج الـ HTML وسيلة التفاعل الأساسية التي يدخل المستخدم من خلالها بياناته إلى تطبيقات الويب. تاريخياً، كان إرسال النموذج يؤدي إلى إعادة تحميل الصفحة بالكامل عبر طلب HTTP POST تقليدي. أما في تطبيقات الويب الحديثة (SPAs)، تتولى جافا سكريبت اعتراض عملية الإرسال، والتحقق من صحة البيانات، وإرسالها لاتزامناً عبر واجهات الـ JSON API دون مغادرة الصفحة.

بنهاية هذا الدرس، ستتقن:
1. اعتراض إرسال النماذج باحترافية عبر حدث `submit` والدالة `e.preventDefault()`.
2. استخراج بيانات النماذج برمجياً باستخدام واجهة **`FormData`** الحديثة.
3. التمييز الدقيق بين أحداث الإدخال: `input`، `change`، `focus`، و `blur`.
4. الاستفادة من محرك المتصفح المدمج **(HTML5 Constraint Validation API)**.
5. فهم الحد الفاصل الحاسم: **التحقق في جانب العميل لتحسين تجربة المستخدم مقابل التحقق في جانب الخادم للأمان**.
6. كتابة كود نظيف وسهل الوصول (Accessible) للتحقق من صحة المدخلات.

---

## 🛑 2. اعتراض إرسال النماذج برمجياً

من الأخطاء الكلاسيكية الشائعة ربط مستمع الحدث بزر الإرسال (`button click`). الأسلوب الصحيح دائماً هو الاستماع لحدث **`submit` على عنصر النموذج `<form>` نفسه**:

```javascript
const registrationForm = document.querySelector('#register-form');

registrationForm.addEventListener('submit', (event) => {
  // 1. منع المتصفح من إعادة تحميل الصفحة تلقائياً:
  event.preventDefault();

  console.log('تم اعتراض إرسال النموذج ومعالجته عبر جافا سكريبت.');
});
```

> [!NOTE]
> الاستماع لحدث `submit` على النموذج يلتقط تلقائياً نقرات **زر الإرسال** وكذلك ضغط المستخدم على زر **Enter** داخل أي حقل إدخال.

---

## 📦 3. قراءة قيم الحقول: واجهة `FormData` مقابل الاستخراج اليدوي

### الطريقة الحديثة: واجهة `FormData`
بدلاً من كتابة كود منفصل للبحث عن كل حقل `input` بمعرفه الـ ID، تجمع واجهة `FormData` كافة الحقول التي تمتلك السمة `name` في خطوة واحدة:

```html
<form id="user-form">
  <input type="text" name="username" value="alex99">
  <input type="email" name="email" value="alex@example.com">
  <input type="checkbox" name="subscribe" checked>
</form>
```

```javascript
const form = document.querySelector('#user-form');
const formData = new FormData(form);

// 1. استخراج قيمة حقل فردي عبر السمة name:
const username = formData.get('username'); // 'alex99'
const isSubscribed = formData.get('subscribe') === 'on'; // true

// 2. تحويل النموذج بالكامل إلى كائن جافا سكريبت جاهز للإرسال لـ API:
const payload = Object.fromEntries(formData.entries());
console.log(payload);
// { username: 'alex99', email: 'alex@example.com', subscribe: 'on' }
```

---

## ⚡ 4. أحداث تتبع المدخلات: `input` مقابل `change` مقابل `blur`

اختيار الحدث المناسب هو ما يحدد هل تبدو الواجهة سلسة ومريحة للمستخدم أم مزعجة:

```javascript
const emailInput = document.querySelector('#email-field');

// 1. حدث 'input': ينطلق فوراً وبشكل متزامن مع كل ضغطة زر أو حذف حرف:
emailInput.addEventListener('input', (e) => {
  // مثالي لعداد الحروف المتبقية أو قياس قوة كلمة المرور لحظياً:
  console.log('القيمة الحالية:', e.target.value);
});

// 2. حدث 'change': ينطلق فقط بعد اعتماد القيمة (عند الضغط على Enter أو الخروج من الحقل):
emailInput.addEventListener('change', (e) => {
  console.log('تم اعتماد القيمة:', e.target.value);
});

// 3. حدث 'blur': ينطلق في اللحظة التي يفقد فيها الحقل التركيز (يخرج المؤشر منه):
emailInput.addEventListener('blur', (e) => {
  // المعيار العالمي: فحص صحة الحقل عند الخروج منه حتى لا تقاطع المستخدم أثناء الكتابة!
  validateEmailField(e.target);
});
```

---

## 🛡️ 5. واجهة التحقق المدمجة في المتصفح (Constraint Validation API)

تحتوي المتصفحات الحديثة على محرك فحص مدمج يعتمد على سمات الـ HTML القياسية:
- `required` (حقل إجباري)
- `minlength="4"` / `maxlength="20"` (الحد الأدنى والأقصى للطول)
- `type="email"` / `type="url"` (مطابقة الصيغة)
- `pattern="^[A-Za-z0-9_]+$"` (مطابقة تعبير نمطي Regex)

يمكنك التفاعل برمجياً مع هذا المحرك في جافا سكريبت:

```javascript
const input = document.querySelector('#username-input');

// 1. checkValidity(): ترجع true إذا كان الحقل صالحاً، و false إذا كان مخالفاً:
if (!input.checkValidity()) {
  console.warn('الحقل غير صالح!');
  
  // 2. كائن validity: معرفة السبب الدقيق لعدم صلاحية الحقل:
  if (input.validity.valueMissing) {
    console.log('خطأ: هذا الحقل إجباري ومطلوب.');
  } else if (input.validity.tooShort) {
    console.log('خطأ: اسم المستخدم قصير جداً.');
  } else if (input.validity.patternMismatch) {
    console.log('خطأ: يُسمح بالحروف الإنجليزية والأرقام فقط.');
  }
}

// 3. تعيين رسالة خطأ مخصصة:
input.setCustomValidity('اسم المستخدم هذا مستخدم بالفعل، يرجى اختيار اسم آخر.');
```

---

## 🚨 6. القاعدة الذهبية لأمان النماذج والمدخلات

> [!CAUTION]
> **التحقق في جانب العميل (Client-Side) مخصص لتجربة المستخدم (UX) فقط!**  
> يمكن لأي شخص تجاوز التحقق في المتصفح بسهولة بالغة:
> 1. فتح الـ DevTools وحذف السمة `required` أو `pattern` من كود الـ HTML.
> 2. تعطيل جافا سكريبت بالكامل في المتصفح.
> 3. إرسال طلب HTTP POST مباشر عبر أدوات مثل `curl` أو Postman دون فتح المتصفح أصلاً.
> 
> **لا تعتمد أبداً على التحقق في الواجهة لأغراض الحماية! يجب على خادم الويب (Backend) التحقق من صحة وتطهير وتفويض كل حقل يتم استلامه بشكل مستقل وحتمي.**

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: استخراج كل حقل يدوياً في النماذج الكبيرة
const name = document.querySelector('#name').value;
const email = document.querySelector('#email').value;
const role = document.querySelector('#role').value;
const city = document.querySelector('#city').value;

// ✅ كود ممتاز: استخراج مجمع ونظيف باستخدام FormData
const formData = new FormData(event.target);
const userProfile = Object.fromEntries(formData.entries());

// ❌ كود سيئ: إظهار رسائل الخطأ الحمراء مع كل حرف يكتبه المستخدم (تجربة مستخدم سيئة ومربكة)
input.addEventListener('input', () => {
  if (!isValid(input.value)) showError('قيمة غير صالحة!');
});

// ✅ كود ممتاز: التحقق عند فقدان التركيز (blur) ومسح رسالة الخطأ فور بدء التعديل
input.addEventListener('blur', () => {
  if (!isValid(input.value)) showError('يرجى إدخال بريد إلكتروني صالح.');
});
input.addEventListener('input', () => {
  clearError(); // مسح رسالة الخطأ بمجرد أن يبدأ المستخدم في تصحيح المدخل
});
```

---

## 📝 8. الخلاصة والدرس التالي
- استمع دائماً لحدث **`submit` على وسم `<form>`** وليس النقر على الزر.
- استخدم **`event.preventDefault()`** لمنع إعادة تحميل الصفحة في الـ SPAs.
- اعتمد على واجهة **`FormData`** لتحويل النماذج إلى كائنات JSON بسهولة.
- افحص المدخلات عند حدث **`blur`** لتحقيق أفضل تجربة استخدام.
- تذكر دائماً: **التحقق في المتصفح لتسهيل الاستخدام؛ والتحقق في الخادم لفرض الأمان.**
- في الدرس التالي، سنتعلم كيفية قراءة وتعديل معاملات الروابط: **عناوين URL وتحليل الاستعلامات عبر `URLSearchParams`**.
