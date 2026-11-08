# 11 - Network Requests & The Fetch API

> 🌐 **Language / اللغة:** [English 🇬🇧](./11-network-requests.md) | [العربية 🇸🇦](../ar/11-network-requests.md)

---

## 🎯 1. Overview & Learning Objectives

Modern web applications do not reload the entire page to update content. Instead, they dynamically exchange data with backend REST or GraphQL APIs in the background.

While legacy applications relied on the verbose `XMLHttpRequest` object, modern JavaScript uses the Promise-based **`Fetch API`**.

By the end of this lesson, you will master:
1. The client-server request/response lifecycle.
2. Executing HTTP requests (`GET`, `POST`, `PUT`, `DELETE`) with `fetch()`.
3. Reading and setting HTTP headers (`Content-Type`, `Authorization`).
4. Decoding payload streams (`response.json()`, `response.text()`, `response.blob()`).
5. **The #1 Fetch Gotcha:** Why `fetch()` does NOT reject on HTTP 404 or 500 errors, and how to use `response.ok`.
6. Canceling inflight requests using **`AbortController`**.

---

## 🌐 2. The Client-Server HTTP Lifecycle

When a web browser requests data from a server, it transmits an HTTP Request and waits for an HTTP Response:

```
[ BROWSER (Client) ]                                  [ SERVER (Backend API) ]
         |                                                       |
         | --- 1. HTTP Request (Method, URL, Headers, Body) ---> |
         |                                                       | (Processes database query)
         | <--- 2. HTTP Response (Status Code, Headers, Body) -- |
         v                                                       v
```

### Essential HTTP Verbs:
- **`GET`:** Retrieve resources (cannot send a request `body`).
- **`POST`:** Submit new data (e.g. creating an account, placing an order).
- **`PUT` / `PATCH`:** Replace or partially update an existing resource.
- **`DELETE`:** Remove a resource.

---

## 🚀 3. Fetching Data with `fetch()`

The `fetch()` function takes a URL and optional configuration options, returning a **Promise** that resolves to a `Response` object.

### The Two-Step Asynchronous Process:
1. **Step 1:** The server returns HTTP headers and status code (`response`).
2. **Step 2:** The response body stream must be read and parsed asynchronously (`response.json()`).

```javascript
// A Standard GET Request:
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => {
    // ⚠️ Check if the HTTP status code is in the 200-299 range:
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    return response.json(); // Parses incoming JSON stream
  })
  .then(data => {
    console.log('Post Title:', data.title);
  })
  .catch(error => {
    console.error('Network or parsing failure:', error.message);
  });
```

---

## ⚠️ 4. The Giant Fetch Gotcha: `response.ok`

> [!CAUTION]
> **A Critical Distinction:**  
> The Promise returned by `fetch()` **WILL NOT REJECT on HTTP 404 or 500 errors!**  
> `fetch()` only rejects if there is a **physical network failure** (e.g. the user is offline, DNS resolution fails, or CORS blocks the request).
> 
> You MUST inspect **`response.ok`** manually in every request!

```javascript
// Inspecting Response Metadata:
fetch('/api/user/999')
  .then(response => {
    console.log(response.status);     // e.g. 404
    console.log(response.statusText); // 'Not Found'
    console.log(response.ok);         // false (because status is not 200-299)

    if (!response.ok) {
      throw new Error(`Server returned status ${response.status}`);
    }
    return response.json();
  });
```

---

## 📤 5. Sending Data: `POST`, `PUT`, `DELETE`

To send data, pass an options object as the second argument:

```javascript
async function createNewProduct(productData) {
  try {
    const response = await fetch('https://api.example.com/products', {
      method: 'POST',
      headers: {
        // Crucial: Informs the server that the payload is JSON:
        'Content-Type': 'application/json',
        // Optional Auth token:
        'Authorization': 'Bearer YOUR_ACCESS_TOKEN'
      },
      // Serialize the JavaScript object to JSON text:
      body: JSON.stringify(productData)
    });

    if (!response.ok) {
      throw new Error(`Failed to create product: ${response.status}`);
    }

    const createdRecord = await response.json();
    console.log('Created record ID:', createdRecord.id);
    return createdRecord;
  } catch (error) {
    console.error('Request failed:', error);
  }
}

// Invocation:
createNewProduct({ name: 'Mechanical Keyboard', price: 120 });
```

---

## ⏱️ 6. Canceling Requests: `AbortController`

If a user navigates away from a page or types rapidly into an autocomplete search box, ongoing network requests should be aborted to save bandwidth and prevent race conditions:

```javascript
const controller = new AbortController();
const { signal } = controller;

// Pass the signal to fetch:
fetch('https://api.example.com/large-dataset', { signal })
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => {
    if (err.name === 'AbortError') {
      console.log('Request was canceled by user action.');
    } else {
      console.error('Other network error:', err);
    }
  });

// Cancel the request if it exceeds 3 seconds:
setTimeout(() => {
  controller.abort(); // Triggers AbortError on the fetch promise!
}, 3000);
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Bare fetch without checking response.ok
fetch('/api/profile')
  .then(res => res.json()) // If server returns 500 HTML error page, this throws SyntaxError!
  .then(data => render(data));

// ✅ GOOD: Robust API service wrapper
async function fetchJson(endpoint) {
  const response = await fetch(endpoint);
  if (!response.ok) {
    throw new Error(`Request to ${endpoint} failed with status ${response.status}`);
  }
  return response.json();
}

// ❌ BAD: Forgetting Content-Type header on POST requests
fetch('/api/users', {
  method: 'POST',
  body: JSON.stringify({ name: 'Alex' }) // Server fails to parse payload without header!
});

// ✅ GOOD: Explicit Content-Type header
fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Alex' })
});
```

---

## 📝 8. Summary & Next Steps
- `fetch()` is modern and built on **Promises**.
- Reading the response requires **two steps**: headers (`fetch`) followed by body stream (`response.json()`).
- Always verify **`response.ok`**; HTTP 4xx and 5xx errors do NOT reject automatically.
- Always include `'Content-Type': 'application/json'` when transmitting POST/PUT bodies.
- Use **`AbortController`** to handle request cancellations and timeouts.
- In the next lesson, we will master the asynchronous core of JavaScript: **Promises, Chaining, and async/await**.
