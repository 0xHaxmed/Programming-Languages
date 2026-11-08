# 07 - نظام الأحداث والتفويض والانتشار (The Events System & Delegation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/07-events-system.md) | [العربية 🇸🇦](./07-events-system.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تطبيقات الويب بطبيعتها قائمة على **الأحداث (Event-Driven)**. الحدث هو إشارة يطلقها المتصفح تفيد بوقوع إجراء معين، مثل قيام المستخدم بالنقر على زر، أو الكتابة في حقل إدخال، أو تحريك الفأرة، أو إرسال نموذج بيانات.

لبناء واجهات مستخدم سريعة وعالية الأداء وتوفر استهلاك الذاكرة، يجب عليك فهم مسار انتقال الأحداث عبر شجرة الـ DOM وإتقان نمط **تفويض الأحداث (Event Delegation Pattern)** القياسي.

بنهاية هذا الدرس، ستتقن:
1. ربط وإلغاء مستمعي الأحداث برمجياً عبر `addEventListener()` و `removeEventListener()`.
2. تشريح كائن الحدث `Event` والتمييز الحاسم بين `e.target` و `e.currentTarget`.
3. منع السلوك الافتراضي للمتصفح باستخدام `e.preventDefault()`.
4. المراحل الثلاث لدورة حياة الحدث: **الالتقاط (Capturing)**، **الهدف (Target)**، و **الصعود (Bubbling)**.
5. إيقاف انتشار الحدث عبر الشجرة باستخدام `e.stopPropagation()`.
6. تطبيق نمط **تفويض الأحداث (Event Delegation)** عالي الأداء باستخدام `element.closest()`.

---

## 👂 2. ربط وإلغاء مستمعي الأحداث برمجياً

تجنب كتابة الأحداث داخل وسوم الـ HTML مباشرة (مثل `onclick="..."`). اربط الأحداث دائماً داخل كود جافا سكريبت:

```javascript
const saveButton = document.querySelector('#btn-save');

// دالة المعالج المسماة:
function handleSaveClick(event) {
  console.log('تم النقر على زر الحفظ!');
}

// 1. ربط مستمع الحدث:
saveButton.addEventListener('click', handleSaveClick);

// 2. إلغاء ربط المستمع (يتطلب نفس المرجع الدقيق للدالة في الذاكرة!):
saveButton.removeEventListener('click', handleSaveClick);
```

> [!WARNING]
> إذا قمت بربط دالة مجهولة (`saveButton.addEventListener('click', () => {...})`)، فلن تتمكن من إزالتها لاحقاً أبداً؛ لأن مرجعها في الذاكرة فُقد!

### خيارات التكوين المتقدمة للمستمع:
تقبل دالة `addEventListener(type, handler, options)` معاملاً ثالثاً للتكوين:
```javascript
// أ) 'once: true' — يقوم المستمع بحذف نفسه تلقائياً بعد أول تفعيل:
saveButton.addEventListener('click', handleSaveClick, { once: true });

// ب) 'passive: true' — يخبر المتصفح أن الدالة لن تستدعي preventDefault() (يحسن سرعة التمرير واللمس على الهواتف):
window.addEventListener('scroll', onScrollHandler, { passive: true });
```

---

## 🎯 3. كائن الحدث: `e.target` مقابل `e.currentTarget`

عند وقوع الحدث، يمرر المتصفح تلقائياً كائناً يحمل تفاصيل الحدث (`Event Object`):

```javascript
const card = document.querySelector('.card');

card.addEventListener('click', (event) => {
  // e.target: العنصر الفيزيائي الدقيق الذي نقرت عليه الفأرة (مثلاً: أيقونة <i> داخل الزر)
  console.log('العنصر الذي نُقر عليه فعلياً:', event.target);

  // e.currentTarget: العنصر المربوط به مستمع الحدث نفسه (الحاوية .card)
  console.log('العنصر صاحب المستمع:', event.currentTarget);
});
```

### دوال الحدث الأساسية:
- **`e.preventDefault()`:** إلغاء السلوك التلقائي للمتصفح (مثل منع رابط `<a>` من الانتقال لصفحة جديدة، أو منع نموذج `<form>` من إعادة تحميل الصفحة).
- **`e.stopPropagation()`:** إيقاف انتقال الحدث للأعلى أو للأسفل عبر شجرة الـ DOM.

---

## 🌊 4. المراحل الثلاث لانتشار الحدث (Event Propagation)

عندما ينقر المستخدم على زر يقع عميقاً داخل شجرة الـ DOM، فإن الحدث لا ينطلق فقط عند ذلك الزر، بل يمر بدورة حياة ثلاثية كاملة:

```
                            [ Window / Document ]
                                      |   ^
            1. مرحلة الالتقاط         |   |    3. مرحلة الصعود
            (تهبط من الأعلى للأسفل)   |   |    (تصعد من الأسفل للأعلى)
                                      v   |
                                 [ <body> ]
                                      |   ^
                                      v   |
                                 [ <div> ]
                                      |   ^
                                      v   |
                              [ 2. مرحلة الهدف ]
                             (الزر الذي نُقر عليه)
```

1. **مرحلة الالتقاط (Capturing Phase):** يهبط الحدث من قمة النافذة `window` نزولاً عبر العناصر حتى يصل إلى العنصر الأب للزر.
2. **مرحلة الهدف (Target Phase):** يصل الحدث إلى العنصر الفعلي المستهدف بالنقر.
3. **مرحلة الصعود والانتشار (Bubbling Phase - الوضع الافتراضي):** يصعد الحدث مجدداً للأعلى عبر كل العناصر الآباء بالتتابع حتى يصل إلى `window`.

```javascript
// الوضع الافتراضي لمستمعي الأحداث هو الاستماع أثناء مرحلة الصعود (Bubbling).
// للاستماع أثناء مرحلة الهبوط (Capturing)، نمرر capture: true:
document.body.addEventListener('click', (e) => {
  console.log('التقط عنصر body الحدث أولاً أثناء هبوطه!');
}, { capture: true });
```

---

## 🚀 5. نمط تفويض الأحداث (Event Delegation Pattern)

### المشكلة:
تخيل قائمة متجر تحتوي على 500 منتج. ربط 500 مستمع أحداث منفصل يستهلك ذاكرة المتصفح ويبطئ الصفحة، كما أنه يفشل مع المنتجات الجديدة التي يضيفها المستخدم لاحقاً عبر الـ DOM:

```javascript
// ❌ كود سيئ: 500 مستمع منفصل (استنزاف للذاكرة ولا يعمل مع العناصر المضافة ديناميكياً)
document.querySelectorAll('.delete-btn').forEach(btn => {
  btn.addEventListener('click', handleDelete);
});
```

### الحل الاحترافي: تفويض الأحداث
ربط **مستمع حدث واحد فقط** بالعنصر الأب المشترك، واستغلال صعود الحدث (Bubbling) لالتقاط نقرات كافة الأبناء الحاليين والمستقبليين:

```javascript
// ✅ كود ممتاز: مستمع واحد على الحاوية الأب باستخدام event.target و closest()
const itemsList = document.querySelector('#product-list');

itemsList.addEventListener('click', (event) => {
  // فحص هل تمت النقرة على زر الحذف أو أي أيقونة بداخله:
  const deleteBtn = event.target.closest('.delete-btn');

  if (deleteBtn && itemsList.contains(deleteBtn)) {
    const productId = deleteBtn.dataset.id;
    console.log(`حذف المنتج رقم: ${productId}`);
    deleteBtn.closest('.product-item').remove();
  }
});
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: كتابة الأحداث داخل وسوم الـ HTML (يخالف مبدأ فصل الاهتمامات وقواعد الـ CSP)
// <button onclick="handleClick()">إرسال</button>

// ✅ كود ممتاز: الربط البرمجي المنظم عبر addEventListener
const submitBtn = document.querySelector('#submit-btn');
submitBtn.addEventListener('click', handleSubmit);

// ❌ كود سيئ: نسيان إزالة المستمعات عند إخلاق أو حذف المكونات البرمجية (Memory Leak)
class Modal {
  open() {
    window.addEventListener('resize', this.onResize); // يظل في الذاكرة للأبد!
  }
}

// ✅ كود ممتاز: تنظيف وإلغاء المستمعات عند انتهاء الحاجة إليها
class Modal {
  close() {
    window.removeEventListener('resize', this.onResize);
  }
}
```

---

## 📝 7. الخلاصة والدرس التالي
- تجنب كتابة `onclick` داخل الـ HTML؛ استخدم دائماً **`addEventListener()`**.
- تذكر أن **`e.target`** هو العنصر الذي نُقر عليه بالماوس، بينما **`e.currentTarget`** هو العنصر الحامل للمستمع.
- تنتشر الأحداث صعوداً (**Bubbling**) عبر العناصر الأب بالوضع الافتراضي.
- استخدم نمط **تفويض الأحداث (Event Delegation)** لإدارة القوائم والعناصر الديناميكية بأعلى كفاءة في الذاكرة.
- في الدرس التالي، سنتعلم كيفية التحكم في مدخلات المستخدمين: **النماذج والتحقق من صحة البيانات (Forms & Input Validation)**.
