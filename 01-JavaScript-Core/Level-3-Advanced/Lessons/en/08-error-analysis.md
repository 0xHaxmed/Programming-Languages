# 08 - Error Analysis: Hierarchies, Stack Traces & Global Rejections

> 🌐 **Language / اللغة:** [English 🇬🇧](./08-error-analysis.md) | [العربية 🇸🇦](../ar/08-error-analysis.md)

---

## 🎯 1. Overview & Learning Objectives

In production software, errors are not anomalous accidents—they are normal runtime occurrences that must be categorized, serialized, traced, and handled systematically. Sloppy error management leads to silent failures, unhandled promise rejections, memory leaks, and sensitive data exposure.

By the end of this lesson, you will master:
1. The structural anatomy of the native JavaScript **`Error`** object and ES2022 **`cause`** chaining.
2. Constructing an enterprise-grade, domain-specific **Custom Error Hierarchy**.
3. Overcoming the **`JSON.stringify(error)`** serialization pitfall for centralized logging.
4. Setting up robust global error traps (`unhandledrejection` and `window.onerror`).
5. Defensive error boundaries that protect user privacy and system security from information disclosure leaks.

---

## 🔬 2. Anatomy of the JavaScript `Error` Object

The base `Error` object consists of standard properties:
- **`name`**: The category identifier of the error (e.g., `'TypeError'`, `'ReferenceError'`).
- **`message`**: Human-readable technical description.
- **`stack`**: Non-standard (but universally supported) string containing the function call frames leading to the exception.
- **`cause`** *(ES2022)*: An optional reference to an inner error that triggered this failure.

### Modern Error Chaining with `cause`:
When wrapping low-level errors into high-level business errors, preserve the underlying root cause using `{ cause: originalError }`:

```javascript
async function fetchUserBillingHistory(userId) {
  try {
    const response = await fetch(`/api/billing/${userId}`);
    if (!response.ok) {
      throw new Error(`Billing API responded with HTTP ${response.status}`);
    }
    return await response.json();
  } catch (networkError) {
    // Preserve the root network cause while throwing a domain error:
    throw new Error(`Failed to load billing history for user ${userId}`, {
      cause: networkError
    });
  }
}

// Inspecting chained causes:
try {
  await fetchUserBillingHistory('usr_8921');
} catch (error) {
  console.error('High-level error:', error.message);
  console.error('Root underlying cause:', error.cause?.message);
}
```

---

## 🏗️ 3. Designing a Domain-Specific Custom Error Hierarchy

In professional systems, errors should carry HTTP status codes, machine-readable error codes, and structured metadata:

```javascript
// Base Application Error:
class AppError extends Error {
  constructor(message, { statusCode = 500, errorCode = 'INTERNAL_ERROR', isOperational = true, cause } = {}) {
    super(message, { cause });
    this.name = this.constructor.name;
    this.statusCode = statusCode;
    this.errorCode = errorCode;
    this.isOperational = isOperational; // Differentiates programmer bugs from expected runtime errors

    // Clean up stack trace in V8 runtimes:
    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, this.constructor);
    }
  }
}

// Domain-Specific Subclasses:
class ValidationError extends AppError {
  constructor(message, validationIssues = [], cause) {
    super(message, { statusCode: 400, errorCode: 'VALIDATION_FAILED', isOperational: true, cause });
    this.validationIssues = validationIssues;
  }
}

class NotFoundError extends AppError {
  constructor(resourceName, identifier) {
    super(`${resourceName} with identifier [${identifier}] was not found`, {
      statusCode: 404,
      errorCode: 'RESOURCE_NOT_FOUND',
      isOperational: true
    });
  }
}

class UnauthorizedError extends AppError {
  constructor(message = 'Authentication token is expired or invalid') {
    super(message, { statusCode: 401, errorCode: 'UNAUTHORIZED', isOperational: true });
  }
}
```

### Clean Consumption with `instanceof`:
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

  // Programmer bugs or unhandled exceptions (Hide internal details!):
  logger.error('CRITICAL UNHANDLED ERROR:', error);
  return res.status(500).json({
    code: 'INTERNAL_SERVER_ERROR',
    message: 'An unexpected internal error occurred.'
  });
}
```

---

## ⚠️ 4. The `JSON.stringify` Error Pitfall & Serialization

A notorious JavaScript trap: passing an `Error` object to `JSON.stringify()` returns an empty object `{}`!

```javascript
const err = new Error('Database connection dropped');
console.log(JSON.stringify(err)); // Output: "{}" (Empty!)
```

**Why?** In JavaScript, the `name`, `message`, and `stack` properties of `Error` instances are configured as **non-enumerable** properties.

### Building a Bulletproof Error Serializer:
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

// Now easily logged or sent over HTTP/WebSocket:
console.log(JSON.stringify(serializeError(err), null, 2));
```

---

## 🌐 5. Global Error Boundaries & Unhandled Rejections

Even with defensive coding, unforeseen runtime exceptions or floating promise rejections can occur. Unhandled rejections must be trapped before they crash the application or leave it in a corrupted state.

### In Browser Environments:
```javascript
// 1. Trap unhandled asynchronous promise rejections:
window.addEventListener('unhandledrejection', (event) => {
  console.error('Unhandled Promise Rejection caught:', event.reason);
  // Send telemetry to monitoring service (e.g., Sentry / Datadog)
  telemetryService.report({
    type: 'unhandled_promise',
    error: serializeError(event.reason)
  });
  // Prevent browser default console spam if desired:
  // event.preventDefault();
});

// 2. Trap unhandled synchronous runtime errors:
window.onerror = function (message, source, lineno, colno, error) {
  telemetryService.report({
    type: 'runtime_exception',
    message,
    source,
    line: lineno,
    column: colno,
    stack: error?.stack
  });
  return false; // Let default browser error processing continue
};
```

---

## 🛡️ 6. Defensive Security: Preventing Information Disclosure

Stack traces and raw database errors frequently leak sensitive internal architectural details:
- File system paths (`/var/www/internal/secrets/db.js`)
- SQL queries and database schemas
- Cloud service credentials or internal microservice hostnames

```javascript
// ❌ DANGEROUS: Leaking internal stack trace to client API response
app.use((err, req, res, next) => {
  res.status(500).json({ error: err.message, stack: err.stack });
});

// ✅ SECURE: Strict segregation of client response vs internal logger
app.use((err, req, res, next) => {
  // 1. Log full details internally for developers:
  logger.error({
    traceId: req.id,
    stack: err.stack,
    sql: err.query
  });

  // 2. Return sanitized, safe message to the client:
  const isProduction = process.env.NODE_ENV === 'production';
  res.status(err.statusCode || 500).json({
    traceId: req.id, // Client can quote this trace ID to support
    message: isProduction ? 'An unexpected error occurred.' : err.message
  });
});
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Throwing raw strings or arbitrary numbers
throw 'User not found'; // No stack trace, breaks error instanceof checks!

// ✅ GOOD: Always throw Error instances or subclasses
throw new NotFoundError('User', userId);

// ❌ BAD: Silent error swallowing (Empty catch block)
try {
  parseComplexXml(payload);
} catch (e) {
  // Silent death: impossible to debug in production!
}

// ✅ GOOD: Log, augment, or rethrow
try {
  parseComplexXml(payload);
} catch (e) {
  logger.warn('Failed XML fallback parsing, falling back to JSON', { error: e.message });
  return parseFallbackJson(payload);
}
```

---

## 📝 8. Summary & Next Steps

- Always throw `Error` instances or subclasses—never throw strings or raw primitives.
- Use **ES2022 `{ cause: originalError }`** to preserve the full root-cause chain across abstractions.
- Design domain-specific classes (`ValidationError`, `NotFoundError`) that inherit from a central `AppError`.
- Handle the **`JSON.stringify`** non-enumerable trap when building logging pipelines.
- Implement global listeners for **`unhandledrejection`** and **`window.onerror`**.
- Never expose raw stack traces, database queries, or server filesystem paths in client responses.
- In the next lesson, we will explore **Code Review Architecture: Static Data Flow, Refactoring & Code Smells**.
