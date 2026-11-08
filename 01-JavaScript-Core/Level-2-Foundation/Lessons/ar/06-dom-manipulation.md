# 06 - شجرة عناصر الصفحة والتحكم بها (DOM Manipulation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/06-dom-manipulation.md) | [العربية 🇸🇦](./06-dom-manipulation.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

**نموذج كائن المستند (Document Object Model - DOM)** هو تمثيل هيكلي كائني لصفحة الـ HTML. يقوم المتصفح بتحويل نص الـ HTML الخام إلى شجرة من العقد (Nodes) والكائنات البرمجية التي تستطيع لغة جافا سكريبت البحث فيها، وتعديلها، وإضافة أو حذف عناصر منها ديناميكياً.

إتقان التعامل الاحترافي مع الـ DOM هو جوهر بناء واجهات المستخدم التفاعلية دون التسبب في بطء العرض أو الوقوع في ثغرات أمنية كارثية.

بنهاية هذا الدرس، ستتقن:
1. البنية الهرمية لـ **شجرة الـ DOM** (المستند، العناصر، وعقد النصوص).
2. استخراج العناصر بكفاءة وسرعة باستخدام `querySelector()` و `querySelectorAll()`.
3. الفروق الجوهرية والأمنية بين `textContent` و `innerText` و `innerHTML`.
4. التحكم في الفئات التنسيقية بـ `classList` والسمات المخصصة (`dataset`).
5. إنشاء وإدراج العناصر بأداء عالي باستخدام `DocumentFragment` لمنع بطء الرسم والتحديث (Reflows).
6. معايير الكود النظيف والأمان الدفاعي عند تعديل واجهة المستخدم.

---

## 🌲 2. البنية الهرمية لشجرة الـ DOM (The DOM Tree)

عند تحميل صفحة الـ HTML، يقوم محلل المتصفح بتحويل الوسوم إلى شجرة كائنات جافا سكريبت:

```
                      +-------------------+
                      |     Document      |
                      +---------+---------+
                                |
                      +---------v---------+
                      |  <html> (عنصر)    |
                      +---------+---------+
            +-------------------+-------------------+
            |                                       |
    +-------v-------+                       +-------v-------+
    | <head> (عنصر) |                       | <body> (عنصر) |
    +---------------+                       +-------+-------+
                                        +-----------+-----------+
                                        |                       |
                                +-------v-------+       +-------v-------+
                                |  <h1> (عنصر)  |       |  <p> (عنصر)   |
                                +-------+-------+       +---------------+
                                        |
                                +-------v-------+
                                | "نص العنوان"  | (عقدة نصية)
                                +---------------+
```

---

## 🔍 3. استهداف والبحث عن العناصر (Querying Elements)

يعتمد تطوير الويب الحديث كلياً على محددات الـ CSS (CSS Selectors) لاستخراج العناصر:

### الطرق القياسية الحديثة:
```javascript
// 1. تحديد أول عنصر يطابق محدد الـ CSS (أو null إذا لم يوجد):
const primaryButton = document.querySelector('.btn-primary');
const searchInput = document.querySelector('#header input[type="text"]');

// 2. تحديد جميع العناصر المطابقة (يُرجع قائمة NodeList ساكنة):
const menuItems = document.querySelectorAll('nav .nav-link');

// تدعم NodeList التكرار بـ forEach مباشرة:
menuItems.forEach((item, index) => {
  console.log(`العنصر رقم ${index}: ${item.textContent}`);
});

// تحويل NodeList إلى مصفوفة حقيقية في حال رغبت باستخدام map أو filter:
const itemsArray = Array.from(menuItems);
```

### القائمة الساكنة (Static NodeList) مقابل المجموعة الحية (Live HTMLCollection):
- ترجع `querySelectorAll()` لقطة ساكنة (Static Snapshot) لعناصر الصفحة في لحظة استدعائها.
- الطرق القديمة (`getElementsByClassName` و `getElementsByTagName`) تُرجع مجموعات حية تتغير تلقائياً في الذاكرة كلما تغير الـ DOM، مما يسبب أخطاء غير متوقعة أثناء حلقات التكرار!

---

## ✏️ 4. تعديل النصوص ومحتوى الـ HTML

```javascript
const banner = document.querySelector('#welcome-banner');

// 1. textContent (الخيار المفضل والآمن): يغير النص المجرد، سريع، ولا يحلل وسوم الـ HTML:
banner.textContent = 'مرحباً بك مجدداً!';

// 2. innerText: يراعي التنسيقات المرئية (يتجاهل العناصر المخفية بـ display:none)، ولكنه أبطأ:
console.log(banner.innerText);

// 3. innerHTML: يقوم بتفسير وتحليل وسوم الـ HTML وعرضها كعناصر مرئية:
banner.innerHTML = '<strong>عرض خاص:</strong> خصم 20% اليوم!';
```

> [!CAUTION]
> **قاعدة أمنية حاسمة:**  
> **لا تقم إطلاقاً** بإسناد مدخلات مستخدم غير موثوقة أو نصوص مأخوذة من الروابط مباشرة إلى `innerHTML`؛ لأن ذلك يسبب ثغرة حقن كود **(Cross-Site Scripting - XSS)** مباشرة! استخدم دائماً `textContent` لعرض النصوص الديناميكية بأمان.

---

## 🎨 5. إدارة الفئات والأنماط والسمات (Classes & Attributes)

### أ) إدارة فئات الـ CSS عبر `classList` (الأسلوب الموصى به)
تجنب تعديل نص `className` يدوياً، واستخدم دوال `classList`:
```javascript
const card = document.querySelector('.card');

card.classList.add('active', 'highlighted'); // إضافة فئة أو أكثر
card.classList.remove('dimmed');             // إزالة فئة
card.classList.toggle('collapsed');          // إضافتها إن لم تكن موجودة، وحذفها إن وُجدت
const isVisible = card.classList.contains('active'); // إرجاع true أو false
card.classList.replace('theme-light', 'theme-dark');  // استبدال فئة بأخرى
```

### ب) التنسيقات المباشرة (Inline Styles)
```javascript
// التعديل المباشر (استخدم camelCase لخصائص CSS):
card.style.backgroundColor = '#1e293b';
card.style.display = 'flex';

// قراءة التنسيقات المحسوبة المطبقة من ملفات CSS الخارجية:
const computed = window.getComputedStyle(card);
console.log(computed.marginTop); // مثلاً: '16px'
```

### ج) السمات والبيانات المخصصة (`dataset`)
```javascript
const userAvatar = document.querySelector('#avatar');

// السمات القياسية:
userAvatar.setAttribute('alt', 'الصورة الشخصية للمستخدم');
console.log(userAvatar.getAttribute('src'));
userAvatar.removeAttribute('aria-hidden');

// السمات المخصصة (HTML: <div id="item" data-product-id="44" data-in-stock="true">):
const item = document.querySelector('#item');
console.log(item.dataset.productId); // "44"
item.dataset.inStock = 'false';       // تحديث قيمة السمة في الـ HTML
```

---

## 🏗️ 6. إنشاء وإدراج وحذف العناصر (Create, Insert & Remove)

```javascript
// 1. إنشاء عنصر DOM جديد في الذاكرة:
const newBadge = document.createElement('span');
newBadge.className = 'badge badge-success';
newBadge.textContent = 'جديد!';

// 2. إدراجه في الصفحة:
const cardHeader = document.querySelector('.card-header');
cardHeader.appendChild(newBadge); // إدراجه كآخر عنصر ابن
cardHeader.prepend(newBadge);     // إدراجه كأول عنصر ابن

// 3. طرق الإدراج الحديثة:
// targetElement.before(newBadge); // إدراجه قبله مباشرة
// targetElement.after(newBadge);  // إدراجه بعده مباشرة

// 4. حذف العناصر:
newBadge.remove(); // يحذف العنصر نفسه من شجرة الـ DOM بسهولة
```

### الإدراج المجمع عالي الأداء: `DocumentFragment`
إدراج 100 عنصر واحداً تلو الآخر في الـ DOM يجبر المتصفح على إعادة حساب موضع العناصر وتحديث الرسم 100 مرة (Reflows & Repaints). استخدام **`DocumentFragment`** يجمع العناصر في الذاكرة ويدرجها في عملية واحدة خاطفة:

```javascript
const listContainer = document.querySelector('#notifications-list');
const fragment = document.createDocumentFragment();

const notifications = ['تم إرسال البريد', 'اكتملت النسخة الاحتياطية', 'تم دفع الفاتورة'];

notifications.forEach((text) => {
  const li = document.createElement('li');
  li.textContent = text;
  fragment.appendChild(li); // يجمع في الذاكرة فقط دون إجهاد المتصفح
});

// إدراج واحد في شجرة الـ DOM لجميع العناصر دفعة واحدة:
listContainer.appendChild(fragment);
```

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: دمج نصوص الفئات يدوياً (عرضة للأخطاء والمسافات الزائدة)
element.className += ' active highlighted';

// ✅ كود ممتاز: استخدام واجهة classList المخصصة
element.classList.add('active', 'highlighted');

// ❌ كود سيئ: إضافة عناصر داخل حلقة تكرار مباشرة إلى الصفحة (يسبب بطء العرض Layout Thrashing)
data.forEach(item => {
  const div = document.createElement('div');
  div.textContent = item.name;
  container.appendChild(div); // يعيد رسم الصفحة في كل دورة!
});

// ✅ كود ممتاز: استخدام DocumentFragment وتحديث الصفحة دفعة واحدة
const fragment = document.createDocumentFragment();
data.forEach(item => {
  const div = document.createElement('div');
  div.textContent = item.name;
  fragment.appendChild(div);
});
container.appendChild(fragment); // عملية رسم واحدة فائقة السرعة
```

---

## 📝 8. الخلاصة والدرس التالي
- استخدم **`querySelector`** و **`querySelectorAll`** لتحديد العناصر بمحددات الـ CSS النظيفة.
- اعتمد دائماً على **`textContent`** لعرض النصوص غير الموثوقة لتجنب ثغرات الـ XSS.
- تحكم في التنسيقات عبر **`classList`** (`add`, `remove`, `toggle`).
- وظف **`DocumentFragment`** عند إنشاء وتوليد عناصر مجمعة لضمان أعلى أداء ممكن للواجهة.
- في الدرس التالي، سنتعلم كيف نجعل الصفحة تفاعلية مع المستخدم: **نظام الأحداث والتفويض والانتشار (Events System: Listeners, Bubbling & Delegation)**.
