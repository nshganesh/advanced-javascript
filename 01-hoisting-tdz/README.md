# 🚀 Hoisting & Temporal Dead Zone (TDZ)

## 📖 Theory

### What is Hoisting?
Hoisting is JavaScript's default behavior of moving declarations to the top of their scope during the compilation phase. However, **only declarations are hoisted, not initializations**.

### Key Concepts

#### 1. Variable Hoisting
- **`var`**: Hoisted and initialized with `undefined`
- **`let` and `const`**: Hoisted but NOT initialized (TDZ)
- **Function declarations**: Fully hoisted
- **Function expressions**: Not hoisted

#### 2. Temporal Dead Zone (TDZ)
The period between entering scope and the actual declaration where variables exist but cannot be accessed.

## 💡 Interactive Examples

### Example 1: Basic Hoisting
```javascript
console.log(x); // undefined (not ReferenceError!)
var x = 5;

// What actually happens:
// var x;           // Declaration hoisted
// console.log(x);  // undefined
// x = 5;          // Assignment
```

### Example 2: TDZ with let/const
```javascript
console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

// What happens:
// let y;          // Declaration hoisted but not initialized
// console.log(y); // TDZ - ReferenceError!
// y = 10;        // Assignment
```

### Example 3: Function Hoisting
```javascript
hoistedFunction(); // "Hello from hoisted function!"

function hoistedFunction() {
    console.log("Hello from hoisted function!");
}

// Function expressions are NOT hoisted
try {
    notHoisted(); // TypeError: notHoisted is not a function
} catch (e) {
    console.log("Error:", e.message);
}

var notHoisted = function() {
    console.log("I'm not hoisted!");
};
```

## ❓ Practice Questions

### Question 1: What will be logged?
```javascript
console.log(a);
var a = 1;
console.log(a);
```

**Options:**
- A) undefined, 1
- B) ReferenceError, 1
- C) 1, 1
- D) undefined, undefined

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) undefined, 1**

**Explanation:** `var a` is hoisted and initialized with `undefined`, so first log shows `undefined`. After assignment, `a` becomes `1`.

**Code Execution Order:**
```javascript
// What actually happens during execution:
var a;           // Declaration hoisted to top
console.log(a);  // undefined
a = 1;          // Assignment
console.log(a);  // 1
```

</details>

---

### Question 2: What will happen?
```javascript
console.log(b);
let b = 2;
```

**Options:**
- A) undefined
- B) 2
- C) ReferenceError
- D) SyntaxError

<details>
<summary>Click to see answer and explanation</summary>

**Answer: C) ReferenceError**

**Explanation:** `let` declarations are hoisted but not initialized, creating a TDZ. Accessing `b` before declaration throws ReferenceError.

**Why this happens:**
- `let b` is hoisted to the top of the scope
- But it's not initialized (unlike `var`)
- The period between scope entry and declaration is the TDZ
- Accessing `b` in TDZ throws ReferenceError

</details>

---

### Question 3: Function vs Variable Hoisting
```javascript
console.log(typeof myFunction);
console.log(typeof myVar);

function myFunction() {}
var myVar = function() {};

console.log(typeof myFunction);
console.log(typeof myVar);
```

**Options:**
- A) function, undefined, function, function
- B) function, function, function, function
- C) undefined, undefined, function, function
- D) ReferenceError

<details>
<summary>Click to see answer and explanation</summary>

**Answer: A) function, undefined, function, function**

**Explanation:** Function declarations are fully hoisted, so `myFunction` is available as a function. `myVar` is hoisted but initialized as `undefined` until assignment.

**Execution Order:**
```javascript
// What actually happens:
function myFunction() {}  // Fully hoisted
var myVar;               // Declaration hoisted, initialized as undefined

console.log(typeof myFunction); // "function"
console.log(typeof myVar);      // "undefined"

myVar = function() {};          // Assignment

console.log(typeof myFunction); // "function"
console.log(typeof myVar);      // "function"
```

</details>

---

### Question 4: Block Scope Hoisting
```javascript
{
    console.log(x);
    var x = 100;
    console.log(x);
}
console.log(x);
```

**What will be the output?**

<details>
<summary>Click to see answer and explanation</summary>

**Output: undefined, 100, 100**

**Explanation:** `var` is function-scoped, not block-scoped. The `x` inside the block is the same variable as the outer `x`.

**Why this happens:**
- `var` declarations are hoisted to the function scope (or global scope)
- Block scopes don't create new variable scopes for `var`
- The block just creates a new execution context
- All `var` declarations in the same function scope share the same variable

**Code Execution:**
```javascript
var x;              // Declaration hoisted to function/global scope
{
    console.log(x); // undefined (not yet assigned)
    x = 100;        // Assignment
    console.log(x); // 100
}
console.log(x);     // 100 (same variable)
```

</details>

---

### Question 5: Advanced Hoisting Scenario
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

**What will be the output?**

<details>
<summary>Click to see answer and explanation</summary>

**Output: undefined, 2, 1**

**Explanation:** Inside the function, `var x` is hoisted, creating a local variable that shadows the global `x`. The first `console.log` shows `undefined` due to hoisting.

**Execution Flow:**
```javascript
var x = 1;  // Global x

function test() {
    var x;           // Local x declaration hoisted
    console.log(x);  // undefined (local x, not yet assigned)
    x = 2;          // Local x assignment
    console.log(x);  // 2 (local x)
}

test();
console.log(x);      // 1 (global x, unchanged)
```

**Key Insight:** Variable shadowing occurs when a local variable has the same name as a global variable.

</details>

---

### Question 6: Function Declaration vs Expression
```javascript
hoisted();
notHoisted();

function hoisted() { 
    console.log('I work!'); 
}

var notHoisted = function() { 
    console.log('I don\'t work!'); 
};
```

**What will happen?**

<details>
<summary>Click to see answer and explanation</summary>

**Result:** First call works, second call throws TypeError

**Explanation:** Function declarations are hoisted, but function expressions assigned to variables are not.

**Execution Order:**
```javascript
function hoisted() { 
    console.log('I work!'); 
}  // Fully hoisted

var notHoisted;  // Declaration hoisted, initialized as undefined

hoisted();       // ✅ Works - function is available
notHoisted();    // ❌ TypeError: notHoisted is not a function

notHoisted = function() { 
    console.log('I don\'t work!'); 
};  // Assignment
```

**Key Difference:**
- **Function Declaration**: `function name() {}` - fully hoisted
- **Function Expression**: `var name = function() {}` - variable hoisted as undefined

</details>

## 🔍 Deep Dive

### Advanced Scenarios

#### 1. Hoisting Order and Precedence
```javascript
// Function declarations are hoisted before variable declarations
console.log(typeof hoistedFunc);  // "function"
console.log(typeof hoistedVar);   // "undefined"

function hoistedFunc() {}
var hoistedVar = function() {};

console.log(typeof hoistedFunc);  // "function"
console.log(typeof hoistedVar);   // "function"
```

#### 2. Multiple Declarations
```javascript
var x = 1;
var x = 2; // Redeclaration allowed with var

let y = 1;
// let y = 2; // SyntaxError: Identifier 'y' has already been declared

const z = 1;
// const z = 2; // SyntaxError: Identifier 'z' has already been declared
```

**Key Insight**: `var` allows redeclaration, `let` and `const` don't.

#### 3. Practical Implications
```javascript
// ❌ Bad practice (but works due to hoisting)
console.log(badVar); // undefined
var badVar = "I'm declared after use";

// ✅ Good practice
var goodVar = "I'm declared at the top";
console.log(goodVar); // "I'm declared at the top"
```

## 📝 Summary & Mind Map

### 🧠 Key Takeaways

```
HOISTING & TDZ
├── Variable Hoisting
│   ├── var: hoisted + initialized (undefined)
│   ├── let/const: hoisted + NOT initialized (TDZ)
│   └── Function declarations: fully hoisted
├── Temporal Dead Zone
│   ├── Period between scope entry and declaration
│   ├── let/const variables exist but inaccessible
│   └── ReferenceError if accessed
├── Function Hoisting
│   ├── Function declarations: hoisted
│   ├── Function expressions: NOT hoisted
│   └── var function expressions: hoisted as undefined
└── Best Practices
    ├── Use let/const instead of var
    ├── Declare variables at top of scope
    ├── Understand TDZ implications
    └── Be aware of hoisting behavior
```

### 🚨 Common Pitfalls

1. **Assuming hoisting means variables are initialized**
2. **Forgetting TDZ exists for let/const**
3. **Mixing function declarations and expressions**
4. **Not understanding scope differences between var/let/const**

### ✅ Best Practices

1. **Always declare variables at the top of their scope**
2. **Use `let` and `const` instead of `var`**
3. **Understand that hoisting doesn't mean "moving code"**
4. **Be explicit about your intentions**

## 🎯 Next Steps

Ready to test your knowledge? Try the practice questions above, then move on to:
- **[Scopes & Closures](./../02-scopes-closures/)**
- **[This Keyword & Context](./../03-this-keyword/)**

---

**💡 Pro Tip:** Understanding hoisting and TDZ is crucial for debugging and writing predictable JavaScript code. These concepts form the foundation for understanding JavaScript's execution context and scope chain.

**🚀 Ready to practice? Copy the code examples into your console and experiment!**
