# 09 - URLs, Query Strings & Client-Side Navigation

> 🌐 **Language / اللغة:** [English 🇬🇧](./09-urls-and-navigation.md) | [العربية 🇸🇦](../ar/09-urls-and-navigation.md)

---

## 🎯 1. Overview & Learning Objectives

In modern web applications, the URL is the single source of truth for application state: search queries, active filters, pagination, and navigation destinations are serialized directly into the address bar.

Historically, developers manipulated URLs using fragile string splitting and regular expressions. Modern JavaScript provides dedicated, robust APIs: the **`URL`** object and the **`URLSearchParams`** interface.

By the end of this lesson, you will master:
1. Deconstructing the anatomical components of a web URL.
2. Parsing, modifying, and serializing query parameters with **`URLSearchParams`**.
3. Resolving relative paths cleanly using the **`URL` constructor**.
4. Updating browser address bar history without page reloads using **`history.pushState()`** and **`popstate`**.
5. Client-side security: validating return URLs and preventing `javascript:` protocol injection.

---

## 🧭 2. Anatomy of a Web URL

```
  https://example.com:8080/catalog/shoes?brand=nike&sort=asc#reviews
  \___/   \_________/ \__/ \___________/ \_________________/ \______/
 protocol     host    port    pathname          search         hash
 \_______________________/
          origin
```

### The Native `URL` Object:
```javascript
const url = new URL('https://example.com:8080/catalog/shoes?brand=nike&sort=asc#reviews');

console.log(url.origin);   // 'https://example.com:8080'
console.log(url.hostname); // 'example.com'
console.log(url.pathname); // '/catalog/shoes'
console.log(url.search);   // '?brand=nike&sort=asc'
console.log(url.hash);     // '#reviews'

// Modifying parts updates the entire URL automatically:
url.pathname = '/catalog/jackets';
url.port = '9000';
console.log(url.href); // 'https://example.com:9000/catalog/jackets?brand=nike&sort=asc#reviews'
```

### Resolving Relative URLs:
The `URL` constructor accepts a base URL as its second argument:
```javascript
const base = 'https://api.mycompany.com/v1/';
const endpoint = new URL('users/profile', base);
console.log(endpoint.href); // 'https://api.mycompany.com/v1/users/profile'
```

---

## 🔍 3. Query Parameter Manipulation: `URLSearchParams`

`URLSearchParams` eliminates manual string operations (`split('?')`, `split('&')`, `split('=')`) and automatically manages URL encoding and decoding.

```javascript
// Initializing with current window query string:
const searchParams = new URLSearchParams(window.location.search);

// 1. Reading parameters:
const page = searchParams.get('page'); // Returns string value or null if not found
const hasFilter = searchParams.has('category'); // Returns true or false

// 2. Reading multiple values for the same key (?tag=js&tag=react):
const tags = searchParams.getAll('tag'); // ['js', 'react']

// 3. Setting and Appending:
searchParams.set('page', '2'); // Sets 'page' to 2 (overwrites existing)
searchParams.append('tag', 'typescript'); // Appends an additional 'tag'

// 4. Deleting parameters:
searchParams.delete('outdatedParam');

// 5. Serializing back to a clean string:
console.log(searchParams.toString());
// e.g. "brand=nike&page=2&tag=js&tag=react&tag=typescript"
```

> [!TIP]
> **Automatic Percent-Encoding:**  
> When you use `searchParams.set('query', 'shoes & boots')`, it automatically converts special characters to safe URL entities (`shoes+%26+boots`), preventing corrupted URLs.

---

## 🚀 4. Client-Side Navigation: `pushState` vs `replaceState`

Single-Page Applications (SPAs) update the URL and browser history dynamically without triggering a server request:

```javascript
// 1. pushState(): Changes URL and ADDS a new entry to the browser's Back/Forward history:
function filterProducts(category) {
  const currentUrl = new URL(window.location);
  currentUrl.searchParams.set('category', category);

  // history.pushState(stateObject, title, newUrl)
  history.pushState({ category }, '', currentUrl);
  
  // Re-render UI:
  updateCatalogView(category);
}

// 2. replaceState(): Changes URL but REPLACES the current history entry (user cannot click Back):
function updatePagination(pageNum) {
  const currentUrl = new URL(window.location);
  currentUrl.searchParams.set('page', pageNum);

  history.replaceState({ page: pageNum }, '', currentUrl);
}

// 3. Listening to the browser Back and Forward buttons:
window.addEventListener('popstate', (event) => {
  console.log('User navigated back/forward! Saved state:', event.state);
  // Restore view from URL:
  const params = new URLSearchParams(window.location.search);
  updateCatalogView(params.get('category'));
});
```

---

## 🛡️ 5. URL Security: Preventing Open Redirects & Script Execution

When an application reads a destination URL from a query parameter (e.g. `?returnUrl=/dashboard`):

### The Vulnerability:
```javascript
// ❌ DANGEROUS: Directly assigning an unvalidated parameter to location.href
const nextUrl = new URLSearchParams(window.location.search).get('returnUrl');
// If attacker passes ?returnUrl=javascript:alert(document.origin)
// or ?returnUrl=https://attacker-phishing.com
location.href = nextUrl; // Triggers script execution or Open Redirection!
```

### The Defensive Fix:
```javascript
// ✅ SECURE: Validate that the URL is strictly a relative internal path
function getSafeReturnUrl() {
  const target = new URLSearchParams(window.location.search).get('returnUrl');
  if (!target) return '/dashboard';

  // Ensure it starts with a single '/' and not '//' (which browser treats as protocol-relative external domain):
  if (target.startsWith('/') && !target.startsWith('//') && !target.startsWith('/\\')) {
    return target; // Safe internal path
  }

  return '/dashboard'; // Fallback
}

location.href = getSafeReturnUrl();
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Naive, error-prone manual query string manipulation
const query = window.location.search;
const brand = query.split('brand=')[1]?.split('&')[0]; // Breaks easily on special characters!

// ✅ GOOD: Using URLSearchParams API
const params = new URLSearchParams(window.location.search);
const brand = params.get('brand');

// ❌ BAD: Manual string concatenation for complex URLs
const apiUrl = 'https://api.com/search?q=' + searchTerm + '&page=' + page; // Breaks on spaces and &

// ✅ GOOD: Using URL object with searchParams
const url = new URL('https://api.com/search');
url.searchParams.set('q', searchTerm);
url.searchParams.set('page', page);
const finalUrl = url.href; // Perfectly escaped and encoded
```

---

## 📝 7. Summary & Next Steps
- Use the **`URL`** object to inspect and mutate protocol, host, pathname, and origin.
- Use **`URLSearchParams`** for robust reading, setting, and encoding of query parameters.
- Use **`history.pushState()`** to build clean, bookmarkable SPA URLs without page reloads.
- Handle browser Back/Forward buttons using the **`popstate`** event listener.
- In the next lesson, we will explore **Data Serialization & Serialization Mechanics: JSON.stringify and JSON.parse**.
