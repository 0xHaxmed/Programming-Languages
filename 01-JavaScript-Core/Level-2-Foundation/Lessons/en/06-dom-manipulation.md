# 06 - The Document Object Model (DOM Manipulation)

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-dom-manipulation.md) | [العربية 🇸🇦](../ar/06-dom-manipulation.md)

---

## 🎯 1. Overview & Learning Objectives

The **Document Object Model (DOM)** is an object-oriented structural representation of an HTML document. The browser converts raw HTML text into a tree of programmable nodes that JavaScript can query, mutate, add, and remove dynamically.

Mastering clean DOM manipulation is essential for creating dynamic user interfaces without introducing UI performance bottlenecks or security vulnerabilities.

By the end of this lesson, you will master:
1. The hierarchical structure of the **DOM Tree** (Document, Elements, Text nodes).
2. Querying elements efficiently using `querySelector()` and `querySelectorAll()`.
3. The vital differences between `textContent`, `innerText`, and `innerHTML`.
4. Managing attributes, data-attributes (`dataset`), and CSS classes via `classList`.
5. Creating and inserting elements efficiently using `DocumentFragment` to prevent layout thrashing.
6. Clean Code and defensive DOM manipulation best practices.

---

## 🌲 2. The DOM Tree Structure

When an HTML page loads, the browser parser converts markup into a tree of JavaScript objects:

```
                      +-------------------+
                      |     Document      |
                      +---------+---------+
                                |
                      +---------v---------+
                      |  <html> (Element) |
                      +---------+---------+
            +-------------------+-------------------+
            |                                       |
    +-------v-------+                       +-------v-------+
    | <head> (Elem) |                       | <body> (Elem) |
    +---------------+                       +-------+-------+
                                        +-----------+-----------+
                                        |                       |
                                +-------v-------+       +-------v-------+
                                |  <h1> (Elem)  |       |  <p> (Elem)   |
                                +-------+-------+       +---------------+
                                        |
                                +-------v-------+
                                | "Title Text"  | (Text Node)
                                +---------------+
```

---

## 🔍 3. Querying Elements

Modern web development relies almost exclusively on CSS Selector syntax:

### Modern Standard Methods:
```javascript
// 1. Select the FIRST element matching a CSS selector (or null):
const primaryButton = document.querySelector('.btn-primary');
const searchInput = document.querySelector('#header input[type="text"]');

// 2. Select ALL matching elements (Returns a static NodeList):
const menuItems = document.querySelectorAll('nav .nav-link');

// NodeList supports forEach directly:
menuItems.forEach((item, index) => {
  console.log(`Item ${index}: ${item.textContent}`);
});

// Convert NodeList to true Array if you need map/filter:
const itemsArray = Array.from(menuItems);
```

### Static NodeList vs Live HTMLCollection:
- `querySelectorAll()` returns a **static snapshot** of the matching elements at the time of query.
- Legacy methods (`getElementsByClassName()`, `getElementsByTagName()`) return **Live Collections** that automatically mutate when the DOM changes, causing unexpected bugs in loops!

---

## ✏️ 4. Manipulating Text & HTML Content

```javascript
const banner = document.querySelector('#welcome-banner');

// 1. textContent (PREFERRED): Returns raw text, ignores styling, fast, SAFE:
banner.textContent = 'Welcome back, User!';

// 2. innerText: Aware of rendered styling (excludes hidden elements with display:none), triggers reflow:
console.log(banner.innerText);

// 3. innerHTML: Parses and renders HTML tags:
banner.innerHTML = '<strong>Special Offer:</strong> 20% off today!';
```

> [!CAUTION]
> **Security Imperative:**  
> **Never** assign untrusted user input directly to `innerHTML`. Doing so creates a direct **Cross-Site Scripting (XSS)** vulnerability. Always use `textContent` when rendering dynamic text supplied by users or URLs!

---

## 🎨 5. Managing Classes, Styles & Attributes

### A. Class Management via `classList` (Recommended)
Avoid overwriting the raw `className` string. Use `classList` methods:
```javascript
const card = document.querySelector('.card');

card.classList.add('active', 'highlighted'); // Adds classes
card.classList.remove('dimmed');             // Removes class
card.classList.toggle('collapsed');          // Adds if absent, removes if present
const isVisible = card.classList.contains('active'); // Returns boolean
card.classList.replace('theme-light', 'theme-dark');
```

### B. Inline Styles
```javascript
// Direct inline style (Use camelCase for CSS properties):
card.style.backgroundColor = '#1e293b';
card.style.display = 'flex';

// Reading computed styles applied by CSS stylesheets:
const computed = window.getComputedStyle(card);
console.log(computed.marginTop); // e.g. '16px'
```

### C. Attributes & Data Attributes (`dataset`)
```javascript
const userAvatar = document.querySelector('#avatar');

// Standard attributes:
userAvatar.setAttribute('alt', 'User Profile Picture');
console.log(userAvatar.getAttribute('src'));
userAvatar.removeAttribute('aria-hidden');

// Custom Data Attributes (HTML: <div id="item" data-product-id="44" data-in-stock="true">):
const item = document.querySelector('#item');
console.log(item.dataset.productId); // "44"
item.dataset.inStock = 'false';       // Updates HTML data-in-stock attribute
```

---

## 🏗️ 6. Creating, Inserting & Removing Elements

```javascript
// 1. Create a new DOM element in memory:
const newBadge = document.createElement('span');
newBadge.className = 'badge badge-success';
newBadge.textContent = 'New!';

// 2. Insert into the DOM:
const cardHeader = document.querySelector('.card-header');
cardHeader.appendChild(newBadge); // Adds as last child
cardHeader.prepend(newBadge);     // Adds as first child

// 3. Modern positioning:
// targetElement.before(newBadge); // Insert directly before
// targetElement.after(newBadge);  // Insert directly after

// 4. Removing elements:
newBadge.remove(); // Removes itself cleanly from the DOM tree
```

### High-Performance Batch Insertion: `DocumentFragment`
Inserting 100 elements one by one triggers 100 browser recalculations (Reflows & Repaints). Use a **`DocumentFragment`** to update the DOM in a single atomic operation:

```javascript
const listContainer = document.querySelector('#notifications-list');
const fragment = document.createDocumentFragment();

const notifications = ['Email sent', 'Backup finished', 'Invoice paid'];

notifications.forEach((text) => {
  const li = document.createElement('li');
  li.textContent = text;
  fragment.appendChild(li); // Appends in memory only (No reflow!)
});

// A single DOM insertion for all 3 items:
listContainer.appendChild(fragment);
```

---

## 🧼 7. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Manipulating classes via raw string concatenation
element.className += ' active highlighted'; // Prone to spacing errors and duplicate classes

// ✅ GOOD: Using classList API
element.classList.add('active', 'highlighted');

// ❌ BAD: Appending multiple elements inside a loop (Causes Layout Thrashing)
data.forEach(item => {
  const div = document.createElement('div');
  div.textContent = item.name;
  container.appendChild(div); // Triggers browser re-render on every iteration!
});

// ✅ GOOD: Using DocumentFragment or replaceChildren
const fragment = document.createDocumentFragment();
data.forEach(item => {
  const div = document.createElement('div');
  div.textContent = item.name;
  fragment.appendChild(div);
});
container.appendChild(fragment); // Single render pass!
```

---

## 📝 8. Summary & Next Steps
- Use **`querySelector`** and **`querySelectorAll`** with CSS selectors for clean element retrieval.
- Always use **`textContent`** instead of `innerHTML` when displaying untrusted data.
- Manage CSS classes with **`classList`** (`add`, `remove`, `toggle`).
- Use **`DocumentFragment`** when creating multiple elements in a loop for optimal rendering performance.
- In the next lesson, we will explore **The Events System: Listeners, Bubbling, Capturing, and Delegation**.
