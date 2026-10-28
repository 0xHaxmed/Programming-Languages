# Level 2 Capstone Project: API Explorer

> 🌐 **Language / اللغة:** [English 🇬🇧](./README.md) | [العربية 🇸🇦](../ar/README.md)

---

## 🎯 1. Architectural Vision

The **API Explorer** project is an interactive client-side developer console designed to dispatch network requests, inspect response telemetry, format JSON payloads, and gracefully intercept transport errors—analogous to a lightweight in-browser Postman client.

This project reinforces all Level 2 learning milestones:
- Asynchronous networking with `fetch()`, HTTP verbs, and headers.
- Safe DOM node operations avoiding dangerous HTML injection sinks.
- Parsing and serialization of JSON payloads with error interceptors.
- Performance profiling and status code decoding.
- Offline support via local Mock datasets.

---

## 🏗️ 2. Core Functional Specifications

1. **HTTP Method Selector:** Dispatches `GET`, `POST`, and `DELETE` requests.
2. **Endpoint URL Input:** Pre-populated with public REST mock APIs (e.g., JSONPlaceholder).
3. **Payload & Header Configuration:** Custom header injections and JSON body serialization.
4. **Diagnostic Telemetry Console:** Formatted syntax highlighting for JSON, HTTP response status badges, and request execution latency ($ms$).
5. **Mock Fallback Engine:** Simulates local responses when operating offline.

---

## 📁 3. Directory Layout

```plaintext
API-Explorer/
├── index.html        # Semantic HTML layout and controls
├── style.css         # Modern flexbox/grid layout and dark theme
└── app.js            # Core fetch dispatcher, event handling, and DOM rendering
```

---

## 💻 4. Core Implementation (`app.js`)

```javascript
/**
 * API Explorer Dispatch Engine
 */
async function sendRequest(url, method = "GET", payload = null) {
    const startTime = performance.now();
    const resultBox = document.getElementById("results");

    try {
        resultBox.textContent = "Dispatching network request...";

        const options = {
            method: method,
            headers: {
                "Content-Type": "application/json"
            }
        };

        if (payload && (method === "POST" || method === "PUT")) {
            options.body = JSON.stringify(payload);
        }

        const response = await fetch(url, options);
        const duration = Math.round(performance.now() - startTime);
        const data = await response.json();

        renderResponse({
            status: response.status,
            statusText: response.statusText,
            timeMs: duration,
            data: data
        });

    } catch (error) {
        renderError(`Transport Layer Failure: ${error.message}`);
    }
}

function renderResponse({ status, statusText, timeMs, data }) {
    console.log(`[HTTP ${status} ${statusText}] latency: ${timeMs}ms`);
    console.table(data);
}

function renderError(message) {
    console.error("[Network Error]:", message);
}
```

---

⬅️ [Level 2 Lessons](../../Lessons/en/README.md) | ➡️ [Advance to Level 3 — Advanced](../../../Level-3-Advanced/README.md)
