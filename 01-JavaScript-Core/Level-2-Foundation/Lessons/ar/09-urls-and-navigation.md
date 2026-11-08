# 09 - عناوين URL واستخراج الاستعلامات والتنقل البرمجي (URLs & Navigation)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/09-urls-and-navigation.md) | [العربية 🇸🇦](./09-urls-and-navigation.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في تطبيقات الويب الحديثة، يمثل عنوان الرابط (URL) المرجع الأساسي لحالة التطبيق: كلمات البحث، الفلاتر النشطة، أرقام الصفحات (Pagination)، ووجهات التنقل يتم تخزينها مباشرة داخل شريط العنوان لتمكين المستخدم من حفظ الصفحة في المفضلة ومشاركتها.

تاريخياً، كان المطورون يتعاملون مع الروابط عبر تقطيع النصوص يدوياً والتعبيرات النمطية المعقدة والهشة. أما في جافا سكريبت الحديثة، تتوفر واجهات قياسية قوية ومدمجة: كائن **`URL`** وواجهة **`URLSearchParams`**.

بنهاية هذا الدرس، ستتقن:
1. تشريح الأجزاء المعمارية الدقيقة لعنوان الرابط (Protocol, Host, Path, Search, Hash).
2. قراءة وتعديل وتشفير معاملات الاستعلام باستخدام واجهة **`URLSearchParams`**.
3. حل وتركيب الروابط النسبية بأمان باستخدام المنشئ **`new URL()`**.
4. تحديث شريط عنوان المتصفح برمجياً دون إعادة تحميل الصفحة عبر **`history.pushState()`** و **`popstate`**.
5. الأمان البرمجي للروابط: فحص روابط إعادة التوجيه ومنع حقن بروتوكول `javascript:`.

---

## 🧭 2. التشريح المعماري لعنوان الرابط (Anatomy of a URL)

```
  https://example.com:8080/catalog/shoes?brand=nike&sort=asc#reviews
  \___/   \_________/ \__/ \___________/ \_________________/ \______/
 البروتوكول   المضيف   المنفذ     المسار          الاستعلام        المرساة
 \_______________________/
       الأصل (Origin)
```

### استخدام كائن `URL` القياسي:
```javascript
const url = new URL('https://example.com:8080/catalog/shoes?brand=nike&sort=asc#reviews');

console.log(url.origin);   // 'https://example.com:8080'
console.log(url.hostname); // 'example.com'
console.log(url.pathname); // '/catalog/shoes'
console.log(url.search);   // '?brand=nike&sort=asc'
console.log(url.hash);     // '#reviews'

// تعديل أي جزء يحدث الرابط بالكامل تلقائياً وبأمان:
url.pathname = '/catalog/jackets';
url.port = '9000';
console.log(url.href); // 'https://example.com:9000/catalog/jackets?brand=nike&sort=asc#reviews'
```

### حل الروابط النسبية (Relative URL Resolution):
يقبل المنشئ `URL` رابطاً أساسياً (Base URL) كمعامل ثانٍ لتركيب المسارات بسهولة:
```javascript
const base = 'https://api.mycompany.com/v1/';
const endpoint = new URL('users/profile', base);
console.log(endpoint.href); // 'https://api.mycompany.com/v1/users/profile'
```

---

## 🔍 3. إدارة معاملات الاستعلام عبر `URLSearchParams`

تغنيك واجهة `URLSearchParams` عن تقطيع النصوص يدوياً بـ `split()`، وتتولى فك التشفير والتشفير الآمن تلقائياً:

```javascript
// التهيئة بنص الاستعلام الحالي من عنوان الصفحة:
const searchParams = new URLSearchParams(window.location.search);

// 1. قراءة المعاملات:
const page = searchParams.get('page'); // يرجع القيمة كنص، أو null إذا لم يكن موجوداً
const hasFilter = searchParams.has('category'); // يرجع true أو false

// 2. قراءة قيم متعددة لنفس المفتاح (?tag=js&tag=react):
const tags = searchParams.getAll('tag'); // ['js', 'react']

// 3. التعيين والإضافة:
searchParams.set('page', '2'); // يضبط page كـ 2 (يستبدل القديم)
searchParams.append('tag', 'typescript'); // يضيف قيمة إضافية لنفس المفتاح

// 4. الحذف:
searchParams.delete('outdatedParam');

// 5. التحويل مجدداً لنص رابط نظيف ومشفر:
console.log(searchParams.toString());
// الناتج: "brand=nike&page=2&tag=js&tag=react&tag=typescript"
```

> [!TIP]
> **التشفير التلقائي (Percent-Encoding):**  
> عند استدعاء `searchParams.set('query', 'أحذية رياضية')`، تقوم الواجهة تلقائياً بتحويل المسافات والحروف الخاصة إلى صيغة آمنة لروابط الويب، مانعة حدوث تلف في الروابط.

---

## 🚀 4. التنقل في جانب العميل: `pushState` مقابل `replaceState`

في تطبيقات الصفحة الواحدة (SPAs)، نحدث الرابط وسجل التصفح برمجياً دون إرسال طلب جديد للخادم:

```javascript
// 1. pushState(): يغير الرابط ويضيف سجلاً جديداً في تاريخ المتصفح (يمكن الرجوع له بزر Back):
function filterProducts(category) {
  const currentUrl = new URL(window.location);
  currentUrl.searchParams.set('category', category);

  // history.pushState(stateObject, title, newUrl)
  history.pushState({ category }, '', currentUrl);
  
  // تحديث محتوى الصفحة:
  updateCatalogView(category);
}

// 2. replaceState(): يغير الرابط ولكنه يستبدل السجل الحالي (لا يمكن للمستخدم الرجوع له):
function updatePagination(pageNum) {
  const currentUrl = new URL(window.location);
  currentUrl.searchParams.set('page', pageNum);

  history.replaceState({ page: pageNum }, '', currentUrl);
}

// 3. الاستماع لزر الرجوع والتقدم في المتصفح عبر حدث popstate:
window.addEventListener('popstate', (event) => {
  console.log('تنقل المستخدم في السجل! الحالة المحفوظة:', event.state);
  // استعادة حالة العرض من الرابط الجديد:
  const params = new URLSearchParams(window.location.search);
  updateCatalogView(params.get('category'));
});
```

---

## 🛡️ 5. أمان الروابط: منع إعادة التوجيه المفتوح وحقن السكربتات

عندما يقرأ التطبيق رابط الوجهة التالية من معامل استعلام (مثل `?returnUrl=/dashboard`):

### الثغرة الخطرة:
```javascript
// ❌ كود خطر: إسناد مباشر لمعامل من الرابط إلى location.href دون فحص
const nextUrl = new URLSearchParams(window.location.search).get('returnUrl');
// إذا مرر المهاجم ?returnUrl=javascript:alert(document.origin)
// أو ?returnUrl=https://attacker-phishing.com
location.href = nextUrl; // يؤدي لتنفيذ سكربت خبيث أو إعادة توجيه لموقع احتيالي!
```

### الحل الدفاعي المحكم:
```javascript
// ✅ كود آمن: التأكد الصارم من أن الرابط يمثل مساراً داخلياً نسبياً فقط
function getSafeReturnUrl() {
  const target = new URLSearchParams(window.location.search).get('returnUrl');
  if (!target) return '/dashboard';

  // التأكد من أنه يبدأ بشرطة مائلة واحدة وليس شرطتين (// يعاملها المتصفح كرابط خارجي):
  if (target.startsWith('/') && !target.startsWith('//') && !target.startsWith('/\\')) {
    return target; // مسار داخلي سليم
  }

  return '/dashboard'; // مسار افتراضي آمن
}

location.href = getSafeReturnUrl();
```

---

## 🧼 6. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: تقطيع نص الرابط يدوياً بأسلوب هش وعرضة للأخطاء
const query = window.location.search;
const brand = query.split('brand=')[1]?.split('&')[0];

// ✅ كود ممتاز: استخدام واجهة URLSearchParams القياسية المدمجة
const params = new URLSearchParams(window.location.search);
const brand = params.get('brand');

// ❌ كود سيئ: دمج نصوص الروابط يدوياً بشرطات مائلة (ينتج عنه روابط تالفة ومكررة)
const apiUrl = 'https://api.com/search?q=' + searchTerm + '&page=' + page;

// ✅ كود ممتاز: استخدام كائن URL مع searchParams لضمان التشفير الصحيح
const url = new URL('https://api.com/search');
url.searchParams.set('q', searchTerm);
url.searchParams.set('page', page);
const finalUrl = url.href; // رابط مشفر ونظيف تماماً
```

---

## 📝 7. الخلاصة والدرس التالي
- استخدم كائن **`URL`** لفحص وتعديل أجزاء الروابط النسبية والمطلقة بأمان.
- اعتمد على واجهة **`URLSearchParams`** لإدارة معاملات الاستعلام والترميز التلقائي للرموز الخاصة.
- استخدم **`history.pushState()`** لتحديث شريط العنوان في تطبيقات الـ SPA دون إعادة تحميل الصفحة.
- افحص دائماً روابط إعادة التوجيه لمنع ثغرات **Open Redirect** وحقن السكربتات عبر `javascript:`.
- في الدرس التالي، سنشرح آلية تسلسل وتبادل البيانات: **كائن JSON والتحويل المزدوج عبر `JSON.stringify` و `JSON.parse`**.
