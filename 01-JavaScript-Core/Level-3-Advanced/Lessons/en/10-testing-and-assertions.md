# 10 - Testing & Assertions: Node.js Test Runner, Mocking & Determinism

> 🌐 **Language / اللغة:** [English 🇬🇧](./10-testing-and-assertions.md) | [العربية 🇸🇦](../ar/10-testing-and-assertions.md)

---

## 🎯 1. Overview & Learning Objectives

Automated testing is the ultimate safety net of modern software development. Without reliable automated tests, refactoring is risky, regressions are inevitable, and release velocity crawls to a halt. While third-party frameworks like Jest, Mocha, or Vitest are popular, modern JavaScript runtimes (Node.js 18+ and 20+) now include an **enterprise-grade, zero-dependency built-in test runner** (`node:test`) and strict assertion module (`node:assert/strict`).

By the end of this lesson, you will master:
1. The **Testing Pyramid** balance: Unit vs Integration vs End-to-End (E2E).
2. Authoring deterministic test suites using Node.js's native **`node:test`** and **`node:assert/strict`**.
3. Controlling external side effects with **Test Doubles**: Mocks, Spies, and Stubs using `t.mock`.
4. Testing asynchronous functions, timeouts, and error rejection paths.
5. Avoiding brittle test anti-patterns (testing behavior vs testing implementation details).

---

## 🔺 2. The Testing Pyramid & Determinism

```
             / \
            / E2E \          (Fewest: Slowest, Most expensive, High flake rate)
           /-------\
          /  Integ  \        (Balanced: Tests multi-component contracts & APIs)
         /-----------\
        /  Unit Tests \      (Most: Fast, In-memory, Isolated, 100% Deterministic)
       +---------------+
```

### The FIRST Principles of Professional Unit Tests:
- **Fast:** Run thousands of tests in seconds.
- **Isolated:** Tests must never depend on each other or external shared state.
- **Repeatable:** Running the test 1,000 times yields the exact same result every time.
- **Self-Validating:** Pass/fail is boolean and automated—no manual log inspection.
- **Timely:** Written alongside or before implementation (TDD).

---

## 🧪 3. Native Node.js Test Runner & Strict Assertions

No heavy npm installs (`npm i jest` with 500 sub-dependencies) are needed. Node.js ships with first-class test support:

### Writing Your First Test Suite:
```javascript
// math.test.js
import { describe, it, beforeEach } from 'node:test';
import assert from 'node:assert/strict'; // Always use /strict!

// Code under test:
function calculateTax(subtotal, rate = 0.1) {
  if (typeof subtotal !== 'number' || subtotal < 0) {
    throw new TypeError('Subtotal must be a non-negative number');
  }
  return Math.round(subtotal * rate * 100) / 100;
}

describe('calculateTax()', () => {
  it('should calculate the standard 10% tax correctly', () => {
    const result = calculateTax(100);
    assert.strictEqual(result, 10);
  });

  it('should handle custom tax rates with rounding', () => {
    const result = calculateTax(49.99, 0.0825);
    assert.strictEqual(result, 4.12);
  });

  it('should throw TypeError when given invalid subtotal', () => {
    assert.throws(
      () => calculateTax(-50),
      {
        name: 'TypeError',
        message: 'Subtotal must be a non-negative number'
      }
    );
  });
});
```

### Running Tests from the Terminal:
```bash
node --test
# Or watch mode:
node --test --watch
```

---

## 🎭 4. Test Doubles: Spies & Mocks with `node:test`

A **Test Double** replaces a real dependency (like an email service or database) during testing:
- **Dummy:** Passed around but never used.
- **Stub:** Returns pre-canned answers to calls made during the test.
- **Spy:** Records calls, arguments, and return values for later inspection.
- **Mock:** Pre-programmed with expectations of what calls it should receive.

### Using Native Mocks in `node:test`:
```javascript
import { describe, it, mock } from 'node:test';
import assert from 'node:assert/strict';

// Service to test:
class OrderService {
  constructor(paymentGateway, notificationService) {
    this.paymentGateway = paymentGateway;
    this.notificationService = notificationService;
  }

  async checkout(orderId, amount, customerEmail) {
    const payment = await this.paymentGateway.charge(amount);
    if (!payment.success) {
      throw new Error('Payment declined');
    }

    await this.notificationService.sendReceipt(customerEmail, orderId);
    return { status: 'COMPLETED', orderId };
  }
}

describe('OrderService.checkout()', () => {
  it('should process payment and send receipt to customer', async (t) => {
    // 1. Create native spies/stubs using the test context (t.mock):
    const mockPaymentGateway = {
      charge: t.mock.fn(async (amount) => ({ success: true, txnId: 'tx_123' }))
    };

    const mockNotificationService = {
      sendReceipt: t.mock.fn(async (email, orderId) => true)
    };

    const service = new OrderService(mockPaymentGateway, mockNotificationService);

    // 2. Execute the action:
    const result = await service.checkout('ORD-991', 150.00, 'customer@example.com');

    // 3. Verify outcomes and spy calls:
    assert.strictEqual(result.status, 'COMPLETED');
    
    // Assert payment gateway was called once with 150:
    assert.strictEqual(mockPaymentGateway.charge.mock.callCount(), 1);
    assert.deepStrictEqual(mockPaymentGateway.charge.mock.calls[0].arguments, [150.00]);

    // Assert notification service received customer email:
    assert.strictEqual(mockNotificationService.sendReceipt.mock.callCount(), 1);
    assert.deepStrictEqual(mockNotificationService.sendReceipt.mock.calls[0].arguments, [
      'customer@example.com',
      'ORD-991'
    ]);
  });
});
```

---

## ⚡ 5. Testing Asynchronous Pipelines & Rejections

Testing promises requires asserting both successful resolution and unexpected rejections:

```javascript
import { describe, it } from 'node:test';
import assert from 'node:assert/strict';

async function fetchUserData(userId) {
  if (!userId) {
    throw new Error('User ID is required');
  }
  return { id: userId, role: 'USER' };
}

describe('Async Assertions', () => {
  it('resolves expected object', async () => {
    const data = await fetchUserData('usr_42');
    assert.deepStrictEqual(data, { id: 'usr_42', role: 'USER' });
  });

  it('rejects with specific error using assert.rejects()', async () => {
    await assert.rejects(
      async () => {
        await fetchUserData(null);
      },
      {
        name: 'Error',
        message: 'User ID is required'
      }
    );
  });
});
```

---

## 🧼 6. Clean Code: Good vs Bad Testing Patterns

```javascript
// ❌ BAD: Brittle test testing internal implementation details
it('renders user details', () => {
  const component = new UserCard();
  // Don't test private state or internal DOM classes!
  assert.strictEqual(component._internalPrivateFlag, true);
  assert.strictEqual(component.state.renderCount, 1);
});

// ✅ GOOD: Test observable behavior (Public API / User perspective)
it('renders user details', () => {
  const component = new UserCard({ name: 'Alex' });
  assert.strictEqual(component.getDisplayName(), 'Alex');
});

// ❌ BAD: Non-deterministic test relying on real timers or real network
it('fetches exchange rates', async () => {
  const rates = await fetch('https://api.exchangerate.live/usd'); // Flaky! Fails on Wi-Fi drop!
  assert.ok(rates.ok);
});

// ✅ GOOD: Deterministic, hermetic mock of external network
it('fetches exchange rates', async (t) => {
  t.mock.method(globalThis, 'fetch', async () => ({
    ok: true,
    json: async () => ({ rates: { EUR: 0.92 } })
  }));

  const result = await getRates();
  assert.strictEqual(result.rates.EUR, 0.92);
});
```

---

## 📝 7. Summary & Next Steps

- Structure tests around the **Testing Pyramid**: prioritize fast, isolated unit tests.
- Adopt the **FIRST** principles for deterministic, trustworthy test suites.
- Leverage the native **`node:test`** and **`node:assert/strict`** libraries for zero-dependency test runner performance.
- Use **`t.mock.fn()`** and **`t.mock.method()`** to spy on interactions and stub external services.
- Always test error paths using **`assert.rejects()`** and **`assert.throws()`**.
- Never test private implementation details; test observable public behavior.
- In the next lesson, we will explore **Regular Expressions & Text Processing: Modern Syntax, Lookarounds & ReDoS Prevention**.
