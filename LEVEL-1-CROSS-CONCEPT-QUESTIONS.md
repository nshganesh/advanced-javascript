# 🎯 Level 1 Cross-Concept Interview Questions

## 🚨 **CRITICAL: These questions test MULTIPLE concepts simultaneously**

Each question combines 2-4 Level 1 concepts to simulate real-world complexity and interview scenarios.

---

## 🔥 **Question Set 1: Hoisting + Scopes + Closures**

### Q1: The Infamous Loop Closure Problem
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```
**What will be logged and why?**
- Tests: Hoisting, Scopes, Closures, Event Loop
- **Answer**: All logs will show `3` due to closure capturing the reference to `i`, not the value

### Q2: Let vs Var in Loops with Closures
```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```
**What will be logged and why?**
- Tests: Block scope, Temporal Dead Zone, Closures, Event Loop
- **Answer**: `0, 1, 2` because `let` creates block scope for each iteration

### Q3: Function Declaration vs Expression in Closures
```javascript
var x = 10;

function outer() {
  var x = 20;
  
  function inner() {
    console.log(x);
  }
  
  return inner;
}

var fn = outer();
fn();
```
**What will be logged and why?**
- Tests: Hoisting, Scopes, Closures, Lexical scoping
- **Answer**: `20` due to lexical scoping and closure capturing the `x` from `outer`'s scope

---

## 🔥 **Question Set 2: This + Scopes + Closures**

### Q4: This Context Loss in Closures
```javascript
var name = "Global";

var obj = {
  name: "Object",
  getName: function() {
    return function() {
      return this.name;
    };
  }
};

console.log(obj.getName()());
```
**What will be logged and why?**
- Tests: This keyword, Context loss, Closures, Global scope
- **Answer**: `"Global"` because the returned function loses its `this` context and defaults to global

### Q5: Arrow Functions Preserving This
```javascript
var name = "Global";

var obj = {
  name: "Object",
  getName: function() {
    return () => {
      return this.name;
    };
  }
};

console.log(obj.getName()());
```
**What will be logged and why?**
- Tests: Arrow functions, This binding, Closures, Lexical scoping
- **Answer**: `"Object"` because arrow functions preserve the `this` context from their enclosing scope

### Q6: Method Assignment Context Loss
```javascript
var name = "Global";

var obj = {
  name: "Object",
  getName: function() {
    return this.name;
  }
};

var getNameFn = obj.getName;
console.log(getNameFn());
```
**What will be logged and why?**
- Tests: This keyword, Context loss, Method context, Global scope
- **Answer**: `"Global"` because `getNameFn` is called in global context, losing the object context

---

## 🔥 **Question Set 3: Event Loop + Closures + Scopes**

### Q7: Async Closures with Var
```javascript
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```
**What will be logged and when?**
- Tests: Event Loop, Closures, IIFE, Scopes, setTimeout
- **Answer**: `0` immediately, `1` after 1 second, `2` after 2 seconds due to IIFE creating new scope for each iteration

### Q8: Promise Microtasks vs setTimeout
```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
}, 0);

Promise.resolve().then(() => {
  console.log('3');
});

console.log('4');
```
**What will be the output order and why?**
- Tests: Event Loop, Microtasks, Macrotasks, Call Stack
- **Answer**: `1, 4, 3, 2` because microtasks (Promises) have priority over macrotasks (setTimeout)

---

## 🔥 **Question Set 4: All Concepts Combined**

### Q9: The Ultimate Challenge
```javascript
var name = "Global";
var counter = 0;

function createCounter() {
  var counter = 0;
  
  return {
    increment: function() {
      counter++;
      return counter;
    },
    getValue: function() {
      return counter;
    }
  };
}

var counter1 = createCounter();
var counter2 = createCounter();

console.log(counter1.increment()); // ?
console.log(counter1.increment()); // ?
console.log(counter2.increment()); // ?
console.log(counter1.getValue()); // ?
console.log(counter2.getValue()); // ?
```
**What will be logged and why?**
- Tests: ALL Level 1 concepts combined
- **Answer**: `1, 2, 1, 2, 1` due to closures creating separate counter variables for each instance

### Q10: Complex Scope Chain
```javascript
var x = 1;

function outer() {
  var x = 2;
  
  function inner() {
    var x = 3;
    
    setTimeout(function() {
      console.log(x);
    }, 1000);
  }
  
  inner();
}

outer();
```
**What will be logged and why?**
- Tests: Scope chain, Closures, Event Loop, Variable shadowing
- **Answer**: `3` because the closure captures the `x` from the `inner` function's scope

---

## 🎯 **Interview Question Types**

### **Type 1: Code Output Prediction**
- Given code, predict the output
- Explain the reasoning step by step
- Identify which concepts are at play

### **Type 2: Bug Identification**
- Find bugs in given code
- Explain why they occur
- Provide solutions

### **Type 3: Code Writing**
- Write code that demonstrates specific concept interactions
- Implement solutions to common problems
- Show understanding of best practices

### **Type 4: Concept Explanation**
- Explain how multiple concepts work together
- Describe real-world scenarios
- Discuss trade-offs and alternatives

---

## 🚀 **Practice Strategy**

1. **Start Simple**: Begin with questions combining 2 concepts
2. **Build Complexity**: Gradually add more concepts
3. **Time Yourself**: Practice under interview conditions
4. **Explain Aloud**: Verbalize your reasoning process
5. **Draw Diagrams**: Visualize scope chains and execution flow
6. **Multiple Solutions**: Find different ways to solve the same problem

---