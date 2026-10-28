# 11 - التعامل مع النصوص وخصائصها (String Handling)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./11-string-handling.md) | [English 🇬🇧](../en/11-string-handling.md)

---

## 🔤 1. طبيعة النصوص في جافا سكريبت

النصوص (Strings) هي سلاسل من الحروف والرموز. في جافا سكريبت، النصوص هي **أنواع أولية غير قابلة للتغيير الداخلي (Immutable)**، بمعنى أن أي دالة تطبقها على النص لا تغير النص الأصلي نفسه، بل تُنتج وترجع **نصاً جديداً تماماً**.

---

## 📏 2. طول النص والفهرسة (Length & Indexing)

```javascript
const word = "JavaScript";

console.log(word.length);   // 10 (عدد الحروف)
console.log(word[0]);       // "J" (الحرف الأول)
console.log(word[word.length - 1]); // "t" (الحرف الأخير)
```

---

## 🛠️ 3. أهم دوال معالجة النصوص (String Methods)

### أ. تغيير حالة الحروف (Case Conversion)
```javascript
const text = "Hello World";
console.log(text.toLowerCase()); // "hello world"
console.log(text.toUpperCase()); // "HELLO WORLD"
```

### ب. تنظيف المسافات الفارغة (Trimming)
مفيدة جداً لتنظيف مدخلات المستخدم وحقول البريد الإلكتروني:
```javascript
const emailInput = "   user@example.com   \n";
console.log(emailInput.trim()); // "user@example.com"
```

### ج. البحث والفحص (Searching)
```javascript
const sentence = "تعلم لغة جافا سكريبت ممتع ومفيد";

console.log(sentence.includes("جافا سكريبت")); // true
console.log(sentence.startsWith("تعلم"));       // true
console.log(sentence.endsWith("مفيد"));        // true
console.log(sentence.indexOf("لغة"));         // 5 (مكان بداية الكلمة)
```

### د. استقطاع جزء من النص (`slice`)
```javascript
const code = "JS-2026-PRO";

// استقطاع من الفهرس 3 إلى 7 (غير شامل 7):
console.log(code.slice(3, 7)); // "2026"

// استقطاع آخر 3 حروف:
console.log(code.slice(-3));   // "PRO"
```

### هـ. الاستبدال (`replace` و `replaceAll`)
```javascript
const message = "قطة رمادية مع قطة بيضاء";

// replace تستبدل أول ظهور فقط:
console.log(message.replace("قطة", "عصفورة")); 
// "عصفورة رمادية مع قطة بيضاء"

// replaceAll تستبدل جميع الكلمات المطابقة:
console.log(message.replaceAll("قطة", "حمامة")); 
// "حمامة رمادية مع حمامة بيضاء"
```

### و. تقسيم النص إلى مصفوفة (`split`)
تحويل النص إلى قائمة عناصر بناءً على فاصل معين:
```javascript
const tags = "برمجة,جافاسكريبت,ويب,تطوير";
const tagsArray = tags.split(",");
console.log(tagsArray); // ["برمجة", "جافاسكريبت", "ويب", "تطوير"]
```

---

## 📝 4. قوالب النصوص الحديثة (Template Literals)

باستخدام علامة الباك-تك (\`\`)، يمكنك دمج المتغيرات والتعابير الحسابية وكتابة نصوص على عدة أسطر بكل سلاسة:

```javascript
const userName = "طارق";
const totalTasks = 5;
const completedTasks = 3;

const summary = `
مرحباً يا ${userName}،
لديك ${totalTasks - completedTasks} مهام متبقية لإنجازها اليوم!
`;

console.log(summary);
```

---

## 🎯 ملخص وتحدي الدرس

- النصوص غير قابلة للتغيير المباشر في الذاكرة (Immutable).
- `slice` و `trim` و `split` و `includes` هي أكثر دوال النصوص استخداماً.
- Template Literals تحل محل الجمع التقليدي بالـ `+`.

**التحدي:**  
اكتب دالة باسم `maskEmail(email)` تقوم بإخفاء جزء من البريد الإلكتروني لحماية الخصوصية:  
مثلاً: إذا كان المدخل `"ahmed@gmail.com"` تُرجع `"a***d@gmail.com"`.  
(تلميح: استخدم `split("@")` و `slice`).

---

**الانتقال إلى:**  
⬅️ [الدرس السابق: هياكل البيانات](./10-basic-data-structures.md) | ➡️ [الدرس التالي: معالجة الأخطاء والتصحيح (Error Handling)](./12-error-handling.md)
