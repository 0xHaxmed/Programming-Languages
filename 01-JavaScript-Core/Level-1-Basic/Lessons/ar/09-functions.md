# 09 - الدوال والمعاملات (Functions, Parameters & Return)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./09-functions.md) | [English 🇬🇧](../en/09-functions.md)

---

## 🧩 1. ما هي الدالة (Function)؟

الدالة هي كتلة برمجية مستقلة تحمل اسماً محدداً، تحتوي على مجموعة من الأوامر لتنفيذ مهمة معينة. الفائدة الكبرى من الدوال هي:
1. **إعادة استخدام الكود (DRY - Don't Repeat Yourself):** بدلاً من تكرار 20 سطراً في أماكن متعددة، تستدعي الدالة باسمها فقط.
2. **تنظيم وتبسيط الكود:** تقسيم المشاكل المعقدة إلى وحدات صغيرة ومفهومة.

---

## 🛠️ 2. تعريف واستدعاء الدوال (Declaration vs Call)

```javascript
// 1. تعريف الدالة (Function Declaration)
function sayHello() {
    console.log("مرحباً بك في عالم الدوال!");
}

// 2. استدعاء الدالة (Function Call / Invocation)
sayHello();
sayHello();
```

---

## 📥 3. المعاملات والوسائط (Parameters vs Arguments)

- **Parameter (المعامل):** هو المتغير الوهمي المكتوب في **أقواس تعريف الدالة** (يستقبل البيانات).
- **Argument (الوسيط):** هو القيمة الفعلية التي تمررها للدالة عند **استدعائها**.

```javascript
// name و age هنا هما Parameters
function introduce(name, age) {
    console.log(`أهلاً، أنا ${name} وعمري ${age} سنة.`);
}

// "يوسف" و 22 هنا هما Arguments
introduce("يوسف", 22);
introduce("مريم", 26);
```

### القيم الافتراضية (Default Parameters)
يمكنك وضع قيمة افتراضية للمعامل في حال لم يمرر المستخدم قيمة:
```javascript
function greetUser(name = "زائرنا العزيز") {
    console.log(`أهلاً بك يا ${name}!`);
}

greetUser();          // أهلاً بك يا زائرنا العزيز!
greetUser("إبراهيم"); // أهلاً بك يا إبراهيم!
```

---

## 📤 4. جملة الإرجاع (`return`)

الدالة لا تقتصر على طباعة النصوص فقط، بل صُممت في الأصل لـ **حساب قيمة وإرجاعها** لتستخدمها في باقي البرنامج:

```javascript
function calculateTotal(price, taxRate) {
    const total = price + (price * taxRate);
    return total; // ترجع القيمة للسطر الذي استدعاها
}

const finalBill = calculateTotal(100, 0.14);
console.log(`الفاتورة الإجمالية شاملة الضريبة: ${finalBill} جنيه`);
```

> [!IMPORTANT]
> **قاعدة هامة:**  
> كلمة `return` تنهي عمل الدالة فوراً! أي سطر كود يوضع بعدها داخل نفس الدالة لن يتم تنفيذه أبداً (Unreachable Code).

---

## 🏹 5. طرق كتابة الدوال في جافا سكريبت

### أ. Function Declaration
```javascript
function add(a, b) {
    return a + b;
}
```

### ب. Function Expression (تخزين الدالة في متغير)
```javascript
const multiply = function(a, b) {
    return a * b;
};
```

### ج. Arrow Function (الدالة السهمية - ES6)
طريقة حديثة ومختصرة جداً شائعة في مشاريع جافا سكريبت الحديثة:
```javascript
const subtract = (a, b) => a - b;
console.log(subtract(10, 4)); // 6
```

---

## 🎯 ملخص وتحدي الدرس

- الدوال تعزز إعادة استخدام الكود وتمنع التكرار.
- الـ Parameters هي المتغيرات في التعريف، والـ Arguments هي القيم أثناء الاستدعاء.
- `return` تُرجع النتيجة وتُنهي تنفيذ الدالة فوراً.

**التحدي:**  
اكتب دالة باسم `convertCelsiusToFahrenheit(celsius)` تأخذ درجة الحرارة بالمئوية وترجع قيمتها بالفهرنهايت باستخدام المعادلة:  
$$F = (C \times \frac{9}{5}) + 32$$  
ثم استدعِ الدالة لدرجة حرارة 25 واطبع النتيجة.

---

**الانتقال إلى:**  
⬅️ [الدرس السابق: حلقات التكرار](./08-loops.md) | ➡️ [الدرس التالي: هياكل البيانات الأساسية (Arrays & Objects)](./10-basic-data-structures.md)
