# 08 - هندسة إدارة الأخطاء وتحليلها: التسلسل، مكدس الاستدعاءات ومعالجة الانهيارات

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/08-error-analysis.md) | [العربية 🇸🇦](./08-error-analysis.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

في الأنظمة البرمجية الاحترافية وبيئات الإنتاج (Production)، لا تُعد الأخطاء مجرد حوادث استثنائية مفاجئة—بل هي أحداث تشغيلية طبيعية ومتوقعة يجب تصنيفها، وتتبع أصولها، وتحويلها إلى بيانات منظمة، والتعامل معها بنظام معماري دقيق. يؤدي الإهمال في إدارة الأخطاء إلى فشل صامت، وتجميد للنظام، وتسريب للذاكرة، بل وتسريب بيانات أمنية حساسة للمستخدمين.

بنهاية هذا الدرس، ستتقن تماماً:
1. التشريح البنيوي لكائن الخطأ الأصلي **`Error`** وخاصية تسلسل الأسباب **`cause`** الحديثة في ES2022.
2. بناء هيكلية هرمية مخصصة للأخطاء (Custom Error Hierarchy) مدعومة بأكواد HTTP ومعرفات تشغيلية.
3. التغلب على فخ تحويل الأخطاء لنصوص **`JSON.stringify(error)`** وتجهيزها لأنظمة المراقبة وسجلات الأحداث (Logging).
4. ضبط حواجز ومصائد الأخطاء الشاملة على مستوى التطبيق ككل (`unhandledrejection` و `window.onerror`).
5. تطبيق الحماية الدفاعية لمنع تسريب أسرار النظام والـ Stack Traces للمستخدم النهائي.

---

## 🔬 2. التشريح البنيوي لكائن الخطأ (Error Object)

يتكون كائن الخطأ القياسي في جافاسكريبت من خصائص أساسية:
- **`name`**: اسم وتصنيف الخطأ (مثل `'TypeError'` أو `'ReferenceError'`).
- **`message`**: وصف تقني مقروء لسبب الخطأ.
- **`stack`**: نص يحتوي على تتبع دقيق لمكدس الاستدعاءات وسطور الأكواد التي أدت للاستثناء (Call Frames).
- **`cause`** *(ميزة قياسية في ES2022)*: مرجع للخطأ الأصلي الداخلي الذي تسبب في ظهور هذا الخطأ.

### ربط الأخطاء المتسلسلة باستخدام `cause`:
عند تغليف أخطاء برمجية منخفضة المستوى بأخطاء عليا على مستوى النظام، احرص على حفظ الخطأ الأصلي دون ضياع عبر `{ cause: originalError }`:

```javascript
async function fetchUserBillingHistory(userId) {
  try {
    const response = await fetch(`/api/billing/${userId}`);
    if (!response.ok) {
      throw new Error(`خادم الفواتير استجاب بكود HTTP ${response.status}`);
    }
    return await response.json();
  } catch (networkError) {
    // نغلف الخطأ برسالة واضحة مع الحفاظ على السبب الأصلي كاملاً:
    throw new Error(`تعذر جلب سجل فواتير المستخدم ${userId}`, {
      cause: networkError
    });
  }
}

// فحص سلسلة الأسباب عند التقاط الخطأ:
try {
  await fetchUserBillingHistory('usr_8921');
} catch (error) {
  console.error('الخطأ عالي المستوى:', error.message);
  console.error('السبب الجذري الأصلي (Root Cause):', error.cause?.message);
}
```

---

## 🏗️ 3. بناء هيكلية هرمية مخصصة للأخطاء (Custom Error Hierarchy)

في التطبيقات المعقدة، يجب أن تحمل الأخطاء كود حالة HTTP، ورمز خطأ آلياً (Machine-Readable Error Code)، وبيانات تفصيلية:

```javascript
// فئة الخطأ الأساسية للتطبيق:
class AppError extends Error {
  constructor(message, { statusCode = 500, errorCode = 'INTERNAL_ERROR', isOperational = true, cause } = {}) {
    super(message, { cause });
    this.name = this.constructor.name;
    this.statusCode = statusCode;
    this.errorCode = errorCode;
    this.isOperational = isOperational; // للتمييز بين أخطاء التشغيل العادية وبين أخطاء المبرمج القاتلة

    // تنظيف مكدس الاستدعاءات وحذف سطر دالة البناء في محركات V8:
    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, this.constructor);
    }
  }
}

// الفئات الفرعية المخصصة حسب طبيعة الخطأ:
class ValidationError extends AppError {
  constructor(message, validationIssues = [], cause) {
    super(message, { statusCode: 400, errorCode: 'VALIDATION_FAILED', isOperational: true, cause });
    this.validationIssues = validationIssues;
  }
}

class NotFoundError extends AppError {
  constructor(resourceName, identifier) {
    super(`لم يتم العثور على [${resourceName}] بالمعرف [${identifier}]`, {
      statusCode: 404,
      errorCode: 'RESOURCE_NOT_FOUND',
      isOperational: true
    });
  }
}

class UnauthorizedError extends AppError {
  constructor(message = 'رمز المصادقة منتهي الصلاحية أو غير صالح') {
    super(message, { statusCode: 401, errorCode: 'UNAUTHORIZED', isOperational: true });
  }
}
```

### الاستهلاك النظيف والتحقق عبر `instanceof`:
```javascript
function handleApiError(error, res) {
  if (error instanceof ValidationError) {
    return res.status(error.statusCode).json({
      code: error.errorCode,
      message: error.message,
      issues: error.validationIssues
    });
  }

  if (error instanceof AppError && error.isOperational) {
    return res.status(error.statusCode).json({
      code: error.errorCode,
      message: error.message
    });
  }

  // في حال كان خطأ غير تشغيلي أو استثناء غير متوقع (يجب إخفاء التفاصيل الداخلية!):
  logger.error('CRITICAL UNHANDLED ERROR:', error);
  return res.status(500).json({
    code: 'INTERNAL_SERVER_ERROR',
    message: 'حدث خطأ داخلي غير متوقع في الخادم.'
  });
}
```

---

## ⚠️ 4. فخ `JSON.stringify` وتحويل الأخطاء إلى بيانات مسلسلة

يقع الكثير من المبرمجين في هذا الفخ الشهير: عند تمرير كائن `Error` إلى `JSON.stringify()`، يُرجع دائماً كائناً فارغاً `{}`!

```javascript
const err = new Error('انقطع الاتصال بقاعدة البيانات');
console.log(JSON.stringify(err)); // النتيجة: "{}" (فارغ تماماً!)
```

**لماذا يحدث هذا؟** في جافاسكريبت، تم تعريف خصائص كائن الخطأ (`name`, `message`, `stack`) بأنها خصائص غير قابلة للعد (**Non-Enumerable**)، وبالتالي تتجاهلها دالة `JSON.stringify` بالكامل.

### الحل: بناء دالة تسلسل احترافية (Error Serializer):
```javascript
function serializeError(error) {
  if (!(error instanceof Error)) {
    return { raw: error };
  }

  return {
    name: error.name,
    message: error.message,
    statusCode: error.statusCode || 500,
    errorCode: error.errorCode || 'UNKNOWN_ERROR',
    stack: error.stack,
    cause: error.cause ? serializeError(error.cause) : undefined,
    validationIssues: error.validationIssues || undefined
  };
}

// الآن يمكن إرسال الخطأ بأمان عبر شبكة السجلات أو WebSockets:
console.log(JSON.stringify(serializeError(err), null, 2));
```

---

## 🌐 5. حواجز الأخطاء العامة والوعود المعلقة (Unhandled Rejections)

مهما بلغت دقة الكود، قد تفلت بعض الأخطاء غير المتوقعة أو الوعود التي تفشل بلا `catch`. يجب الإمساك بها على مستوى التطبيق لتسجيلها وإبلاغ فرق المراقبة.

### في بيئة المتصفح:
```javascript
// 1. التقاط الوعود الفاشلة التي تم نسيان معالجتها (Unhandled Promise Rejections):
window.addEventListener('unhandledrejection', (event) => {
  console.error('تم التقاط وعد فاشل بلا catch:', event.reason);
  // إرسال تقرير لمنصة المراقبة (مثل Sentry):
  telemetryService.report({
    type: 'unhandled_promise',
    error: serializeError(event.reason)
  });
  // يمكن استخدام event.preventDefault() لمنع طباعة الخطأ في console المتصفح إن أردت
});

// 2. التقاط الاستثناءات التزامنية العامة:
window.onerror = function (message, source, lineno, colno, error) {
  telemetryService.report({
    type: 'runtime_exception',
    message,
    source,
    line: lineno,
    column: colno,
    stack: error?.stack
  });
  return false;
};
```

---

## 🛡️ 6. البرمجة الدفاعية: منع تسريب البيانات الحساسة (Information Disclosure)

تعد مخرجات تتبع المكدس (Stack Traces) ورسائل استعلامات قواعد البيانات الخام مصدراً خطيراً لتسريب معلومات معمارية سرية للمهاجمين، مثل مسارات ملفات السيرفر (`/var/app/secrets/db.js`)، أو أسماء الجداول وأعمدة قواعد البيانات:

```javascript
// ❌ كارثة أمنية: تسريب تفاصيل السيرفر الداخلية في استجابة الـ API
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message, stack: err.stack });
});

// ✅ كود آمن ودفاعي: فصل السجلات الداخلية عن الرسائل الموجهة للمستخدم
app.use((err, req, res, next) => {
  // 1. تسجيل التفاصيل التقنية الدقيقة في السجلات الداخلية للمطورين فقط:
  logger.error({
    traceId: req.id,
    stack: err.stack,
    sql: err.query
  });

  // 2. إرجاع رسالة نظيفة ومعقمة للمستخدم تحتوي على معرف التتبع فقط:
  const isProduction = process.env.NODE_ENV === 'production';
  res.status(err.statusCode || 500).json({
    traceId: req.id, // يمكن للمستخدم إعطاء هذا الرقم لفريق الدعم الفني
    message: isProduction ? 'حدث خطأ غير متوقع، يرجى المحاولة لاحقاً.' : err.message
  });
});
```

---

## 🧼 7. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: رمي نصوص عادية أو أرقام مجردة
throw 'User not found'; // يفقد تتبع السطور (Stack Trace) ويكسر فحص instanceof!

// ✅ جيد: دائماً ارمِ كائنات Error أو فئات مخصصة مشتقة منها
throw new NotFoundError('User', userId);

// ❌ سيء: ابتلاع الأخطاء في صمت (Empty Catch Block)
try {
  parseComplexXml(payload);
} catch (e) {
  // يموت الخطأ في صمت، ويستحيل تتبعه في الإنتاج!
}

// ✅ جيد: تسجيل الخطأ أو توفير مسار بديل آمن
try {
  parseComplexXml(payload);
} catch (e) {
  logger.warn('فشل تحليل XML، التحويل للمسار البديل JSON', { error: e.message });
  return parseFallbackJson(payload);
}
```

---

## 📝 8. ملخص الدرس والخطوة القادمة

- ارمِ دائماً كائنات مشتقة من `Error` ولا ترمِ نصوصاً مجردة أبداً.
- استخدم **خاصية `cause` الحديثة في ES2022** للحفاظ على سلسلة الأسباب الأصلية.
- ابنِ هيكلية هرمية واضحة للأخطاء تبدأ من فئة أساسية `AppError` وتحدد أكواد HTTP وأرقام المعرفات.
- انتبه لفخ **`JSON.stringify`** وقم بكتابة دالة تسلسل خاصة بالخصائص غير القابلة للعد.
- راقب الوعود المعلقة عبر **`unhandledrejection`** لمنع تسريب الأخطاء.
- احرص على حماية تفاصيل السيرفر وStack Traces من الظهور في استجابات الـ API للعملاء.
- في الدرس القادم، سنتناول **مراجعة الكود وهندسة التدفق: التحليل الساكن، تقليل التعقيد ومكافحة روائح الكود (Code Smells)**.
