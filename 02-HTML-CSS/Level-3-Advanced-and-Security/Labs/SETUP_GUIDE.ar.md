# مختبرات المستوى الثالث الأمنية: دليل التشغيل والتحميل المنفصل

> 🌐 **Language:** [English (SETUP_GUIDE.md)](./SETUP_GUIDE.md) | **العربية 🇸🇦**

---

## 🚀 1. التشغيل المحلي الفوري (Zero-Setup)

صُممت كافة مختبرات المستوى الثالث لتعمل **محلياً 100%** في متصفح الويب الخاص بك دون الحاجة لأي حزم npm، أو حاويات Docker، أو أي اتصال سحابي خارجي.

### الخيار أ: الفتح المباشر في المتصفح
يمكنك فتح أي مختبر بمجرد النقر المزدوج على ملف `index.html`:
- **في ويندوز:** انقر مرتين أو نفذ في PowerShell:
  ```powershell
  Start-Process chrome.exe "E:\Programming Languages\02-HTML-CSS\Level-3-Advanced-and-Security\Labs\Lab-1-Guided-Walkthrough\index.html"
  ```
- **في لينكس / ماك:**
  ```bash
  xdg-open index.html
  ```

### الخيار ب: تشغيل سيرفر محلي خفيف عبر بايثون (موصى به)
يمنع تشغيل سيرفر محلي قيود بروتوكول `file://` الصارمة في بعض المتصفحات:
- **باستخدام بايثون (ويندوز ولينكس):**
  ```bash
  # داخل مجلد المختبر
  python -m http.server 8080
  ```
  ثم افتح الرابط: `http://localhost:8080`
- **عبر إضافة Live Server في VS Code:**
  انقر بالزر الأيمن على `index.html` واختر **"Open with Live Server"**.

---

## 📥 2. كيفية تحميل مجلد المختبرات منفرداً من GitHub دون سحب كامل المستودع

إذا رغبت في تنزيل مجلد اللابات هذا فقط دون تحميل المشروع كاملاً، اتبع أياً من الطرق التالية:

### الطريقة 1: التنزيل المباشر عبر الويب (الأسرع والأسهل)
1. ادخل إلى موقع **[download-directory.github.io](https://download-directory.github.io)**
2. الصق رابط مجلد اللابات على GitHub:
   ```
   https://github.com/0xHaxmed/Programming-Languages/tree/main/02-HTML-CSS/Level-3-Advanced-and-Security/Labs
   ```
3. اضغط **Enter** وسيقوم الموقع بتجهيز وتنزيل ملف مضغوط `.zip` يحوي المجلد فقط!

### الطريقة 2: عبر خاصية Sparse Checkout في Git
```bash
# إنشاء مجلد جديد
git init html-css-labs && cd html-css-labs

# إضافة المستودع
git remote add -f origin https://github.com/0xHaxmed/Programming-Languages.git

# تفعيل السحب الجزئي
git config core.sparseCheckout true

# تحديد مجلد مختبرات المستوى الثالث فقط
echo "02-HTML-CSS/Level-3-Advanced-and-Security/Labs/*" >> .git/info/sparse-checkout

# سحب المجلد المحدد فقط
git pull origin main
```

### الطريقة 3: عبر أداة degit في Node.js
```bash
npx degit 0xHaxmed/Programming-Languages/02-HTML-CSS/Level-3-Advanced-and-Security/Labs my-security-labs
```

---

## 🧪 المختبرات المتاحة في هذا المستوى:
1. 🧪 **[المختبر 1: مختبر توجيهي مشروح ومحلول — سرقة الرموز عبر العلامات العالقة](./Lab-1-Guided-Walkthrough/README.ar.md)**
2. 🎯 **[المختبر 2: تحدي تدريبي غير محلول — تسريب البيانات عبر محددات CSS](./Lab-2-Challenge-Unsolved/README.ar.md)**
