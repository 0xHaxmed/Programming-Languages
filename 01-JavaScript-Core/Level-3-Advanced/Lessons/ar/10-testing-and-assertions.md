# 10 - هندسة الاختبارات والتحقق: محرك اختبارات Node.js المدمج، المحاكاة وحتمية النتائج

> 🌐 **Language / اللغة:** [English 🇬🇧](../en/10-testing-and-assertions.md) | [العربية 🇸🇦](./10-testing-and-assertions.md)

---

## 🎯 1. نظرة عامة وأهداف التعلم (Overview & Learning Objectives)

الاختبارات المؤتمتة (Automated Testing) هي شبكة الأمان الأساسية في هندسة البرمجيات الحديثة. فبدون اختبارات قوية وموثوقة، تصبح إعادة هيكلة الأكواد مخاطرة كبرى، وتتسلل أخطاء التراجع (Regressions) باستمرار، وتتباطأ سرعة إطلاق التحديثات. ورغم شهرة أطر العمل الخارجية مثل Jest أو Vitest، فإن بيئات تشغيل جافاسكريبت الحديثة (Node.js 18+ و 20+) تأتي الآن مزودة بـ **محرك اختبارات مدمج رسمي عالي الأداء دون الحاجة لتثبيت أي مكتبات خارجية (Zero-Dependency)** عبر `node:test` ومكتبة التحقق الصارمة `node:assert/strict`.

بنهاية هذا الدرس، ستتقن تماماً:
1. التوزيع المتوازن لـ **هرم الاختبارات (Testing Pyramid)**: اختبارات الوحدة (Unit)، التكامل (Integration)، والنظام الشامل (E2E).
2. كتابة مجموعات اختبارات حتمية ومنضبطة باستخدام **`node:test`** و **`node:assert/strict`**.
3. عزل المؤثرات الجانبية الخارجية باستخدام **بدائل الاختبار (Test Doubles)**: الموك (Mocks) والجواسيس (Spies) عبر واجهة `t.mock`.
4. اختبار الدوال غير المتزامنة (Async Code) ومسارات الرفض والرمي المباشر للأخطاء (`assert.rejects`).
5. تجنب الاختبارات الهشة (Brittle Tests) والتركيز على سلوك النظام الظاهر بدلاً من التفاصيل الداخلية.

---

## 🔺 2. هرم الاختبارات ومبادئ الحتمية (FIRST Principles)

```
             / \
            / E2E \          (الأقل عدداً: الأبطأ، الأعلى تكلفة، والأكثر عرضة لعدم الاستقرار)
           /-------\
          /  Integ  \        (متوسط: يختبر التكامل وتفاعل المكونات والـ APIs)
         /-----------\
        /  Unit Tests \      (الأكثر عدداً: فائق السرعة، معزول في الذاكرة، حتمي 100%)
       +---------------+
```

### مبادئ FIRST للاختبارات الاحترافية:
- **سريع (Fast):** تشغيل آلاف الاختبارات في ثوانٍ معدودة.
- **معزول (Isolated):** لا يعتمد أي اختبار على نتائج أو حالة اختبار آخر.
- **قابل للتكرار (Repeatable):** تشغيل الاختبار 1,000 مرة يعطي نفس النتيجة الحتمية دائماً.
- **ذاتي التحقق (Self-Validating):** النجاح أو الفشل آلي وصريح ولا يتطلب فحصاً يدوياً للسجلات.
- **مواكب (Timely):** يُكتب بالتزامن مع كود التطوير أو قبله (TDD).

---

## 🧪 3. محرك اختبارات Node.js المدمج والتحقق الصارم

لا حاجة لتثبيت حزم npm ثقيلة بمئات التبعيات. يوفر Node.js كل ما تحتاجه جاهزاً في النواة:

### كتابة مجموعة الاختبارات الأولى:
```javascript
// math.test.js
import { describe, it, beforeEach } from 'node:test';
import assert from 'node:assert/strict'; // احرص دائماً على استخدام /strict!

// الكود البرمجي المراد اختباره:
function calculateTax(subtotal, rate = 0.1) {
  if (typeof subtotal !== 'number' || subtotal < 0) {
    throw new TypeError('يجب أن يكون المبلغ رقماً موجباً');
  }
  return Math.round(subtotal * rate * 100) / 100;
}

describe('دالة حساب الضريبة calculateTax()', () => {
  it('يجب أن تحسب نسبة الضريبة القياسية 10% بشكل صحيح', () => {
    const result = calculateTax(100);
    assert.strictEqual(result, 10);
  });

  it('يجب أن تتعامل مع نسب الضرائب المخصصة والتقريب المالي', () => {
    const result = calculateTax(49.99, 0.0825);
    assert.strictEqual(result, 4.12);
  });

  it('يجب أن ترمي خطأ من نوع TypeError إذا كان المبلغ سالباً', () => {
    assert.throws(
      () => calculateTax(-50),
      {
        name: 'TypeError',
        message: 'يجب أن يكون المبلغ رقماً موجباً'
      }
    );
  });
});
```

### تشغيل الاختبارات من الطرفية (Terminal):
```bash
node --test
# أو وضع المراقبة الحية أثناء التعديل:
node --test --watch
```

---

## 🎭 4. بدائل الاختبار والمحاكاة (Mocks & Spies)

تُستخدم **بدائل الاختبار (Test Doubles)** لاستبدال الخدمات الخارجية الحقيقية (مثل بوابات الدفع أو خوادم البريد الإلكتروني):
- **Stub:** دالة بديلة تُرجع إجابة مجهزة مسبقاً أثناء الاختبار.
- **Spy:** دالة تسجل عدد مرات استدعائها والمعاملات التي استقبلتها للتأكد منها لاحقاً.
- **Mock:** كائن محاكاة مبرمج بتوقعات مسبقة لما يجب أن يتلقاه.

### استخدام المحاكاة الأصلية داخل `node:test`:
```javascript
import { describe, it, mock } from 'node:test';
import assert from 'node:assert/strict';

// الخدمة المراد اختبارها:
class OrderService {
  constructor(paymentGateway, notificationService) {
    this.paymentGateway = paymentGateway;
    this.notificationService = notificationService;
  }

  async checkout(orderId, amount, customerEmail) {
    const payment = await this.paymentGateway.charge(amount);
    if (!payment.success) {
      throw new Error('تم رفض عملية الدفع');
    }

    await this.notificationService.sendReceipt(customerEmail, orderId);
    return { status: 'COMPLETED', orderId };
  }
}

describe('OrderService.checkout()', () => {
  it('يجب أن يعالج الدفع ويرسل الإيصال للمشتري بنجاح', async (t) => {
    // 1. إنشاء دوال محاكاة وجواسيس أصلية باستخدام سياق الاختبار (t.mock):
    const mockPaymentGateway = {
      charge: t.mock.fn(async (amount) => ({ success: true, txnId: 'tx_123' }))
    };

    const mockNotificationService = {
      sendReceipt: t.mock.fn(async (email, orderId) => true)
    };

    const service = new OrderService(mockPaymentGateway, mockNotificationService);

    // 2. تنفيذ العملية:
    const result = await service.checkout('ORD-991', 150.00, 'customer@example.com');

    // 3. التحقق من النتيجة واستدعاءات الجواسيس:
    assert.strictEqual(result.status, 'COMPLETED');
    
    // التحقق من استدعاء بوابة الدفع مرة واحدة وبالمبلغ 150:
    assert.strictEqual(mockPaymentGateway.charge.mock.callCount(), 1);
    assert.deepStrictEqual(mockPaymentGateway.charge.mock.calls[0].arguments, [150.00]);

    // التحقق من أن خدمة الإشعارات استلمت بريد العميل ورقم الطلب الصحيح:
    assert.strictEqual(mockNotificationService.sendReceipt.mock.callCount(), 1);
    assert.deepStrictEqual(mockNotificationService.sendReceipt.mock.calls[0].arguments, [
      'customer@example.com',
      'ORD-991'
    ]);
  });
});
```

---

## ⚡ 5. اختبار الأكواد غير المتزامنة ورفض الوعود (Async Assertions)

يتطلب اختبار الوعود التحقق من اكتمال النجاح ومن مسارات الرمي والفشل عند حدوث خطأ:

```javascript
import { describe, it } from 'node:test';
import assert from 'node:assert/strict';

async function fetchUserData(userId) {
  if (!userId) {
    throw new Error('معرف المستخدم مطلوب');
  }
  return { id: userId, role: 'USER' };
}

describe('اختبار الوعود غير المتزامنة', () => {
  it('يجب أن يُرجع الكائن المتوقع عند النجاح', async () => {
    const data = await fetchUserData('usr_42');
    assert.deepStrictEqual(data, { id: 'usr_42', role: 'USER' });
  });

  it('يجب أن يرفض الوعد بالخطأ الصحيح عند غياب المعرف باستخدام assert.rejects()', async () => {
    await assert.rejects(
      async () => {
        await fetchUserData(null);
      },
      {
        name: 'Error',
        message: 'معرف المستخدم مطلوب'
      }
    );
  });
});
```

---

## 🧼 6. كود نظيف: مقارنة الأنماط الجيدة والسيئة (Good vs Bad)

```javascript
// ❌ سيء: اختبار هش يعتمد على التفاصيل الداخلية غير المرئية للمستخدم
it('يعرض تفاصيل المستخدم', () => {
  const component = new UserCard();
  // لا تختبر المتغيرات الخاصة أو أسماء الكلاسات الداخلية التي تتغير باستمرار!
  assert.strictEqual(component._internalPrivateFlag, true);
  assert.strictEqual(component.state.renderCount, 1);
});

// ✅ جيد: اختبار السلوك الخارجي والواجهة العامة المتاحة للمستخدم (Public API)
it('يعرض تفاصيل المستخدم', () => {
  const component = new UserCard({ name: 'Alex' });
  assert.strictEqual(component.getDisplayName(), 'Alex');
});

// ❌ سيء: اختبار غير حتمي يعتمد على شبكة إنترنت حقيقية أو خوادم خارجية
it('يجلب أسعار العملات', async () => {
  const rates = await fetch('https://api.exchangerate.live/usd'); // اختبار فاشل وهش إن انقطع الاتصال!
  assert.ok(rates.ok);
});

// ✅ جيد: محاكاة معزولة ومضمونة 100% لاستدعاءات الشبكة
it('يجلب أسعار العملات', async (t) => {
  t.mock.method(globalThis, 'fetch', async () => ({
    ok: true,
    json: async () => ({ rates: { SAR: 3.75 } })
  }));

  const result = await getRates();
  assert.strictEqual(result.rates.SAR, 3.75);
});
```

---

## 📝 7. ملخص الدرس والخطوة القادمة

- التزم بـ **هرم الاختبارات**: ركز النسبة الكبرى على اختبارات الوحدة السريعة والمعزولة.
- اتبع معايير **FIRST** لضمان اختبارات حتمية يمكن الوثوق بها في مسار CI/CD.
- استفد من محرك **`node:test`** المدمج ومكتبة **`node:assert/strict`** للاختبار السريع دون تثبيت مكتبات خارجية.
- استخدم **`t.mock`** لمراقبة استدعاءات الدوال ومحاكاة الخدمات الخارجية.
- اختبر مسارات الأخطاء والرفض غير المتزامن باستخدام **`assert.rejects()`**.
- ركز على اختبار سلوك النظام الخارجي ونتائجه، وتجنب اختبار تفاصيل التنفيذ الداخلية الهشة.
- في الدرس القادم، سنتناول **التعابير النمطية ومعالجة النصوص المتقدمة: الفحص المحيطي (Lookarounds) والوقاية من هجمات ReDoS**.
