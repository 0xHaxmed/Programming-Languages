# 06 - العمليات الحسابية والمنطقية والمقارنة (Operators)

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./06-operators.md) | [English 🇬🇧](../en/06-operators.md)

---

## 🧮 1. العمليات الحسابية (Arithmetic Operators)

تُستخدم لإجراء العمليات الرياضية على القيم العددية:

| المعامل | الوصف | مثال | الناتج |
| :---: | :--- | :--- | :---: |
| `+` | الجمع | `10 + 5` | `15` |
| `-` | الطرح | `10 - 4` | `6` |
| `*` | الضرب | `4 * 3` | `12` |
| `/` | القسمة | `20 / 4` | `5` |
| `%` | باقي القسمة (Modulo) | `7 % 3` | `1` |
| `**` | الأس (Exponentiation) | `2 ** 3` (أي $2^3$) | `8` |
| `++` | الزيادة بواحد (Increment) | `let x = 5; x++;` | `6` |
| `--` | النقصان بواحد (Decrement) | `let y = 5; y--;` | `4` |

---

## 📝 2. معاملات التعيين (Assignment Operators)

تُستخدم لإسناد وتحديث قيم المتغيرات بطريقة مختصرة:

```javascript
let score = 10;

score += 5;  // تكافئ: score = score + 5; (الناتج: 15)
score -= 3;  // تكافئ: score = score - 3; (الناتج: 12)
score *= 2;  // تكافئ: score = score * 2; (الناتج: 24)
score /= 4;  // تكافئ: score = score / 4; (الناتج: 6)
```

---

## ⚖️ 3. معاملات المقارنة (Comparison Operators)

تُقارن بين قيمتين وتُرجع دائماً قيمة منطقية (`true` أو `false`):

### مقارنة القيمة والنوع: `==` مقابل `===` (نقطة جوهرية!)

```javascript
// المقارنة الضعيفة (Loose Equality - تقارن القيمة فقط وتعمل Coercion)
console.log(5 == "5");   // true (خطير!)
console.log(0 == false); // true
console.log("" == 0);    // true

// المقارنة الصارمة (Strict Equality - تقارن القيمة والنوع معاً)
console.log(5 === "5");   // false (لأن الأول Number والثاني String)
console.log(0 === false); // false
console.log(5 === 5);     // true
```

> [!IMPORTANT]
> **قاعدة ثابتة:**  
> استخدم دائماً `===` و `!==` وتجنب `==` و `!=` تماماً لمنع الأخطاء غير المتوقعة في برامجك.

باقي معاملات المقارنة:
- `>` (أكبر من) و `<` (أصغر من)
- `>=` (أكبر من أو يساوي) و `<=` (أصغر من أو يساوي)

---

## 🚦 4. المعاملات المنطقية (Logical Operators)

### أ. AND (`&&`)
تُرجع `true` فقط إذا كانت **جميع الأطراف** صحيحة:
```javascript
console.log(true && true);   // true
console.log(true && false);  // false
```

### ب. OR (`||`)
تُرجع `true` إذا كان **طرف واحد على الأقل** صحيحاً:
```javascript
console.log(true || false);  // true
console.log(false || false); // false
```

### ج. NOT (`!`)
تعكس القيمة المنطقية:
```javascript
console.log(!true);  // false
console.log(!false); // true
```

---

## ⚡ 5. خاصية القصر (Short-Circuit Evaluation) ودمج القيم الفارغة (`??`)

في جافا سكريبت، المعاملات المنطقية لا تُرجع مجرد `true` أو `false`، بل تُرجع **القيمة نفسها**:

```javascript
// الـ OR (||) يبحث عن أول قيمة Truthy:
let userChosenColor = "";
let defaultColor = "أزرق";
let finalColor = userChosenColor || defaultColor;
console.log(finalColor); // "أزرق"

// معامل الـ Nullish Coalescing (??):
// يتجاهل فقط null و undefined (لا يتجاهل 0 أو النص الفارغ ""):
let count = 0;
console.log(count || 10); // 10 (لأن 0 تعتبر Falsy في ||)
console.log(count ?? 10); // 0 (لأن 0 ليست null ولا undefined - تصرف دقيق وصحيح!)
```

---

## 🎯 ملخص وتحدي الدرس

- `===` تقارن القيمة والنوع بدون أي تحويل تلقائي.
- العمليات الحسابية تشمل Modulo (`%`) والأس (`**`).
- `??` هو البديل الآمن لـ `||` عند التعامل مع الصفر والنصوص الفارغة.

**التحدي:**  
اكتب تعبيراً برمجياً يتحقق مما إذا كان عمر المستخدم أكبر من أو يساوي 18 **و** يمتلك رخصة قيادة صالحة (`hasLicense = true`). إذا تحقق الشرط اطبع `مسموح بالقيادة`.

---

**الانتقال إلى:**  
⬅️ [الدرس السابق: تحويل الأنواع](./05-type-conversion.md) | ➡️ [الدرس التالي: الجمل الشرطية (Conditions)](./07-conditions.md)
