# 05 - Advanced Asynchronous JavaScript: Combinators, Timeouts & AbortController

> 🌐 **Language / اللغة:** [English 🇬🇧](./05-advanced-async.md) | [العربية 🇸🇦](../ar/05-advanced-async.md)

---

## 🎯 1. Overview & Learning Objectives

Asynchronous programming in production applications goes far beyond basic `async/await` and simple `fetch()` calls. Real-world systems require concurrent task coordination, timeout enforcement, graceful request cancellation, robust retry algorithms with backoff, and asynchronous stream iteration.

By the end of this lesson, you will master:
1. Deep operational comparison of all four **Promise Combinators**: `Promise.all`, `Promise.allSettled`, `Promise.race`, and `Promise.any`.
2. Cancelling pending asynchronous tasks and network requests using **`AbortController`** and **`AbortSignal`**.
3. Designing an enterprise-grade **Retry with Exponential Backoff and Jitter** algorithm.
4. Implementing request timeouts without memory leaks.
5. Processing data streams sequentially using **Async Generators** (`async function*`) and **`for await...of`**.

---

## 🔀 2. Promise Combinators Compared

JavaScript provides four static combinator methods on `Promise` to coordinate collections of concurrent operations:

| Method | Fulfillment Condition | Rejection Condition | Fail-Fast? | Typical Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **`Promise.all`** | **All** promises fulfill successfully. | **Any single** promise rejects. | **Yes** | Aggregating mandatory dependencies (e.g., fetch user profile AND settings). |
| **`Promise.allSettled`** | **All** promises settle (either fulfilled OR rejected). | **Never rejects** (always fulfills). | **No** | Bulk operations, dashboards, batch telemetry, partial data rendering. |
| **`Promise.race`** | **First** promise to settle (fulfill OR reject). | **First** promise to settle (fulfill OR reject). | **Yes** | Timeout enforcement, pinging fastest endpoint. |
| **`Promise.any`** | **First** promise to **fulfill** successfully. | **All** promises reject (returns `AggregateError`). | **No** (ignores rejections until all fail) | Mirror/replica fallback (fetch from redundant mirrors A, B, C). |

### Visual Flow Comparison:

```
Promise.all([P1, P2, P3])
P1: ----✓ (200ms)
P2: ---------✗ (400ms) ----> FAILS IMMEDIATELY at 400ms (P3 discarded from result)
P3: --------------✓ (700ms)

Promise.allSettled([P1, P2, P3])
P1: ----✓ (200ms)
P2: ---------✗ (400ms)
P3: --------------✓ (700ms) ----> FULFILLS at 700ms with array of statuses & results

Promise.any([P1, P2, P3])
P1: ----✗ (200ms - ignored)
P2: ---------✓ (400ms) ---------> FULFILLS IMMEDIATELY at 400ms with P2's value!
P3: --------------✓ (700ms)
```

### Combinators in Action:

```javascript
const fetchProfile = fetch('/api/profile').then(r => r.json());
const fetchOrders = fetch('/api/orders').then(r => r.json());
const fetchRecommendations = fetch('/api/recommended').then(r => r.json());

// 1. Promise.all: Critical multi-data load
async function loadDashboardCritical() {
  try {
    const [profile, orders] = await Promise.all([fetchProfile, fetchOrders]);
    console.log('User and orders loaded:', profile, orders);
  } catch (error) {
    console.error('Critical page data failed to load:', error);
  }
}

// 2. Promise.allSettled: Resilient UI widget loading
async function loadDashboardWidgets() {
  const results = await Promise.allSettled([
    fetchProfile,
    fetchOrders,
    fetchRecommendations
  ]);

  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Widget ${index} succeeded:`, result.value);
    } else {
      console.warn(`Widget ${index} failed:`, result.reason);
    }
  });
}

// 3. Promise.any: Fast CDN / Mirror failover
async function fetchConfigFromMirrors(mirrorUrls) {
  const requests = mirrorUrls.map(url => 
    fetch(url).then(res => {
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      return res.json();
    })
  );

  try {
    const config = await Promise.any(requests);
    return config;
  } catch (aggregateError) {
    console.error('All mirrors failed:', aggregateError.errors);
    throw new Error('Config service unavailable across all regions.');
  }
}
```

---

## 🛑 3. Task Cancellation with `AbortController`

Prior to the standard `AbortController` API, cancelling async operations was clunky and prone to memory leaks. Today, `AbortController` is supported across modern browsers, Node.js (v15+), and Deno.

### Anatomy of `AbortController`:
- `const controller = new AbortController();`
- `controller.signal`: An instance of `AbortSignal` passed to async tasks.
- `controller.abort(reason)`: Fires the cancellation event and marks `signal.aborted = true`.

### Cancelling Network Requests:
```javascript
async function searchProducts(query, signal) {
  const url = `/api/search?q=${encodeURIComponent(query)}`;
  const response = await fetch(url, { signal });
  
  if (!response.ok) {
    throw new Error(`Search failed with status ${response.status}`);
  }
  return response.json();
}

// Usage in an autocomplete search input:
let activeController = null;

async function onUserTyping(searchTerm) {
  // Cancel previous in-flight request if user keeps typing
  if (activeController) {
    activeController.abort('New keystroke entered by user');
  }

  activeController = new AbortController();

  try {
    const results = await searchProducts(searchTerm, activeController.signal);
    renderSearchResults(results);
  } catch (error) {
    if (error.name === 'AbortError') {
      console.log('Previous search was cleanly aborted.');
    } else {
      console.error('Actual search error:', error);
    }
  }
}
```

### Implementing Enforced Timeouts:
Modern JavaScript provides `AbortSignal.timeout(ms)` to automatically trigger an abort after a given duration:

```javascript
async function fetchWithTimeout(url, timeoutMs = 5000) {
  try {
    // AbortSignal.timeout automatically aborts after timeoutMs milliseconds
    const response = await fetch(url, {
      signal: AbortSignal.timeout(timeoutMs)
    });
    return await response.json();
  } catch (error) {
    if (error.name === 'TimeoutError') {
      throw new Error(`Request to ${url} timed out after ${timeoutMs}ms.`);
    }
    throw error;
  }
}
```

---

## 🔁 4. Enterprise Async Retry with Exponential Backoff & Jitter

When communicating over distributed networks, transient errors (HTTP 503, rate limits 429, network disconnects) will happen. Naive retry loops bombard already failing servers simultaneously ("Thundering Herd" problem). 

A production-grade retry implementation uses:
1. **Exponential Backoff:** `delay = baseDelay * 2^attempt`
2. **Full Jitter:** Adding a random factor (`Math.random() * delay`) to spread out retries among distributed clients.
3. **Idempotency & Safe Status Checks:** Only retrying transient failures, never fatal 400 Bad Request or 401 Unauthorized errors.

```javascript
const wait = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function retryWithBackoff(
  asyncFn,
  {
    maxRetries = 4,
    baseDelayMs = 300,
    maxDelayMs = 5000,
    shouldRetry = (err) => true
  } = {}
) {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await asyncFn();
    } catch (error) {
      const isLastAttempt = attempt === maxRetries;
      if (isLastAttempt || !shouldRetry(error)) {
        throw error;
      }

      // Calculate exponential backoff
      const exponentialDelay = baseDelayMs * Math.pow(2, attempt);
      const cappedDelay = Math.min(exponentialDelay, maxDelayMs);

      // Add full jitter: random value between 0 and cappedDelay
      const jitteredDelay = Math.random() * cappedDelay;

      console.warn(
        `Attempt ${attempt + 1} failed (${error.message}). Retrying in ${Math.round(jitteredDelay)}ms...`
      );

      await wait(jitteredDelay);
    }
  }
}

// Example usage:
async function loadUserData(userId) {
  return retryWithBackoff(
    async () => {
      const res = await fetch(`/api/users/${userId}`);
      if (res.status === 429 || res.status >= 500) {
        throw new Error(`Server returned HTTP ${res.status}`);
      }
      if (!res.ok) {
        const fatalError = new Error(`Client error HTTP ${res.status}`);
        fatalError.isFatal = true;
        throw fatalError;
      }
      return res.json();
    },
    {
      maxRetries: 3,
      baseDelayMs: 250,
      shouldRetry: (err) => !err.isFatal // Do not retry 4xx fatal errors
    }
  );
}
```

---

## 🌊 5. Async Generators & `for await...of`

When dealing with paginated APIs, real-time event logs, or chunked file downloads, loading everything into memory simultaneously causes memory bloat.

**Async Generators** yield promises over time and allow consumers to iterate using `for await...of`:

```javascript
// Generator that lazily streams paginated API pages one by one:
async function* fetchPaginatedAuditLogs(pageSize = 50) {
  let cursor = null;
  let hasMore = true;

  while (hasMore) {
    const url = cursor 
      ? `/api/logs?limit=${pageSize}&cursor=${cursor}`
      : `/api/logs?limit=${pageSize}`;

    const res = await fetch(url);
    const data = await res.json();

    yield data.items; // Yield array of current batch to caller

    cursor = data.nextCursor;
    hasMore = Boolean(cursor);
  }
}

// Consuming the stream:
async function processAllLogs() {
  let totalProcessed = 0;

  for await (const batch of fetchPaginatedAuditLogs(100)) {
    console.log(`Processing batch of ${batch.length} logs...`);
    for (const log of batch) {
      if (log.severity === 'CRITICAL') {
        alertAdmin(log);
      }
    }
    totalProcessed += batch.length;
    // Memory for previous batches can be garbage collected immediately!
  }

  console.log(`Finished processing all ${totalProcessed} logs.`);
}
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Sequential await inside Array.prototype.forEach (does not wait!)
items.forEach(async (item) => {
  await uploadItem(item); // Errors are unhandled and execution is uncontrolled!
});

// ✅ GOOD: Controlled concurrency with Promise.all
await Promise.all(items.map((item) => uploadItem(item)));

// ❌ BAD: Unhandled promise rejection leaking into global state
async function syncData() {
  fetch('/api/sync'); // Floating promise, failures go unnoticed
}

// ✅ GOOD: Explicit error handling and cancellation support
async function syncData(signal) {
  try {
    const res = await fetch('/api/sync', { signal });
    if (!res.ok) throw new Error(`Sync HTTP ${res.status}`);
    return await res.json();
  } catch (error) {
    if (error.name === 'AbortError') return null;
    logger.error('Sync failed', error);
    throw error;
  }
}
```

---

## 📝 7. Summary & Next Steps

- Use **`Promise.all`** when all operations are required (fail-fast).
- Use **`Promise.allSettled`** when you need every result regardless of failure (bulk dashboards).
- Use **`Promise.any`** when finding the first successful response (redundant mirrors).
- Use **`AbortController`** and `AbortSignal.timeout()` to cleanly terminate operations and avoid orphaned network requests.
- Always apply **Exponential Backoff with Jitter** on transient retries to avoid server swamping.
- In the next lesson, we will dissect the **V8 Event Loop & Concurrency: Microtasks, Macrotasks & Starvation**.
