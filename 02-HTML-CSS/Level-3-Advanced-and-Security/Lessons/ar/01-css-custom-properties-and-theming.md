# 01 - متغيرات CSS والسمات اللونية الديناميكية (CSS Custom Properties & Theming)

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/01-css-custom-properties-and-theming.md) | [العربية 🇸🇦](./01-css-custom-properties-and-theming.md)

---

## 📌 1. ما هي متغيرات CSS (Custom Properties)؟

على عكس متغيرات معالجات Sass أو Less التي تُترجم إلى قيم نصية ثابتة قبل التشغيل (Build-time)، تعيش **متغيرات CSS الأصلية (CSS Custom Properties)** ديناميكياً داخل شجرة الـ DOM أثناء وقت التشغيل الفعلي (Runtime). إنها تخضع لقواعد التعاقب (Cascade) والتوريث (Inheritance)، ويمكن قراءتها وتعديلها لحظياً عبر CSS أو جافا سكريبت.

---

## 🏗️ 2. تعريف واستخدام المتغيرات

تبدأ أسماء المتغيرات دائماً بشرطتين متتاليتين (`--`):

```css
/* 1. التعريف العام الشامل على جذر المستند :root */
:root {
    --brand-primary: hsl(217, 91%, 60%);
    --brand-dark: hsl(224, 76%, 48%);
    --surface-bg: #ffffff;
    --text-color: hsl(222, 47%, 11%);
    --radius-sm: 0.25rem;
    --radius-md: 0.5rem;
}

/* 2. استدعاء المتغير عبر دالة var() مع قيمة احتياطية اختيارية */
.button {
    background-color: var(--brand-primary);
    color: var(--surface-bg);
    border-radius: var(--radius-md, 4px); /* استخدام 4px كبديل إن لم يكن المتغير معرفاً */
}
```

---

## 🌳 3. التعاقب والتخصيص على مستوى المكونات (Component Scoping)

نظراً لأن المتغيرات تورث للأبناء، يمكن لأي عنصر فرعي إعادة تعريف قيمة المتغير دون الحاجة لتكرار بقية قواعد التنسيق:

```css
.card {
    --card-accent: #0284c7; /* لون التمييز الافتراضي للبطاقة */
    border-top: 4px solid var(--card-accent);
}

/* صنف التعديل يغير المتغير فقط! */
.card--critical {
    --card-accent: #ef4444; /* يتحول الإطار للأحمر فوراً دون إعادة كتابة التنسيقات! */
}
```

---

## 🌓 4. تبديل السمات الديناميكي (الوضع الفاتح والوضع الليلي)

باستخدام متغيرات CSS، يتم تبديل الوضع الليلي دون تعديل محددات المكونات إطلاقاً:

```css
/* لوحة ألوان الوضع النهاري */
:root {
    --bg-page: #f8fafc;
    --bg-card: #ffffff;
    --text-primary: #0f172a;
    --border-subtle: #e2e8f0;
}

/* لوحة ألوان الوضع الليلي */
[data-theme="dark"] {
    --bg-page: #0f172a;
    --bg-card: #1e293b;
    --text-primary: #f8fafc;
    --border-subtle: #334155;
}

/* المكونات تتكيف تلقائياً مع السمة النشطة! */
body {
    background-color: var(--bg-page);
    color: var(--text-primary);
}

.panel {
    background-color: var(--bg-card);
    border: 1px solid var(--border-subtle);
}
```

---

## 🎯 ملخص وتحدي الدرس

- متغيرات CSS ديناميكية وتورث وتعمل لحظياً في المتصفح.
- استخدم `:root` لحفظ رموز التصميم العامة (Design Tokens).
- فعّل الوضع الليلي بسلاسة عبر إعادة تعيين قيم المتغيرات تحت `[data-theme="dark"]`.

**تحدي الدرس:** صمم بطاقة تضم عنواناً ومحتوى وزراً. عرّف متغيرات للخلفية والنص ولون الزر، وأضف سمة جديدة باسم `[data-theme="cyberpunk"]` تحول الألوان إلى درجات فسفورية نيون عند تفعيلها.

---

**الدرس التالي:**  
➡️ [الدرس 02: معمارية BEM المنظمة وتجنب حروب الأولوية](./02-bem-architecture-and-maintainable-css.md) | 🏠 [فهرس المستوى الثالث](../../README.ar.md)
