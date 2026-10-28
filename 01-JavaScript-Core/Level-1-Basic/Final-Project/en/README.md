# Level 1 Capstone Project: JavaScript Logic Toolkit

> 🌐 **Language / اللغة:** [English 🇬🇧](./README.md) | [العربية 🇸🇦](../ar/README.md)

---

## 🎯 1. Architectural Objective

The **JavaScript Logic Toolkit** is a standalone, pure Vanilla JavaScript library consolidating all foundational principles acquired throughout Level 1:
- Pure functional abstractions with zero external dependencies.
- Defensive parameter verification and edge case handling.
- Algorithmic array and object manipulations.
- String tokenization and parsing.
- A custom, zero-dependency **Mini Test Runner** demonstrating assertion mechanics and regression testing.

---

## 📦 2. Library Modules

1. **Math Utilities:** Mean averaging, prime factorization, and numeric validations.
2. **String Utilities:** Palindrome symmetry verification, word counting, and title casing.
3. **Data Analysis Utilities:** Set frequency histograms and array deduplication.

---

## 💻 3. Complete Source & Runnable Test Suite (`toolkit.js`)

Save the following source code into a local `toolkit.js` file and execute via `node toolkit.js` or directly inside your browser dev console:

```javascript
/**
 * ===================================================================
 * Level 1 Capstone Project: JavaScript Logic Toolkit
 * ===================================================================
 */

const LogicToolkit = {
    // ---------------------------------------------------------------
    // 1. Math Utilities
    // ---------------------------------------------------------------
    math: {
        average(numbers) {
            if (!Array.isArray(numbers) || numbers.length === 0) return 0;
            let sum = 0;
            for (const n of numbers) {
                sum += n;
            }
            return sum / numbers.length;
        },

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
    // 2. String Utilities
    // ---------------------------------------------------------------
    string: {
        isPalindrome(str) {
            if (typeof str !== "string") return false;
            const cleaned = str.toLowerCase().replace(/[\W_]/g, "");
            const reversed = cleaned.split("").reverse().join("");
            return cleaned.length > 0 && cleaned === reversed;
        },

        countWords(str) {
            if (typeof str !== "string" || str.trim() === "") return 0;
            const words = str.trim().split(/\s+/);
            return words.length;
        },

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
    // 3. Data Analysis Utilities
    // ---------------------------------------------------------------
    data: {
        countOccurrences(arr) {
            if (!Array.isArray(arr)) return {};
            const counts = {};
            for (const item of arr) {
                counts[item] = (counts[item] || 0) + 1;
            }
            return counts;
        },

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
// 4. Custom Mini Assertion Engine
// -------------------------------------------------------------------
function runTests() {
    console.log("=========================================");
    console.log("🚀 Executing JavaScript Logic Toolkit Tests");
    console.log("=========================================\n");

    let passed = 0;
    let failed = 0;

    function assert(testName, actual, expected) {
        const isMatch = JSON.stringify(actual) === JSON.stringify(expected);
        if (isMatch) {
            console.log(`✅ [PASS]: ${testName}`);
            passed++;
        } else {
            console.error(`❌ [FAIL]: ${testName}`);
            console.error(`   Actual:   ${JSON.stringify(actual)}`);
            console.error(`   Expected: ${JSON.stringify(expected)}`);
            failed++;
        }
    }

    // Mathematical assertions
    assert("Computes arithmetic mean", LogicToolkit.math.average([10, 20, 30]), 20);
    assert("Identifies prime number (7)", LogicToolkit.math.isPrime(7), true);
    assert("Identifies composite number (4)", LogicToolkit.math.isPrime(4), false);

    // String manipulation assertions
    assert("Verifies palindrome ('racecar')", LogicToolkit.string.isPalindrome("racecar"), true);
    assert("Rejects non-palindrome ('hello')", LogicToolkit.string.isPalindrome("hello"), false);
    assert("Counts word tokens accurately", LogicToolkit.string.countWords("Modular JavaScript Core Guide"), 4);
    assert("Capitalizes multi-word string", LogicToolkit.string.capitalize("javascript core guide"), "Javascript Core Guide");

    // Data set assertions
    assert("Deduplicates scalar arrays", LogicToolkit.data.unique([1, 2, 2, 3, 1]), [1, 2, 3]);
    assert("Generates frequency histogram", LogicToolkit.data.countOccurrences(["a", "b", "a"]), { a: 2, b: 1 });

    console.log("\n-----------------------------------------");
    console.log(`📊 Diagnostic Summary: ${passed} Passed | ${failed} Failed`);
    console.log("-----------------------------------------");
}

runTests();
```

---

## 🏆 Level 1 Certified!
With all assertion gates passing, you have mastered fundamental syntax, variables, data structures, and defensive execution flow.  
Proceed to:  
➡️ **[Level 2 — Foundation (DOM, Browser APIs & HTTP Networking)](../../Level-2-Foundation/README.md)**
