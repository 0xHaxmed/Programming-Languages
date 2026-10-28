# مشروع نهاية المستوى الثاني: API Explorer

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./README.md) | [English 🇬🇧](../en/README.md)

---

## 🎯 1. فكرة وهدف المشروع

مشروع **API Explorer** هو تطبيق ويب تفاعلي يُحاكي أدوات فحص وتجربة الـ APIs مثل Postman مصغر يعمل داخل المتصفح مباشرة.
الهدف من المشروع هو تطبيق جميع مهارات المستوى الثاني عملياً:
- استخدام دالة `fetch()` لإرسال طلبات HTTP حقيقية (GET, POST).
- التعامل مع ردود الـ JSON وعرضها وتنسيقها داخل واجهة المستخدم.
- التعامل مع أخطاء الشبكة والـ HTTP Status Codes (مثل 404 أو 500) وعرض رسائل خطأ واضحة.
- توفير بيانات تجريبية محلية (Mock Data) في حال كان الجهاز غير متصل بالإنترنت.
- الاستماع للأحداث (Click, Submit) وتعديل عناصر الـ DOM بأمان بدون ثغرات حقن.

---

## 🏗️ 2. المميزات الرئيسية للتطبيق

1. **مُحدد نوع الطلب (HTTP Method Selector):** دعم طلبات `GET` و `POST`.
2. **حقل إدخال الرابط (Endpoint URL Input):** مع روابط تجريبية جاهزة مثل JSONPlaceholder.
3. **محرر الرؤوس والبيانات (Headers & Body Editor):** إمكانية إرسال `Content-Type: application/json` وبيانات مخصصة.
4. **شاشة عرض النتائج المنمقة (Formatted JSON Viewer):** عرض الكود بتنسيق ملون مع إظهار كود الحالة (Status Code) ووقت الاستجابة بالمللي ثانية.
5. **وضع البيانات التجريبية (Mock Mode):** زر يتيح اختبار التطبيق محلياً بدون خادم حقيقي.

---

## 📁 3. هيكل ملفات المشروع المقترح

```plaintext
API-Explorer/
├── index.html        # واجهة المستخدم والهيكل الأساسي
├── style.css         # تنسيقات الواجهة ونظام الألوان الداكن/الفاتح
└── app.js            # منطق إرسال الطلبات، معالجة الـ DOM والـ Errors
```

---

## 💻 4. النواة البرمجية للمشروع (`app.js`)

```javascript
/**
 * نواة مشروع API Explorer
 */
async function sendRequest(url, method = "GET", payload = null) {
    const startTime = performance.now();
    const resultContainer = document.getElementById("results");

    try {
        resultContainer.textContent = "جاري إرسال الطلب وجلب البيانات...";

        const options = {
            method: method,
            headers: {
                "Content-Type": "application/json"
            }
        };

        if (payload && (method === "POST" || method === "PUT")) {
            options.body = JSON.stringify(payload);
        }

        const response = await fetch(url, options);
        const duration = Math.round(performance.now() - startTime);

        const data = await response.json();

        // عرض تفاصيل الرد بأمان باستخدام textContent
        renderResponse({
            status: response.status,
            statusText: response.statusText,
            timeMs: duration,
            data: data
        });

    } catch (error) {
        renderError("فشل الاتصال بالشبكة: " + error.message);
    }
}

function renderResponse({ status, statusText, timeMs, data }) {
    console.log(`[HTTP ${status} ${statusText}] - استغرق: ${timeMs}ms`);
    console.table(data);
}

function renderError(message) {
    console.error("[خطأ في الشبكة]:", message);
}
```

---

⬅️ [دروس المستوى الثاني](../../Lessons/ar/README.md) | ➡️ [الانتقال إلى المستوى الثالث: Level 3 Advanced](../../../Level-3-Advanced/README.md)
