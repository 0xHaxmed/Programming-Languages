# مشروع نهاية المستوى الأول: JavaScript Logic Toolkit

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./README.md) | [English 🇬🇧](../en/README.md)

---

## 🎯 1. فكرة وهدف المشروع

مشروع **JavaScript Logic Toolkit** هو حزمة أدوات منطقية برمجية متكاملة، مكتوبة بلغة جافا سكريبت الصافية (Vanilla JS).  
الهدف من المشروع هو دمج كل المهارات التي تعلمتها في المستوى الأول:
- التعامل مع المتغيرات والدوال النظيفة (Pure Functions).
- الشروط وحلقات التكرار والتحقق من صحة المدخلات (Data Validation).
- هياكل البيانات (Arrays & Objects).
- معالجة النصوص والأرقام.
- بناء نظام اختبارات تأكيدي ذاتي (Mini Test Runner) من الصفر بدون أي مكتبات خارجية!

---

## 📦 2. أقسام الأداة (Toolkit Modules)

تنقسم حزمة الأدوات إلى ثلاثة أقسام رئيسية:
1. **Math Utilities (أدوات الحسابات):** حساب المتوسط، الوسيط، وفحص الأعداد الأولية.
2. **String Utilities (أدوات معالجة النصوص):** فحص التناظر (Palindrome)، عد الكلمات، وتنسيق العناوين (Capitalization).
3. **Data Analysis Utilities (أدوات تحليل البيانات):** إيجاد القيم الفريدة، حساب التكرارات، وتصفية السجلات.

---

## 💻 3. كود المشروع المتكامل (`toolkit.js`)

يمكنك نسخ هذا الكود في ملف محلي باسم `toolkit.js` وتشغيله عبر `node toolkit.js` أو داخل المتصفح:

```javascript
/**
 * ===================================================================
 * مشروع نهاية المستوى الأول: JavaScript Logic Toolkit
 * ===================================================================
 */

const LogicToolkit = {
    // ---------------------------------------------------------------
    // 1. Math Utilities (العمليات الحسابية)
    // ---------------------------------------------------------------
    math: {
        // حساب المتوسط الحسابي لمصفوفة أرقام
        average(numbers) {
            if (!Array.isArray(numbers) || numbers.length === 0) return 0;
            let sum = 0;
            for (const n of numbers) {
                sum += n;
            }
            return sum / numbers.length;
        },

        // فحص ما إذا كان الرقم أولياً (Prime Number)
        isPrime(num) {
            if (num <= 1) return false;
            if (num <= 3) return true;
            if (num % 2 === 0 || num % 3 === 0) return false;
            for (let i = 5; i * i <= num; i += 6) {
                if (num % i === 0 || num % (i + 2) === 0) return false;
            }
            return true;
        }
    },

    // ---------------------------------------------------------------
    // 2. String Utilities (معالجة النصوص)
    // ---------------------------------------------------------------
    string: {
        // فحص هل الكلمة تقرأ من اليمين كالشمال (Palindrome) مثل: radar أو level
        isPalindrome(str) {
            if (typeof str !== "string") return false;
            const cleaned = str.toLowerCase().replace(/[\W_]/g, "");
            const reversed = cleaned.split("").reverse().join("");
            return cleaned.length > 0 && cleaned === reversed;
        },

        // عد الكلمات في النص
        countWords(str) {
            if (typeof str !== "string" || str.trim() === "") return 0;
            const words = str.trim().split(/\s+/);
            return words.length;
        },

        // تكبير أول حرف من كل كلمة (Title Case)
        capitalize(str) {
            if (typeof str !== "string") return "";
            const words = str.toLowerCase().split(" ");
            for (let i = 0; i < words.length; i++) {
                if (words[i].length > 0) {
                    words[i] = words[i][0].toUpperCase() + words[i].slice(1);
                }
            }
            return words.join(" ");
        }
    },

    // ---------------------------------------------------------------
    // 3. Data Analysis Utilities (تحليل البيانات)
    // ---------------------------------------------------------------
    data: {
        // حساب تكرار كل عنصر في المصفوفة
        countOccurrences(arr) {
            if (!Array.isArray(arr)) return {};
            const counts = {};
            for (const item of arr) {
                counts[item] = (counts[item] || 0) + 1;
            }
            return counts;
        },

        // إيجاد القيم الفريدة (غير المكررة)
        unique(arr) {
            if (!Array.isArray(arr)) return [];
            const result = [];
            for (const item of arr) {
                if (!result.includes(item)) {
                    result.push(item);
                }
            }
            return result;
        }
    }
};

// -------------------------------------------------------------------
// 4. Mini Test Runner (محرك اختبارات بسيط للتحقق من الدوال)
// -------------------------------------------------------------------
function runTests() {
    console.log("=========================================");
    console.log("🚀 تشغيل اختبارات JavaScript Logic Toolkit");
    console.log("=========================================\n");

    let passed = 0;
    let failed = 0;

    function assert(testName, actual, expected) {
        const isMatch = JSON.stringify(actual) === JSON.stringify(expected);
        if (isMatch) {
            console.log(`✅ [نجح]: ${testName}`);
            passed++;
        } else {
            console.error(`❌ [فشل]: ${testName}`);
            console.error(`   الناتج الفعلي:   ${JSON.stringify(actual)}`);
            console.error(`   الناتج المتوقع: ${JSON.stringify(expected)}`);
            failed++;
        }
    }

    // اختبارات الحساب
    assert("حساب المتوسط", LogicToolkit.math.average([10, 20, 30]), 20);
    assert("فحص رقم أولي (7)", LogicToolkit.math.isPrime(7), true);
    assert("فحص رقم غير أولي (4)", LogicToolkit.math.isPrime(4), false);

    // اختبارات النصوص
    assert("فحص Palindrome لكلمة 'racecar'", LogicToolkit.string.isPalindrome("racecar"), true);
    assert("فحص Palindrome لكلمة 'hello'", LogicToolkit.string.isPalindrome("hello"), false);
    assert("عد الكلمات في جملة", LogicToolkit.string.countWords("تعلم لغة جافا سكريبت ممتع"), 4);
    assert("تكبير الحروف", LogicToolkit.string.capitalize("javascript core guide"), "Javascript Core Guide");

    // اختبارات البيانات
    assert("استخراج القيم الفريدة", LogicToolkit.data.unique([1, 2, 2, 3, 1]), [1, 2, 3]);
    assert("حساب التكرارات", LogicToolkit.data.countOccurrences(["a", "b", "a"]), { a: 2, b: 1 });

    console.log("\n-----------------------------------------");
    console.log(`📊 النتيجة النهائية: ${passed} نجح | ${failed} فشل`);
    console.log("-----------------------------------------");
}

runTests();
```

---

## 🏆 تهانينا!
إذا قمت بتشغيل الكود ونجحت جميع الاختبارات، فأنت رسمياً أنهيت **Level 1 — Basic**!  
جاهز الآن للانتقال إلى المستوى التالي:  
➡️ **[Level 2 — Foundation (المتصفح، والـ DOM، والـ APIs)](../../Level-2-Foundation/README.md)**
