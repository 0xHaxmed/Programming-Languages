# 06 - The Event Loop & Concurrency: Microtasks, Macrotasks & Starvation

> 🌐 **Language / اللغة:** [English 🇬🇧](./06-event-loop-concurrency.md) | [العربية 🇸🇦](../ar/06-event-loop-concurrency.md)

---

## 🎯 1. Overview & Learning Objectives

JavaScript is famously described as a **single-threaded, non-blocking, asynchronous concurrent language**. While JavaScript's execution thread can only process one instruction at a time on its **Call Stack**, the surrounding host runtime (the browser or Node.js) orchestrates concurrency through the **Event Loop**.

By the end of this lesson, you will master:
1. The exact architecture of the JavaScript runtime: **Call Stack**, **Memory Heap**, **Background APIs**, **Microtask Queue**, and **Macrotask (Task) Queue**.
2. The precise execution order of an Event Loop "Tick".
3. The functional differences between **Microtasks** (`Promise`, `queueMicrotask`, `MutationObserver`) and **Macrotasks** (`setTimeout`, `setInterval`, I/O).
4. Diagnosing and preventing **Microtask Starvation** (the UI freeze phenomenon).
5. Yielding control to the main thread during heavy computations using chunking and modern **`scheduler.yield()`**.

---

## 🏗️ 2. Architectural Anatomy of the Event Loop

The JavaScript runtime consists of distinct cooperating components:

```
+-------------------------------------------------------------------------+
|                              JavaScript Engine                          |
|                                                                         |
|   +-----------------------+               +-----------------------+     |
|   |      Memory Heap      |               |      Call Stack       |     |
|   |  (Object allocation)  |               |  (Frames: fn1 -> fn2) |     |
|   +-----------------------+               +-----------+-----------+     |
+-------------------------------------------------------|-----------------+
                                                        |
                                                        v
+------------------------------------+      +-----------------------------+
|        Background Host APIs        |      |       Event Loop Gate       |
|  (Timers, Fetch, DOM, libuv I/O)   |      |                             |
+-----------------+------------------+      +--------------+--------------+
                  |                                        |
                  v                                        v
+------------------------------------+      +-----------------------------+
|          Microtask Queue           |      |    Macrotask / Task Queue   |
| (Promise.then, queueMicrotask, MO) |      | (setTimeout, setInterval,   |
| * Drained completely per tick!     |      |  UI events, messageChannel) |
+------------------------------------+      +-----------------------------+
```

### The Strict Execution Order of a "Tick":
1. **Execute Synchronous Code:** Run synchronous frames from the **Call Stack** until it is completely empty.
2. **Drain Microtask Queue:** If the Call Stack is empty, execute the first microtask. Continue executing microtasks **until the microtask queue is entirely empty**. (Crucial: if a microtask schedules another microtask, it runs in the *same* tick!).
3. **Render Pipeline (Browser only):** The browser evaluates whether the display requires a repaint (style recalculation, layout, paint).
4. **Execute ONE Macrotask:** Pick the oldest task from the Macrotask Queue, push it to the Call Stack, and execute it.
5. **Repeat:** Return to Step 2 immediately.

---

## 🔬 3. Microtasks vs Macrotasks: Step-by-Step Tracing

Consider this classic interview riddle. Can you predict the exact console output?

```javascript
console.log('1: Synchronous start');

setTimeout(() => {
  console.log('2: Macrotask (setTimeout 0)');
}, 0);

Promise.resolve()
  .then(() => {
    console.log('3: Microtask 1 (Promise.then)');
  })
  .then(() => {
    console.log('4: Microtask 2 (Chained Promise.then)');
  });

queueMicrotask(() => {
  console.log('5: Microtask 3 (queueMicrotask)');
});

console.log('6: Synchronous end');
```

### Trace Breakdown:
1. `console.log('1: Synchronous start')` executes immediately on the Call Stack. -> **Output: `1`**
2. `setTimeout(..., 0)` registers a timer with the browser/Node host API. When the timer elapses (0ms), the callback is queued in the **Macrotask Queue**.
3. `Promise.resolve().then(...)` queues its callback into the **Microtask Queue**.
4. `queueMicrotask(...)` explicitly queues its callback into the **Microtask Queue**.
5. `console.log('6: Synchronous end')` executes immediately on the Call Stack. -> **Output: `6`**
6. *Call Stack is now empty!* The engine drains the **Microtask Queue**:
   - First microtask runs: prints `3: Microtask 1`, returns undefined, which queues its chained `.then()` into the microtask queue. -> **Output: `3`**
   - Next microtask runs: prints `5: Microtask 3`. -> **Output: `5`**
   - Next microtask runs: prints `4: Microtask 2`. -> **Output: `4`**
7. *Microtask Queue is now empty!* The engine renders frames if needed.
8. The engine picks **one** task from the Macrotask Queue:
   - `setTimeout` callback runs: prints `2: Macrotask`. -> **Output: `2`**

**Final Output:**
```
1: Synchronous start
6: Synchronous end
3: Microtask 1 (Promise.then)
5: Microtask 3 (queueMicrotask)
4: Microtask 2 (Chained Promise.then)
2: Macrotask (setTimeout 0)
```

---

## 🛑 4. Microtask Starvation (UI Freezing)

Because the Event Loop **must drain all microtasks** before moving on to the rendering phase or the next macrotask, an infinite or recursive microtask chain will **starve the event loop**. 

This completely locks the browser tab: clicks are ignored, animations freeze, and the page becomes completely unresponsive.

```javascript
// ⚠️ DANGEROUS: Microtask Starvation (Crashes / Freezes the Main Thread)
function starveEventLoop() {
  queueMicrotask(() => {
    // Queues another microtask before the current microtask phase ever finishes:
    starveEventLoop();
  });
}

// Contrast with Macrotask Recursion (Does NOT freeze browser UI!):
function safeMacrotaskLoop() {
  setTimeout(() => {
    // Browser renders UI and handles clicks between each tick!
    safeMacrotaskLoop();
  }, 0);
}
```

> **Key Rule:** Never invoke recursive logic inside promises or `queueMicrotask` without an exit condition.

---

## ⏱️ 5. Long Tasks & Yielding to the Main Thread

A **Long Task** is defined by Web Vitals as any execution frame that occupies the main thread for **more than 50 milliseconds**. Long tasks cause poor **Interaction to Next Paint (INP)** scores and perceptible input lag.

### The Problem: Monolithic Processing
```javascript
// Blocks the main thread for several seconds:
function processOneMillionRecords(records) {
  for (let i = 0; i < records.length; i++) {
    heavyMathComputation(records[i]);
  }
}
```

### The Solution: Cooperative Multitasking (Yielding)

We can break the processing into time-boxed chunks and periodically yield control back to the Event Loop so user interactions and UI paints can occur.

#### Modern Approach: `scheduler.yield()` (Chrome 129+ / W3C Draft)
```javascript
async function processRecordsWithYield(records) {
  for (let i = 0; i < records.length; i++) {
    heavyMathComputation(records[i]);

    // Yield control every 50 iterations:
    if (i % 50 === 0) {
      if ('scheduler' in window && 'yield' in window.scheduler) {
        await window.scheduler.yield(); // Modern standards-track API
      } else {
        // Universal fallback using MessageChannel or setTimeout
        await new Promise((resolve) => setTimeout(resolve, 0));
      }
    }
  }
}
```

#### Enterprise Chunking Utility with Time Slicing:
```javascript
async function timeSlicedTask(items, processor, maxExecutionChunkMs = 16) {
  let index = 0;

  while (index < items.length) {
    const chunkStart = performance.now();

    // Work continuously as long as we are under our budget (16ms = ~60fps)
    while (index < items.length && (performance.now() - chunkStart) < maxExecutionChunkMs) {
      processor(items[index]);
      index++;
    }

    // Yield back to the browser so it can handle clicks & paint frames:
    if (index < items.length) {
      await new Promise((resolve) => setTimeout(resolve, 0));
    }
  }
}
```

---

## 🧼 6. Clean Code: Good vs Bad Patterns

```javascript
// ❌ BAD: Using setTimeout(fn, 0) when you intend microtask immediacy
function notifyObservers(data) {
  setTimeout(() => {
    // Sluggish: Pushed behind pending UI events and macrotask delays
    listeners.forEach((fn) => fn(data));
  }, 0);
}

// ✅ GOOD: Using queueMicrotask for fast, high-priority state sync
function notifyObservers(data) {
  queueMicrotask(() => {
    // Runs right after the current function finishes, before UI paint
    listeners.forEach((fn) => fn(data));
  });
}

// ❌ BAD: Blocking synchronous calculations in network request handlers
button.addEventListener('click', () => {
  const data = computeMassiveDataset(); // 800ms freeze on click
  displayData(data);
});

// ✅ GOOD: Time-slice or offload to Web Worker
button.addEventListener('click', async () => {
  showSpinner();
  const data = await runInWorker('computeWorker.js');
  displayData(data);
  hideSpinner();
});
```

---

## 📝 7. Summary & Next Steps

- The JavaScript engine is single-threaded; concurrency is provided by the **Event Loop**.
- **Synchronous code** runs first on the **Call Stack**.
- When the Call Stack is empty, **ALL Microtasks** (`Promise.then`, `queueMicrotask`) are executed until the queue is depleted.
- The browser handles **UI Rendering & Style Recalculation** between ticks.
- **Macrotasks** (`setTimeout`, `setInterval`, I/O) run **one at a time** per event loop cycle.
- Recursive microtasks cause **Microtask Starvation**, freezing the application.
- Break up CPU-heavy tasks using time-slicing and **`scheduler.yield()`** or `setTimeout(..., 0)` to maintain high responsiveness.
- In the next lesson, we will explore **Classes and ES Modules: Encapsulation, Private Fields & ESM Internals**.
