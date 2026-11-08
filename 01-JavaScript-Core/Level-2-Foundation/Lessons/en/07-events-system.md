# 07 - The Events System: Listeners, Bubbling & Delegation

> 🌐 **Language / اللغة:** [English 🇬🇧](./07-events-system.md) | [العربية 🇸🇦](../ar/07-events-system.md)

---

## 🎯 1. Overview & Learning Objectives

Web applications are fundamentally **event-driven**. An event is a signal from the browser that an action occurred—such as a user clicking a button, typing into an input field, moving the mouse, or submitting a form.

To build performant, memory-efficient web interfaces, you must master how events travel through the DOM hierarchy and understand the industry-standard **Event Delegation Pattern**.

By the end of this lesson, you will master:
1. Attaching and detaching event listeners using `addEventListener()` and `removeEventListener()`.
2. Inspecting the `Event` object (`e.target` vs `e.currentTarget`).
3. Preventing browser defaults with `e.preventDefault()`.
4. The 3 lifecycle phases of event propagation: **Capturing**, **Target**, and **Bubbling**.
5. Halting event propagation with `e.stopPropagation()`.
6. Implementing the high-performance **Event Delegation Pattern** with `element.closest()`.

---

## 👂 2. Attaching & Removing Event Listeners

Never use inline HTML attributes (`onclick="..."`). Always bind events programmatically in JavaScript:

```javascript
const saveButton = document.querySelector('#btn-save');

// Named handler function:
function handleSaveClick(event) {
  console.log('Save button clicked!');
}

// 1. Attaching listener:
saveButton.addEventListener('click', handleSaveClick);

// 2. Removing listener (Requires the identical function reference!):
saveButton.removeEventListener('click', handleSaveClick);
```

> [!WARNING]
> If you attach an anonymous function (`saveButton.addEventListener('click', () => {...})`), you **cannot** remove it later because its memory reference is lost!

### Advanced Listener Options:
`addEventListener(type, handler, options)` accepts a third configuration argument:
```javascript
// A. 'once: true' — Automatically removes itself after firing the first time:
saveButton.addEventListener('click', handleSaveClick, { once: true });

// B. 'passive: true' — Signals that preventDefault() will NEVER be called (optimizes mobile touch/scroll performance):
window.addEventListener('scroll', onScrollHandler, { passive: true });
```

---

## 🎯 3. The Event Object (`e.target` vs `e.currentTarget`)

When an event triggers, the browser automatically passes an `Event` object to your callback:

```javascript
const card = document.querySelector('.card');

card.addEventListener('click', (event) => {
  // e.target: The EXACT element physically clicked by the mouse (e.g. an <i> icon inside a button)
  console.log('Physical target clicked:', event.target);

  // e.currentTarget: The element to which this event listener was ATTACHED (the .card container)
  console.log('Listener container element:', event.currentTarget);
});
```

### Essential Event Methods:
- **`e.preventDefault()`:** Cancels the browser's default behavior (e.g., stops a link `<a>` from navigating or a `<form>` from reloading the page).
- **`e.stopPropagation()`:** Stops the event from traveling further up or down the DOM tree.

---

## 🌊 4. The 3 Phases of Event Propagation

When a user clicks an element deep inside the DOM, the event doesn't just fire on that single element. It travels in a complete cycle:

```
                            [ Window / Document ]
                                      |   ^
            1. CAPTURING PHASE        |   |    3. BUBBLING PHASE
            (Travels downward)        |   |    (Bubbles upward)
                                      v   |
                                 [ <body> ]
                                      |   ^
                                      v   |
                                 [ <div> ]
                                      |   ^
                                      v   |
                              [ 2. TARGET PHASE ]
                              (The clicked button)
```

1. **Capturing Phase:** The event travels downward from `window` down to the target element's parent.
2. **Target Phase:** The event reaches the actual element clicked.
3. **Bubbling Phase (Default):** The event bubbles back upward through every ancestor element until reaching `window`.

```javascript
// By default, listeners listen during the BUBBLING phase.
// To listen during the CAPTURING phase, set capture: true:
document.body.addEventListener('click', (e) => {
  console.log('Body captured event first!');
}, { capture: true });
```

---

## 🚀 5. The Event Delegation Pattern

### The Problem:
Imagine an e-commerce list with 500 items. Attaching 500 separate event listeners consumes substantial browser memory and breaks when new items are added dynamically:

```javascript
// ❌ BAD: 500 individual listeners (Memory bloat & fails for dynamically added items)
document.querySelectorAll('.delete-btn').forEach(btn => {
  btn.addEventListener('click', handleDelete);
});
```

### The Solution: Event Delegation
Attach **a single listener** to the common parent element. Take advantage of event bubbling to catch events from all present and future children:

```javascript
// ✅ GOOD: A single parent listener using event.target and closest()
const itemsList = document.querySelector('#product-list');

itemsList.addEventListener('click', (event) => {
  // Check if the click occurred on or inside a delete button:
  const deleteBtn = event.target.closest('.delete-btn');

  if (deleteBtn && itemsList.contains(deleteBtn)) {
    const productId = deleteBtn.dataset.id;
    console.log(`Deleting product: ${productId}`);
    deleteBtn.closest('.product-item').remove();
  }
});
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Inline HTML event handlers (Violates separation of concerns & CSP rules)
// <button onclick="handleClick()">Submit</button>

// ✅ GOOD: Programmatic binding with addEventListener
const submitBtn = document.querySelector('#submit-btn');
submitBtn.addEventListener('click', handleSubmit);

// ❌ BAD: Forgetting to remove listeners on components that get destroyed (Memory Leak!)
class Modal {
  open() {
    window.addEventListener('resize', this.onResize); // Keeps reference forever!
  }
}

// ✅ GOOD: Cleaning up event listeners when component unmounts
class Modal {
  close() {
    window.removeEventListener('resize', this.onResize);
  }
}
```

---

## 📝 7. Summary & Next Steps
- Never use inline `onclick` HTML attributes; always use **`addEventListener()`**.
- Remember that **`e.target`** is the element clicked, while **`e.currentTarget`** is the element holding the listener.
- Events **bubble upward** by default through parent elements.
- Always implement **Event Delegation** for lists and dynamic elements to optimize memory and handle runtime DOM additions.
- In the next lesson, we will explore **Forms & Input Validation: Preventing Submits, State Tracking, and Sanitization**.
