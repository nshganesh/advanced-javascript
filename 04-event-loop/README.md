# 🚀 Event Loop & Call Stack

## 📖 Theory

### What is the Event Loop?
The Event Loop is JavaScript's mechanism for handling asynchronous operations. It's what allows JavaScript to be **single-threaded** yet handle multiple operations concurrently through **non-blocking I/O**.

### Key Concepts

#### 1. Single-Threaded Nature
- JavaScript has **one main thread** (the main execution thread)
- Only **one piece of code** can execute at a time
- **No parallel execution** of JavaScript code

#### 2. Call Stack
- **LIFO (Last In, First Out)** data structure
- Tracks function calls and their execution context
- When a function is called, it's **pushed** onto the stack
- When a function returns, it's **popped** off the stack

#### 3. Event Loop Components
- **Call Stack**: Where synchronous code executes
- **Web APIs**: Browser/node.js APIs (setTimeout, fetch, DOM events)
- **Callback Queue**: Where async callbacks wait
- **Microtask Queue**: Where promises and other microtasks wait
- **Event Loop**: Monitors stack and queues, moves tasks between them

#### 4. Execution Priority
1. **Call Stack** (synchronous code)
2. **Microtask Queue** (promises, queueMicrotask)
3. **Macrotask Queue** (setTimeout, setInterval, DOM events)

## 💡 Interactive Examples

### Example 1: Basic Call Stack
```javascript
function first() {
    console.log("First function");
    second();
}

function second() {
    console.log("Second function");
    third();
}

function third() {
    console.log("Third function");
}

first();
console.log("Global execution");

// Output:
// "First function"
// "Second function"
// "Third function"
// "Global execution"
```

### Example 2: setTimeout and Event Loop
```javascript
console.log("Start");

setTimeout(function() {
    console.log("Timeout callback");
}, 0);

console.log("End");

// Output:
// "Start"
// "End"
// "Timeout callback"
```

### Example 3: Promises and Microtasks
```javascript
console.log("Start");

Promise.resolve().then(function() {
    console.log("Promise resolved");
});

setTimeout(function() {
    console.log("Timeout callback");
}, 0);

console.log("End");

// Output:
// "Start"
// "End"
// "Promise resolved"
// "Timeout callback"
```

### Example 4: Complex Event Loop Example
```javascript
console.log("1. Script start");

setTimeout(function() {
    console.log("2. Timeout 1");
    Promise.resolve().then(function() {
        console.log("3. Promise in timeout");
    });
}, 0);

Promise.resolve().then(function() {
    console.log("4. Promise 1");
    setTimeout(function() {
        console.log("5. Timeout in promise");
    }, 0);
});

console.log("6. Script end");
```

## ❓ Practice Questions

### Question 1: Basic Call Stack Order
```javascript
function a() {
    console.log("A");
    b();
}

function b() {
    console.log("B");
    c();
}

function c() {
    console.log("C");
}

a();
console.log("D");
```

**Options:**
- A) A, B, C, D
- B) D, A, B, C
- C) A, D, B, C
- D) C, B, A, D

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) A, B, C, D**

**Explanation:** Functions execute in the order they're called, following the call stack (LIFO principle).

**Call Stack Analysis:**
```javascript
function a() {
    console.log("A");     // 1. Log "A"
    b();                  // 2. Call b()
}

function b() {
    console.log("B");     // 3. Log "B"
    c();                  // 4. Call c()
}

function c() {
    console.log("C");     // 5. Log "C"
}

a();                     // 6. Call a()
console.log("D");        // 7. Log "D"
```

**Call Stack Flow:**
1. `a()` pushed onto stack
2. `b()` pushed onto stack (inside a)
3. `c()` pushed onto stack (inside b)
4. `c()` executes and pops off
5. `b()` continues and pops off
6. `a()` continues and pops off
7. Global execution continues

**Key Concept:** The call stack follows LIFO (Last In, First Out) - the last function called is the first to complete.

</details>

---

### Question 2: setTimeout vs Synchronous Code
```javascript
console.log("Start");

setTimeout(function() {
    console.log("Timeout");
}, 1000);

console.log("End");

// What's the output order?
```

**Options:**
- A) Start, Timeout, End
- B) Start, End, Timeout
- C) Timeout, Start, End
- D) Start, End (Timeout never prints)

<details>
<summary>Click to see answer and explanation</summary>

**Answer: B) Start, End, Timeout**

**Explanation:** `setTimeout` is asynchronous and goes through the event loop, while synchronous code executes immediately.

**Event Loop Analysis:**
```javascript
console.log("Start");                    // 1. Synchronous - executes immediately

setTimeout(function() {                  // 2. Asynchronous - goes to Web APIs
    console.log("Timeout");
}, 1000);

console.log("End");                      // 3. Synchronous - executes immediately

// After 1000ms, callback goes to Callback Queue
// Event Loop moves it to Call Stack when stack is empty
```

**Execution Flow:**
1. **Call Stack**: Execute synchronous code
   - Log "Start"
   - Register setTimeout callback (Web APIs)
   - Log "End"
2. **Web APIs**: Wait 1000ms
3. **Callback Queue**: Timeout callback waits
4. **Event Loop**: Move callback to Call Stack
5. **Call Stack**: Execute callback, log "Timeout"

**Key Concept:** Asynchronous operations don't block the main thread - they're handled by the event loop after synchronous code completes.

</details>

---

### Question 3: Promise vs setTimeout Priority
```javascript
console.log("1. Start");

setTimeout(function() {
    console.log("2. Timeout");
}, 0);

Promise.resolve().then(function() {
    console.log("3. Promise");
});

console.log("4. End");
```

**Options:**
- A) 1, 2, 3, 4
- B) 1, 4, 2, 3
- C) 1, 4, 3, 2
- D) 1, 3, 4, 2

<details>
<summary>Click to see answer and explanation</summary>

**Answer: C) 1, 4, 3, 2**

**Explanation:** Microtasks (promises) have higher priority than macrotasks (setTimeout).

**Event Loop Priority Analysis:**
```javascript
console.log("1. Start");                    // 1. Synchronous execution

setTimeout(function() {                     // 2. Macrotask - goes to Callback Queue
    console.log("2. Timeout");
}, 0);

Promise.resolve().then(function() {         // 3. Microtask - goes to Microtask Queue
    console.log("3. Promise");
});

console.log("4. End");                      // 4. Synchronous execution
```

**Execution Order:**
1. **Call Stack** (synchronous): 1, 4
2. **Microtask Queue** (promises): 3
3. **Macrotask Queue** (setTimeout): 2

**Key Concept:** Event Loop processes tasks in this order:
1. Execute all synchronous code (Call Stack)
2. Execute all microtasks (Microtask Queue)
3. Execute one macrotask (Callback Queue)
4. Repeat from step 2

</details>

---

### Question 4: Nested Promises and Timeouts
```javascript
console.log("1. Script start");

setTimeout(function() {
    console.log("2. Timeout 1");
    Promise.resolve().then(function() {
        console.log("3. Promise in timeout");
    });
}, 0);

Promise.resolve().then(function() {
    console.log("4. Promise 1");
    setTimeout(function() {
        console.log("5. Timeout in promise");
    }, 0);
});

console.log("6. Script end");
```

**Options:**
- A) 1, 6, 4, 2, 3, 5
- B) 1, 6, 2, 4, 3, 5
- C) 1, 6, 4, 2, 5, 3
- D) 1, 6, 2, 4, 5, 3

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) 1, 6, 4, 2, 3, 5**

**Explanation:** This demonstrates how the event loop handles nested asynchronous operations.

**Detailed Execution Flow:**
```javascript
console.log("1. Script start");            // 1. Synchronous

setTimeout(function() {                     // 2. Macrotask - goes to Callback Queue
    console.log("2. Timeout 1");
    Promise.resolve().then(function() {     // 3. Microtask - goes to Microtask Queue
        console.log("3. Promise in timeout");
    });
}, 0);

Promise.resolve().then(function() {         // 4. Microtask - goes to Microtask Queue
    console.log("4. Promise 1");
    setTimeout(function() {                 // 5. Macrotask - goes to Callback Queue
        console.log("5. Timeout in promise");
    }, 0);
});

console.log("6. Script end");              // 6. Synchronous
```

**Step-by-Step Execution:**
1. **Call Stack**: Execute 1, 6
2. **Microtask Queue**: Execute 4 (which schedules another setTimeout)
3. **Macrotask Queue**: Execute 2 (which schedules a promise)
4. **Microtask Queue**: Execute 3 (from the timeout)
5. **Macrotask Queue**: Execute 5 (from the promise)

**Key Concept:** The event loop processes one macrotask at a time, but processes all available microtasks before moving to the next macrotask.

</details>

---

### Question 5: Blocking vs Non-blocking Operations
```javascript
console.log("Start");

// Simulate blocking operation
var start = Date.now();
while (Date.now() - start < 1000) {
    // Block for 1 second
}

setTimeout(function() {
    console.log("Timeout after blocking");
}, 0);

console.log("End");
```

**Options:**
- A) Start, Timeout after blocking, End
- B) Start, End, Timeout after blocking
- C) Start, (1 second delay), End, Timeout after blocking
- D) Start, (1 second delay), Timeout after blocking, End

<details>
<summary>Click to see answer and explanation</summary>

**Answer: C) Start, (1 second delay), End, Timeout after blocking**

**Explanation:** The while loop blocks the main thread, preventing the event loop from processing other tasks.

**Blocking Analysis:**
```javascript
console.log("Start");                      // 1. Execute immediately

// BLOCKING OPERATION
var start = Date.now();
while (Date.now() - start < 1000) {       // 2. Block main thread for 1 second
    // This prevents event loop from running
}

setTimeout(function() {                     // 3. Schedule callback (but can't execute yet)
    console.log("Timeout after blocking");
}, 0);

console.log("End");                        // 4. Execute after blocking ends
```

**What Happens:**
1. Log "Start"
2. **Block main thread** for 1 second (no other code can execute)
3. Schedule setTimeout callback
4. Log "End"
5. Event loop processes the setTimeout callback

**Key Concept:** Blocking operations in the main thread prevent the event loop from processing other tasks, including timeouts and promises.

</details>

---

### Question 6: Event Loop with DOM Events
```javascript
console.log("1. Script start");

document.addEventListener('click', function() {
    console.log("2. Click event");
    Promise.resolve().then(function() {
        console.log("3. Promise in click");
    });
});

setTimeout(function() {
    console.log("4. Timeout");
}, 0);

Promise.resolve().then(function() {
    console.log("5. Promise");
});

console.log("6. Script end");

// What happens when you click the document?
```

**Options:**
- A) 1, 6, 5, 4, 2, 3
- B) 1, 6, 5, 4, 3, 2
- C) 1, 6, 4, 5, 2, 3
- D) 1, 6, 4, 5, 3, 2

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) 1, 6, 5, 4, 2, 3**

**Explanation:** DOM events are macrotasks, and the event loop processes them in the correct order.

**Event Loop with DOM Events:**
```javascript
console.log("1. Script start");            // 1. Synchronous execution

document.addEventListener('click', function() {  // Register event listener
    console.log("2. Click event");             // Will execute when clicked
    Promise.resolve().then(function() {         // Microtask in event handler
        console.log("3. Promise in click");
    });
});

setTimeout(function() {                     // 2. Macrotask - goes to Callback Queue
    console.log("4. Timeout");
}, 0);

Promise.resolve().then(function() {         // 3. Microtask - goes to Microtask Queue
    console.log("5. Promise");
});

console.log("6. Script end");              // 4. Synchronous execution
```

**Execution Order:**
1. **Call Stack**: 1, 6
2. **Microtask Queue**: 5
3. **Macrotask Queue**: 4 (setTimeout)
4. **User clicks document**
5. **Macrotask Queue**: 2 (click event)
6. **Microtask Queue**: 3 (promise in click)

**Key Concept:** DOM events are treated as macrotasks by the event loop, and they follow the same priority rules as other asynchronous operations.

</details>

## 🔍 Deep Dive

### Advanced Event Loop Scenarios

#### 1. Microtask Queue Starvation
```javascript
function createMicrotask() {
    Promise.resolve().then(function() {
        console.log("Microtask");
        createMicrotask(); // Creates infinite microtasks!
    });
}

setTimeout(function() {
    console.log("Timeout"); // This never executes!
}, 0);

createMicrotask();
console.log("Start");
```

**Problem:** Infinite microtasks prevent macrotasks from executing.

**Solution:** Be careful with recursive microtasks.

#### 2. Task Scheduling with Different APIs
```javascript
console.log("Start");

// Different ways to schedule tasks
setTimeout(function() { console.log("setTimeout"); }, 0);
setImmediate(function() { console.log("setImmediate"); }); // Node.js only
process.nextTick(function() { console.log("nextTick"); }); // Node.js only
Promise.resolve().then(function() { console.log("Promise"); });

console.log("End");
```

**Priority Order (Node.js):**
1. `process.nextTick` (highest priority)
2. `Promise` microtasks
3. `setImmediate` (higher priority than setTimeout)
4. `setTimeout` (lower priority than setImmediate)

#### 3. Event Loop in Different Environments

**Browser:**
```javascript
// Browser event loop
console.log("Start");
setTimeout(() => console.log("Timeout"), 0);
Promise.resolve().then(() => console.log("Promise"));
console.log("End");

// Output: Start, End, Promise, Timeout
```

**Node.js:**
```javascript
// Node.js event loop (may differ slightly)
console.log("Start");
setImmediate(() => console.log("setImmediate"));
setTimeout(() => console.log("setTimeout"), 0);
Promise.resolve().then(() => console.log("Promise"));
process.nextTick(() => console.log("nextTick"));
console.log("End");

// Output: Start, End, nextTick, Promise, setImmediate, setTimeout
// Note: setImmediate has higher priority than setTimeout in Node.js
```

### Common Event Loop Pitfalls

#### 1. Blocking the Main Thread
```javascript
// ❌ Bad: Blocking operation
function blockingOperation() {
    var start = Date.now();
    while (Date.now() - start < 1000) {
        // Block for 1 second
    }
}

// ✅ Good: Non-blocking operation
function nonBlockingOperation() {
    return new Promise(resolve => {
        setTimeout(() => {
            // Do work here
            resolve();
        }, 1000);
    });
}
```

#### 2. Ignoring Promise Chain Order
```javascript
// ❌ Bad: Unpredictable order
Promise.resolve().then(() => console.log("A"));
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));

// ✅ Good: Predictable order
Promise.resolve()
    .then(() => console.log("A"))
    .then(() => console.log("C"));
setTimeout(() => console.log("B"), 0);
```

#### 3. Not Understanding Queue Priorities
```javascript
// Understanding priorities is crucial
console.log("Start");

// Microtasks (highest priority)
Promise.resolve().then(() => console.log("Microtask 1"));
Promise.resolve().then(() => console.log("Microtask 2"));

// Macrotasks (lower priority)
setTimeout(() => console.log("Macrotask 1"), 0);
setTimeout(() => console.log("Macrotask 2"), 0);

console.log("End");

// Output: Start, End, Microtask 1, Microtask 2, Macrotask 1, Macrotask 2
```

## 📝 Summary & Mind Map

### 🧠 Key Takeaways

```
EVENT LOOP & CALL STACK
├── Single-Threaded Nature
│   ├── One main execution thread
│   ├── No parallel JavaScript execution
│   └── Non-blocking I/O through event loop
├── Call Stack
│   ├── LIFO (Last In, First Out)
│   ├── Tracks function execution
│   └── Synchronous code execution
├── Event Loop Components
│   ├── Call Stack: synchronous execution
│   ├── Web APIs: browser/node.js APIs
│   ├── Callback Queue: macrotasks (setTimeout, events)
│   ├── Microtask Queue: promises, queueMicrotask
│   └── Event Loop: coordinator
├── Execution Priority
│   1. Call Stack (synchronous)
│   2. Microtask Queue (promises)
│   3. Macrotask Queue (setTimeout, events)
└── Common Patterns
    ├── setTimeout vs Promise priority
    ├── Blocking vs non-blocking operations
    ├── Event handling and context
    └── Queue starvation prevention
```

### 🚨 Common Pitfalls

1. **Forgetting that JavaScript is single-threaded**
2. **Not understanding microtask vs macrotask priority**
3. **Blocking the main thread with long operations**
4. **Ignoring the event loop when debugging async code**

### ✅ Best Practices

1. **Keep synchronous operations fast and non-blocking**
2. **Use promises for complex async operations**
3. **Understand the event loop priority system**
4. **Avoid infinite microtask loops**

## 🎯 Next Steps

Ready to test your knowledge? Try the practice questions above, then move on to:
- **[Prototypal Inheritance](./../05-prototypal-inheritance/)**
- **[Promises & Async/Await](./../06-promises-async/)**

---

**💡 Pro Tip:** Understanding the event loop is crucial for writing efficient, non-blocking JavaScript code. Remember: "JavaScript is single-threaded, but the event loop makes it feel multi-threaded."

**🚀 Ready to practice? Copy the code examples into your console and experiment with different async patterns!**

**🔍 Debug Tip:** Use `console.log` with timestamps to visualize the event loop in action:**
```javascript
console.log(Date.now(), "Start");
setTimeout(() => console.log(Date.now(), "Timeout"), 100);
Promise.resolve().then(() => console.log(Date.now(), "Promise"));
console.log(Date.now(), "End");
```
