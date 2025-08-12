# 🚀 Scopes & Closures

## 📖 Theory

### What are Scopes?
Scope determines the accessibility (visibility) of variables, functions, and objects in your code. JavaScript has three main types of scope:

1. **Global Scope** - Variables accessible throughout the entire program
2. **Function Scope** - Variables accessible only within a function
3. **Block Scope** - Variables accessible only within a block (ES6+)

### What are Closures?
A closure is a function that has access to variables in its outer (enclosing) scope even after the outer function has returned. Closures are created every time a function is created.

## 💡 Interactive Examples

### Example 1: Basic Scoping
```javascript
// Global scope
var globalVar = "I'm global";

function testFunction() {
    // Function scope
    var functionVar = "I'm function-scoped";
    console.log(globalVar); // ✅ Accessible
    console.log(functionVar); // ✅ Accessible
}

testFunction();
console.log(globalVar); // ✅ Accessible
// console.log(functionVar); // ❌ ReferenceError
```

### Example 2: Block Scope (ES6+)
```javascript
{
    // Block scope
    let blockVar = "I'm block-scoped";
    const blockConst = "I'm also block-scoped";
    console.log(blockVar); // ✅ Accessible
}

// console.log(blockVar); // ❌ ReferenceError
// console.log(blockConst); // ❌ ReferenceError
```

### Example 3: Basic Closure
```javascript
function outerFunction(x) {
    // This variable is in the outer function's scope
    var outerVar = "I'm from outer function";
    
    function innerFunction(y) {
        // This function can access outerVar due to closure
        console.log(outerVar); // ✅ Accessible
        console.log(x); // ✅ Accessible (parameter)
        console.log(y); // ✅ Accessible (parameter)
    }
    
    return innerFunction;
}

var closure = outerFunction("Hello");
closure("World"); // Calls innerFunction with "World"
```

## ❓ Practice Questions

### Question 1: What will be logged?
```javascript
var x = 1;
function test() {
    console.log(x);
    var x = 2;
    console.log(x);
}
test();
console.log(x);
```

**Options:**
A) 1, 2, 1
B) undefined, 2, 1
C) 1, 2, 2
D) ReferenceError

<details>
<summary>Click to see answer and explanation</summary>

**Answer: B) undefined, 2, 1**

**Explanation:** Inside the function, `var x` is hoisted, creating a local variable that shadows the global `x`. The first `console.log` shows `undefined` due to hoisting.

**Scope Chain Analysis:**
```javascript
var x = 1;  // Global scope

function test() {
    var x;           // Local scope - hoisted
    console.log(x);  // undefined (local x, not yet assigned)
    x = 2;          // Local x assignment
    console.log(x);  // 2 (local x)
}

test();
console.log(x);      // 1 (global x, unchanged)
```

**Key Concept:** Variable shadowing occurs when a local variable has the same name as a global variable.

</details>

---

### Question 2: Closure Variable Access
```javascript
function createCounter() {
    var count = 0;
    return function() {
        count++;
        return count;
    };
}

var counter1 = createCounter();
var counter2 = createCounter();

console.log(counter1()); // ?
console.log(counter1()); // ?
console.log(counter2()); // ?
```

**Options:**
A) 1, 2, 1
B) 1, 1, 1
C) 1, 2, 2
D) 0, 1, 0

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) 1, 2, 1**

**Explanation:** Each call to `createCounter()` creates a new scope with its own `count` variable. `counter1` and `counter2` maintain separate counts.

**Closure Analysis:**
```javascript
function createCounter() {
    var count = 0;  // Private variable in closure
    
    return function() {
        count++;     // Access to outer scope variable
        return count;
    };
}

var counter1 = createCounter();  // Creates closure with count = 0
var counter2 = createCounter();  // Creates NEW closure with count = 0

console.log(counter1()); // 1 (first closure: count = 1)
console.log(counter1()); // 2 (first closure: count = 2)
console.log(counter2()); // 1 (second closure: count = 1)
```

**Key Insight:** Each function call creates a new execution context with its own variables, even if the function body is identical.

</details>

---

### Question 3: Block Scope vs Function Scope
```javascript
var x = 1;
{
    var x = 2;
    console.log(x);
}
console.log(x);
```

**Options:**
A) 2, 2
B) 2, 1
C) 1, 2
D) 1, 1

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) 2, 2**

**Explanation:** `var` is function-scoped, not block-scoped. The `x` inside the block is the same variable as the outer `x`.

**Scope Analysis:**
```javascript
var x = 1;  // Global scope

{
    var x = 2;      // Same variable (global scope)
    console.log(x); // 2
}

console.log(x);     // 2 (same variable, value changed)
```

**Key Concept:** `var` declarations are hoisted to the function scope (or global scope), not block scope. Only `let` and `const` respect block boundaries.

</details>

---

### Question 4: Closure Memory Management
```javascript
function createHeavyObject() {
    var heavyData = new Array(1000000).fill("data");
    
    return function() {
        console.log(heavyData.length);
    };
}

var heavyFunction = createHeavyObject();
// What happens to heavyData in memory?
```

**Options:**
A) heavyData is garbage collected immediately
B) heavyData stays in memory due to closure
C) heavyData is moved to global scope
D) heavyData becomes undefined

<details>
<summary>Click to see answer and explanation</summary>

**Answer: B) heavyData stays in memory due to closure**

**Explanation:** The closure maintains a reference to `heavyData`, preventing it from being garbage collected.

**Memory Analysis:**
```javascript
function createHeavyObject() {
    var heavyData = new Array(1000000).fill("data");  // Large array
    
    return function() {
        console.log(heavyData.length);  // Closure references heavyData
    };
}

var heavyFunction = createHeavyObject();
// heavyData is NOT garbage collected because:
// 1. The returned function has access to heavyData
// 2. JavaScript can't free memory that's still referenced
// 3. This can lead to memory leaks if not managed properly
```

**Solution - Prevent Memory Leaks:**
```javascript
// When done with the function, set it to null
heavyFunction = null;
// Now heavyData can be garbage collected
```

</details>

---

### Question 5: Loop Closure Problem
```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);
    }, 100);
}
```

**What will be the output?**

<details>
<summary>Click to see answer and explanation</summary>

**Output: 3, 3, 3**

**Explanation:** All setTimeout callbacks reference the same `i` variable. By the time they execute, the loop has finished and `i` equals 3.

**Why This Happens:**
```javascript
for (var i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // All functions reference the same 'i'
    }, 100);
}

// Execution timeline:
// 1. Loop runs: i = 0, 1, 2, then i = 3 (loop ends)
// 2. After 100ms, all timeouts execute
// 3. All functions see i = 3
```

**Solutions:**

**Solution 1: IIFE (Immediately Invoked Function Expression)**
```javascript
for (var i = 0; i < 3; i++) {
    (function(index) {
        setTimeout(function() {
            console.log(index);  // Each function gets its own 'index'
        }, 100);
    })(i);
}
// Output: 0, 1, 2
```

**Solution 2: let (ES6+)**
```javascript
for (let i = 0; i < 3; i++) {
    setTimeout(function() {
        console.log(i);  // 'let' creates block scope for each iteration
    }, 100);
}
// Output: 0, 1, 2
```

</details>

---

### Question 6: Module Pattern with Closure
```javascript
var calculator = (function() {
    var result = 0;  // Private variable
    
    function add(a, b) {  // Private function
        return a + b;
    }
    
    return {
        add: function(a, b) {
            result = add(a, b);
            return result;
        },
        getResult: function() {
            return result;
        }
    };
})();

console.log(calculator.add(5, 3));    // ?
console.log(calculator.getResult());   // ?
console.log(calculator.result);        // ?
```

**What will be the output?**

<details>
<summary>Click to see answer and explanation</summary>

**Output: 8, 8, undefined**

**Explanation:** The module pattern uses closures to create private variables and functions that are inaccessible from outside.

**Closure Analysis:**
```javascript
var calculator = (function() {
    var result = 0;  // Private variable (closure)
    
    function add(a, b) {  // Private function (closure)
        return a + b;
    }
    
    return {
        add: function(a, b) {
            result = add(a, b);  // Access to private variable and function
            return result;
        },
        getResult: function() {
            return result;  // Access to private variable
        }
    };
})();

console.log(calculator.add(5, 3));    // 8 (calls private add function)
console.log(calculator.getResult());   // 8 (accesses private result variable)
console.log(calculator.result);        // undefined (result is private)
```

**Key Benefits:**
- **Data Privacy**: `result` and `add` are not accessible from outside
- **State Management**: The closure maintains state between function calls
- **Clean API**: Only public methods are exposed

</details>

## 🔍 Deep Dive

### Advanced Closure Patterns

#### 1. Partial Application
```javascript
function multiply(a, b) {
    return a * b;
}

function partial(fn, ...args) {
    return function(...moreArgs) {
        return fn.apply(this, args.concat(moreArgs));
    };
}

var multiplyByTwo = partial(multiply, 2);
console.log(multiplyByTwo(5)); // 10
```

#### 2. Memoization with Closure
```javascript
function memoize(fn) {
    var cache = {};
    
    return function(...args) {
        var key = JSON.stringify(args);
        if (cache[key] === undefined) {
            cache[key] = fn.apply(this, args);
        }
        return cache[key];
    };
}

var expensiveFunction = memoize(function(n) {
    console.log("Computing...");
    return n * 2;
});

console.log(expensiveFunction(5)); // Computing... 10
console.log(expensiveFunction(5)); // 10 (from cache)
```

#### 3. Factory Functions
```javascript
function createPerson(name, age) {
    var privateData = {
        name: name,
        age: age,
        id: Math.random().toString(36).substr(2, 9)
    };
    
    return {
        getName: function() {
            return privateData.name;
        },
        getAge: function() {
            return privateData.age;
        },
        getId: function() {
            return privateData.id;
        },
        setAge: function(newAge) {
            if (newAge > 0) {
                privateData.age = newAge;
            }
        }
    };
}

var person = createPerson("John", 30);
console.log(person.getName()); // "John"
console.log(person.getId());   // Random ID
// person.privateData is not accessible
```

### Common Closure Pitfalls

#### 1. Accidental Closures
```javascript
// ❌ Problematic - creates closure to large object
function createHandler() {
    var largeObject = new Array(1000000).fill("data");
    
    return function(event) {
        console.log("Event:", event.type);
        // largeObject is kept in memory unnecessarily
    };
}

// ✅ Solution - only capture what you need
function createHandler() {
    return function(event) {
        console.log("Event:", event.type);
        // No unnecessary variables captured
    };
}
```

#### 2. this Binding Issues
```javascript
var obj = {
    name: "Object",
    createHandler: function() {
        return function() {
            console.log("Name:", this.name);  // this is undefined!
        };
    }
};

var handler = obj.createHandler();
handler(); // "Name: undefined"

// ✅ Solution 1: Arrow function
var obj = {
    name: "Object",
    createHandler: function() {
        return () => {
            console.log("Name:", this.name);  // this is bound correctly
        };
    }
};

// ✅ Solution 2: bind
var obj = {
    name: "Object",
    createHandler: function() {
        return function() {
            console.log("Name:", this.name);
        }.bind(this);
    }
};
```

## 📝 Summary & Mind Map

### 🧠 Key Takeaways

```
SCOPES & CLOSURES
├── Scope Types
│   ├── Global Scope: accessible everywhere
│   ├── Function Scope: accessible within function
│   └── Block Scope: accessible within block (ES6+)
├── Closure Fundamentals
│   ├── Function + Lexical Environment
│   ├── Access to outer scope variables
│   ├── Variables persist after outer function returns
│   └── Each function call creates new closure
├── Common Patterns
│   ├── Module Pattern: private/public API
│   ├── Partial Application: pre-fill arguments
│   ├── Memoization: cache expensive results
│   └── Factory Functions: create objects with state
├── Pitfalls & Solutions
│   ├── Loop closure problem (use IIFE or let)
│   ├── Memory leaks (set references to null)
│   ├── this binding issues (use arrow functions)
│   └── Variable shadowing (be careful with naming)
└── Best Practices
    ├── Use closures for data privacy
    ├── Be mindful of memory usage
    ├── Understand scope chain
    └── Use appropriate scope for variables
```

### 🚨 Common Pitfalls

1. **Forgetting that closures capture variables by reference**
2. **Creating memory leaks with large objects in closures**
3. **Not understanding the loop closure problem**
4. **Confusing scope with context (this keyword)**

### ✅ Best Practices

1. **Use closures for data privacy and encapsulation**
2. **Be mindful of memory usage in closures**
3. **Understand the scope chain and variable lookup**
4. **Use appropriate scope for variables (don't pollute global scope)**

## 🎯 Next Steps

Ready to test your knowledge? Try the practice questions above, then move on to:
- **[This Keyword & Context](./../03-this-keyword/)**
- **[Event Loop & Call Stack](./../04-event-loop/)**

---

**💡 Pro Tip:** Closures are one of JavaScript's most powerful features. They enable functional programming patterns, data privacy, and elegant solutions to complex problems. Understanding closures is essential for advanced JavaScript development.

**🚀 Ready to practice? Copy the code examples into your console and experiment with closures!**
