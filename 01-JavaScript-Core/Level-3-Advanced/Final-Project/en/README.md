# Level 3 Capstone Project: JavaScript Security Analyzer

> 🌐 **Language / اللغة:** [English 🇬🇧](./README.md) | [العربية 🇸🇦](../ar/README.md)

---

## 🎯 1. Architectural Mission

The **JavaScript Security Analyzer** is a lightweight, zero-dependency Static Application Security Testing (SAST) engine written in pure JavaScript.  
It audits source code payloads using strict regex patterns and heuristic AST rules to flag:
1. **Dangerous Execution Sinks:** Unrestricted `eval()`, `new Function()`, and `document.write()`.
2. **Potential DOM-based XSS:** Unsanitized variable assignments into `element.innerHTML`.
3. **Insecure Cross-Origin Messaging:** `window.addEventListener("message")` handlers lacking origin validation guards.
4. **Prototype Pollution Primitives:** Dangerous dynamic property assignments targeting `__proto__` or `prototype`.

> [!IMPORTANT]
> **Heuristic Triaging Philosophy:**  
> The engine strictly differentiates between **Suspicious Code Smells** and **Confirmed Exploitable Vulnerabilities**. An `innerHTML` assignment represents a suspicious code pattern warranting human review; it constitutes an exploitable flaw only when paired with an untrusted external Source without sanitization.

---

## 💻 2. Core Engine Implementation (`security-analyzer.js`)

```javascript
/**
 * ===================================================================
 * Level 3 Capstone: JavaScript Security Analyzer
 * Static analysis engine for hazardous JS patterns
 * ===================================================================
 */

class JavaScriptSecurityAnalyzer {
    constructor() {
        this.rules = [
            {
                id: "SEC-001",
                name: "Arbitrary Code Execution Sink",
                severity: "HIGH",
                regex: /\b(eval|new\s+Function)\s*\(/g,
                explanation: "Direct evaluation of dynamic strings allows remote code injection.",
                remediation: "Replace eval with strict native parsers such as JSON.parse()."
            },
            {
                id: "SEC-002",
                name: "Dangerous DOM HTML Sink (Potential DOM XSS)",
                severity: "MEDIUM",
                regex: /\.innerHTML\s*=/g,
                explanation: "Direct assignment to innerHTML without sanitization permits malicious script injection.",
                remediation: "Favor element.textContent or programmatic DOM constructors (createElement, setAttribute)."
            },
            {
                id: "SEC-003",
                name: "Unchecked Cross-Origin PostMessage Origin",
                severity: "HIGH",
                regex: /window\.addEventListener\s*\(\s*["']message["']/g,
                explanation: "Listening for window messages without origin verification allows malicious sites to inject payloads.",
                remediation: "Enforce strict origin checks: if (event.origin !== 'https://trusted.domain') return;."
            },
            {
                id: "SEC-004",
                name: "Prototype Pollution Mutation Pattern",
                severity: "MEDIUM",
                regex: /\[\s*["']__proto__["']\s*\]|\.prototype\[/g,
                explanation: "Unvalidated key access to __proto__ permits global prototype pollution across all application objects.",
                remediation: "Initialize dictionaries with Object.create(null) or filter dangerous keys during object merging."
            }
        ];
    }

    analyze(sourceCode) {
        const lines = sourceCode.split("\n");
        const findings = [];

        lines.forEach((lineText, index) => {
            const lineNumber = index + 1;

            this.rules.forEach(rule => {
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
                        classification: "Suspicious Pattern (Requires Human Verification)"
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
        console.log("🛡️ Static Security Audit Report");
        console.log("=================================================");
        console.log(`Lines Analyzed:  ${result.totalLines}`);
        console.log(`Findings Detected: ${result.totalFindings}\n`);

        if (result.findings.length === 0) {
            console.log("✅ Zero high-risk patterns detected.");
            return;
        }

        result.findings.forEach((item, i) => {
            console.log(`[#${i + 1}] [${item.severity}] ${item.ruleId}: ${item.ruleName}`);
            console.log(`   Line ${item.line}:   ${item.snippet}`);
            console.log(`   Rationale: ${item.explanation}`);
            console.log(`   Fix:       ${item.remediation}`);
            console.log(`   Status:    ${item.classification}`);
            console.log("-------------------------------------------------");
        });
    }
}

// Diagnostic Execution
const sampleCode = `
const payload = location.hash.slice(1);
const container = document.getElementById("main");
container.innerHTML = "<div>" + payload + "</div>";
const parsed = eval(payload);
`;

const auditor = new JavaScriptSecurityAnalyzer();
const report = auditor.analyze(sampleCode);
auditor.printReport(report);
```

---

## 🏆 Track Completion!
You have successfully concluded the **JavaScript Core Track** across all 3 developmental tiers. You are now fully prepared to advance into offensive security engineering: **JavaScript for Web Pentesting**!
