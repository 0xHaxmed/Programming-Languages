# مشروع نهاية المستوى الثالث: JavaScript Security Analyzer

> 🌐 **اللغة / Language:** [العربية 🇸🇦](./README.md) | [English 🇬🇧](../en/README.md)

---

## 🎯 1. فكرة وهدف المشروع

مشروع **JavaScript Security Analyzer** هو أداة فحص كود ساكن (Static Application Security Testing - SAST) مصغرة ومكتوبة بلغة JavaScript الصافية.  
تقوم الأداة بقراءة ملفات أو نصوص JavaScript، وتحليل أسطرها باستخدام التعابير النمطية (RegEx) وقواعد الفحص الأمني، للبحث عن:
1. **الأنماط البرمجية الخطرة (Dangerous Sinks):** مثل استخدام `eval()` أو `new Function()` أو `document.write()`.
2. **ثغرات الـ DOM-XSS المحتملة:** مثل إسناد متغيرات غير مفحوصة لـ `element.innerHTML`.
3. **أخطاء التراسل بين النوافذ (Insecure postMessage):** استقبال أحداث `message` بدون التحقق من خاصية `event.origin`.
4. **التعديل الخطر على النماذج (Prototype Pollution Patterns):** تعيين خصائص كـ `__proto__` أو `prototype` ديناميكياً.

> [!IMPORTANT]
> **مبدأ جوهري للأداة:**  
> تلتزم الأداة بالتفريق الصارم بين **"شبهة لوجود نمط خطر" (Suspicious Pattern / Code Smell)** وبين **"إثبات وجود ثغرة فعلية قابلة للاستغلال" (Confirmed Exploit)**؛ فوجود `innerHTML` يُعد شبهة تستدعي المراجعة والتنبيه، ولكنه لا يعني حتمية وجود ثغرة إلا إذا ثبت أن المدخل مصدره المستخدم بدون تعقيم.

---

## 💻 2. كود نواة أداة الفحص (`security-analyzer.js`)

```javascript
/**
 * ===================================================================
 * مشروع نهاية المستوى الثالث: JavaScript Security Analyzer
 * أداة فحص ساكن للأنماط البرمجية الخطرة
 * ===================================================================
 */

class JavaScriptSecurityAnalyzer {
    constructor() {
        // تعريف قواعد الفحص الأمني
        this.rules = [
            {
                id: "SEC-001",
                name: "استخدام دوال التنفيذ المباشر (Arbitrary Code Execution Sink)",
                severity: "HIGH",
                regex: /\b(eval|new\s+Function)\s*\(/g,
                explanation: "استخدام eval أو new Function يتيح تنفيذ نصوص كأكواد برمجية، مما قد يؤدي لـ Code Injection خطير.",
                remediation: "استخدم الدوال المدمجة الآمنة و JSON.parse بدلاً من تقييم الأكواد برمجياً."
            },
            {
                id: "SEC-002",
                name: "حقن نصوص HTML في الـ DOM (Potential DOM XSS)",
                severity: "MEDIUM",
                regex: /\.innerHTML\s*=/g,
                explanation: "إسناد البيانات مباشرة لـ innerHTML قد يتيح للمهاجم حقن وسوم وسكربتات ضارة في المتصفح.",
                remediation: "استخدم textContent أو createElement و setAttribute بدلاً من innerHTML."
            },
            {
                id: "SEC-003",
                name: "استقبال رسائل postMessage دون التحقق من المصدر (Unchecked Origin)",
                severity: "HIGH",
                regex: /window\.addEventListener\s*\(\s*["']message["']/g,
                explanation: "الاستماع لرسائل postMessage دون فحص شرط (event.origin === 'المصدر_الموثوق') يتيح لأي موقع خبيث إرسال بيانات لتطبيقك.",
                remediation: "تحقق دائماً من event.origin وتطابق النطاق الموثوق قبل معالجة أي بيانات."
            },
            {
                id: "SEC-004",
                name: "شبهة تعديل على كائن الـ Prototype (Potential Prototype Pollution)",
                severity: "MEDIUM",
                regex: /\[\s*["']__proto__["']\s*\]|\.prototype\[/g,
                explanation: "الوصول المباشر أو التعيين الديناميكي لـ __proto__ قد يسمح بتعديل النماذج وتغيير سلوك كائنات البرنامج ككل.",
                remediation: "استخدم Object.create(null) أو احظر مفاتيح __proto__ و constructor أثناء نسخ الكائنات."
            }
        ];
    }

    analyze(sourceCode) {
        const lines = sourceCode.split("\n");
        const findings = [];

        lines.forEach((lineText, index) => {
            const lineNumber = index + 1;

            this.rules.forEach(rule => {
                // إعادة تصفير مؤشر الـ RegExp
                rule.regex.lastIndex = 0;
                if (rule.regex.test(lineText)) {
                    findings.push({
                        ruleId: rule.id,
                        ruleName: rule.name,
                        severity: rule.severity,
                        line: lineNumber,
                        snippet: lineText.trim(),
                        explanation: rule.explanation,
                        remediation: rule.remediation,
                        classification: "اشتباه نمط (Suspicious Pattern) - يتطلب مراجعة بشرية"
                    });
                }
            });
        });

        return {
            totalLines: lines.length,
            totalFindings: findings.length,
            findings: findings
        };
    }

    printReport(result) {
        console.log("=================================================");
        console.log("🛡️ تقرير الفحص الأمني (JavaScript Security Audit)");
        console.log("=================================================");
        console.log(`عدد الأسطر المفحوصة: ${result.totalLines}`);
        console.log(`إجمالي الملاحظات المكتشفة: ${result.totalFindings}\n`);

        if (result.findings.length === 0) {
            console.log("✅ لم يتم العثور على أنماط أمنية مريبة في الكود!");
            return;
        }

        result.findings.forEach((item, i) => {
            console.log(`[#${i + 1}] [${item.severity}] ${item.ruleId}: ${item.ruleName}`);
            console.log(`   السطر ${item.line}: ${item.snippet}`);
            console.log(`   السبب:      ${item.explanation}`);
            console.log(`   طريقة الحل: ${item.remediation}`);
            console.log(`   التصنيف:    ${item.classification}`);
            console.log("-------------------------------------------------");
        });
    }
}

// -------------------------------------------------------------------
// تجربة الأداة على كود تجريبي
// -------------------------------------------------------------------
const sampleVulnerableCode = `
const userInput = location.hash.slice(1);
const outputDiv = document.getElementById("profile");

// كود مشبوه: استخدام innerHTML
outputDiv.innerHTML = "<h3>مرحباً " + userInput + "</h3>";

// كود مشبوه: استخدام eval
const calc = eval(userInput);

// كود سليم
console.log("تم تحديث الملف الشخصي بنجاح");
`;

const analyzer = new JavaScriptSecurityAnalyzer();
const report = analyzer.analyze(sampleVulnerableCode);
analyzer.printReport(report);
```

---

## 🏆 خاتمة المسار
بإتمامك هذا المشروع بنجاح، تكون قد أتممت **مسار JavaScript Core الشامل بمستوياته الثلاثة**!  
أنت الآن تمتلك الأساس البرمجي والمنطقي الصلب للانتقال إلى مسار اختبار الاختراق واكتشاف الثغرات المتقدمة (**JavaScript for Web Pentesting**).
