# المستوى 3 — المعمارية المتقدمة، إمكانية الوصول، وأمان HTML/CSS

> 🌐 **Language:** [English (README.md)](./README.md) | **العربية 🇸🇦**

---

## 🎯 المهمة التعليمية

- **الهدف:** الارتقاء بمهاراتك في تطوير الواجهات الأمامية إلى مستوى مهندسي البرمجيات المتقدمين ومدققي الأمان السيبراني. احتراف بنية CSS القابلة للتوسع (BEM ومتغيرات CSS)، وتطبيق معايير إمكانية الوصول العالمية (WCAG و ARIA)، مع دراسة متعمقة وشاملة **لثغرات HTML و CSS الخالصة وطرق الحماية منها**: حقن العلامات العالقة (Dangling Markup Injection)، تسريب البيانات عبر CSS (CSS Data Exfiltration)، وعزل إطارات Iframe.
- **المتطلب السابق:** إتمام [المستوى 1: الهيكل الأساسي](../Level-1-Basic/README.ar.md) و [المستوى 2: التخطيطات الحديثة](../Level-2-Foundation/README.ar.md).

---

## 🧭 فهرس دروس المستوى الثالث

| # | الموضوع | الدليل بالإنجليزية | الدليل بالعربية | المفاهيم الجوهرية المغطاة |
| :-: | :--- | :--- | :--- | :--- |
| **01** | **متغيرات CSS والسمات** | [`01-css-variables.md`](./Lessons/en/01-css-custom-properties-and-theming.md) | [`01-css-variables.md`](./Lessons/ar/01-css-custom-properties-and-theming.md) | `:root`, `--var-name`, `var()`, القيم البديلة، تبديل الوضع الليلي ديناميكياً. |
| **02** | **معمارية BEM المنظمة** | [`02-bem-architecture.md`](./Lessons/en/02-bem-architecture-and-maintainable-css.md) | [`02-bem-architecture.md`](./Lessons/ar/02-bem-architecture-and-maintainable-css.md) | الكتلة (Block)، العنصر (Element)، والمعدل (Modifier)، إنهاء فوضى الأولوية. |
| **03** | **إمكانية الوصول (WCAG)** | [`03-wcag-and-aria.md`](./Lessons/en/03-web-accessibility-wcag-and-aria.md) | [`03-wcag-and-aria.md`](./Lessons/ar/03-web-accessibility-wcag-and-aria.md) | معايير WCAG 2.1 AA، خصائص `aria-label`, `aria-hidden`, والتنقل بلوحة المفاتيح. |
| **04** | **عزل نوافذ Iframe** | [`04-iframe-security.md`](./Lessons/en/04-iframe-sandboxing-and-clickjacking.md) | [`04-iframe-security.md`](./Lessons/ar/04-iframe-sandboxing-and-clickjacking.md) | `<iframe>`, رايات `sandbox`, هجمات خطف النقرات Clickjacking, وتوجيهات الحماية. |
| **05** | **حقن العلامات العالقة** | [`05-dangling-markup.md`](./Lessons/en/05-dangling-markup-injection.md) | [`05-dangling-markup.md`](./Lessons/ar/05-dangling-markup-injection.md) | سرقة رموز CSRF عبر وسوم غير مغلقة حتى في وجود حماية CSP الصارمة! |
| **06** | **تسريب البيانات عبر CSS** | [`06-css-exfiltration.md`](./Lessons/en/06-css-data-exfiltration.md) | [`06-css-exfiltration.md`](./Lessons/ar/06-css-data-exfiltration.md) | قراءة كلمات المرور والرموز حرفاً بحرف عبر محددات الخصائص ودون أي جافا سكريبت! |
| **07** | **تكامل الموارد وسياسة CSP** | [`07-sri-and-csp.md`](./Lessons/en/07-subresource-integrity-and-csp-foundations.md) | [`07-sri-and-csp.md`](./Lessons/ar/07-subresource-integrity-and-csp-foundations.md) | التحقق بالهاش `integrity`, الحماية من اختراق سيرفرات CDN، وسياسات أمان المحتوى. |
| **08** | **التحصين الدفاعي للواجهة** | [`08-defensive-hardening.md`](./Lessons/en/08-defensive-html-and-css-hardening.md) | [`08-defensive-hardening.md`](./Lessons/ar/08-defensive-html-and-css-hardening.md) | القائمة المرجعية الشاملة لتحصين أكواد HTML و CSS ضد التلاعب والتسريب. |

---

## 🔬 المختبرات الأمنية التطبيقية

اختبر وتحقق من ثغرات وميكانيكيات أمان HTML و CSS محلياً بصفر إعدادات:
- 👉 [دليل التشغيل وتحميل مجلد المختبر منفرداً](./Labs/SETUP_GUIDE.ar.md) | [English Setup Guide](./Labs/SETUP_GUIDE.md)
- 🧪 **[المختبر 1: مختبر توجيهي مشروح ومحلول — سرقة الرموز عبر العلامات العالقة](./Labs/Lab-1-Guided-Walkthrough/README.ar.md)** (تطبيق مصاب مشروح بالكامل مع كود الاستغلال وطريقة الترقيع الأمني)
- 🎯 **[المختبر 2: تحدي تدريبي غير محلول — تسريب البيانات عبر محددات CSS](./Labs/Lab-2-Challenge-Unsolved/README.ar.md)** (تحدٍ يواجهه المتدرب بنفسه مع قالب التقرير الأمني)

---

## 🏆 مشروع المستوى الثالث التتويجي: لوحة التحكم الأمنية المحصنة
لوحة تحكم تفاعلية متعددة السمات، مطابقة لأعلى معايير الوصول، ومحصنة بالكامل:
- 👉 [دليل وكود المشروع بالإنجليزية](./Final-Project/en/README.md)
- 👉 [دليل وكود المشروع بالعربية](./Final-Project/ar/README.md)
