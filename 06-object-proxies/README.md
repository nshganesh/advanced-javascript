# 🎭 Object Proxies & Reflect

**Level 2: Advanced Patterns** | **Concept 2 of 4**

## 📖 Theory

Object Proxies and the Reflect API are powerful ES6+ features that allow you to intercept and customize fundamental operations on objects. Proxies act as "middleware" that can intercept operations like property access, assignment, function calls, and more.

### Key Concepts

- **Proxy**: A wrapper object that intercepts operations on the target object
- **Handler/Trap**: Functions that define the behavior when operations are intercepted
- **Reflect**: Built-in object that provides methods for interceptable JavaScript operations
- **Revocable Proxy**: A proxy that can be revoked, making it unusable
- **Meta-programming**: Writing code that manipulates other code

## 💡 Interactive Examples

### 1. Basic Property Interception

```javascript
// Target object
const user = {
  name: "John",
  age: 30
};

// Handler with traps
const handler = {
  get(target, property, receiver) {
    console.log(`Getting property: ${property}`);
    return target[property];
  },
  
  set(target, property, value, receiver) {
    console.log(`Setting property: ${property} = ${value}`);
    target[property] = value;
    return true; // Must return true for successful assignment
  }
};

// Create proxy
const userProxy = new Proxy(user, handler);

// Intercepted operations
console.log(userProxy.name); // "Getting property: name" then "John"
userProxy.age = 31; // "Setting property: age = 31"
console.log(userProxy.age); // "Getting property: age" then "31"
```

### 2. Validation Proxy

```javascript
const user = {
  name: "Alice",
  age: 25
};

const validationHandler = {
  set(target, property, value) {
    if (property === 'age') {
      if (typeof value !== 'number') {
        throw new TypeError('Age must be a number');
      }
      if (value < 0 || value > 150) {
        throw new RangeError('Age must be between 0 and 150');
      }
    }
    
    if (property === 'name') {
      if (typeof value !== 'string' || value.length < 2) {
        throw new TypeError('Name must be a string with at least 2 characters');
      }
    }
    
    target[property] = value;
    return true;
  }
};

const validatedUser = new Proxy(user, validationHandler);

// Valid assignments
validatedUser.name = "Bob"; // Works
validatedUser.age = 30; // Works

// Invalid assignments
try {
  validatedUser.age = "thirty"; // Throws TypeError
} catch (error) {
  console.log(error.message); // "Age must be a number"
}

try {
  validatedUser.age = 200; // Throws RangeError
} catch (error) {
  console.log(error.message); // "Age must be between 0 and 150"
}
```

### 3. Logging Proxy

```javascript
const calculator = {
  add(a, b) {
    return a + b;
  },
  multiply(a, b) {
    return a * b;
  }
};

const loggingHandler = {
  get(target, property, receiver) {
    const value = target[property];
    
    if (typeof value === 'function') {
      return function(...args) {
        console.log(`Calling ${property} with arguments:`, args);
        const result = value.apply(this, args);
        console.log(`Result: ${result}`);
        return result;
      };
    }
    
    return value;
  }
};

const loggedCalculator = new Proxy(calculator, loggingHandler);

loggedCalculator.add(5, 3); // Logs: "Calling add with arguments: [5, 3]" then "Result: 8"
loggedCalculator.multiply(4, 6); // Logs: "Calling multiply with arguments: [4, 6]" then "Result: 24"
```

### 4. Default Values Proxy

```javascript
const handler = {
  get(target, property, receiver) {
    if (property in target) {
      return target[property];
    }
    
    // Return default values for missing properties
    if (property === 'toString') {
      return () => '[Object DefaultValues]';
    }
    
    if (property === 'valueOf') {
      return () => target;
    }
    
    // Return 0 for numeric properties, empty string for string properties
    if (typeof property === 'string' && /^\d+$/.test(property)) {
      return 0;
    }
    
    return '';
  }
};

const obj = { name: "John" };
const defaultObj = new Proxy(obj, handler);

console.log(defaultObj.name); // "John" (existing property)
console.log(defaultObj.age); // "" (default for missing string property)
console.log(defaultObj[0]); // 0 (default for missing numeric property)
console.log(defaultObj.toString()); // "[Object DefaultValues]"
```

### 5. Revocable Proxy

```javascript
const user = { name: "Alice", email: "alice@example.com" };

const { proxy, revoke } = Proxy.revocable(user, {
  get(target, property) {
    if (property === 'email') {
      return '***@***.***'; // Hide email
    }
    return target[property];
  }
});

console.log(proxy.name); // "Alice"
console.log(proxy.email); // "***@***.***"

// Revoke the proxy
revoke();

try {
  console.log(proxy.name); // Throws TypeError: Cannot perform 'get' on a proxy that has been revoked
} catch (error) {
  console.log('Proxy has been revoked:', error.message);
}
```

### 6. Reflect API Usage

```javascript
const user = {
  name: "Bob",
  age: 28,
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

// Using Reflect instead of direct operations
console.log(Reflect.get(user, 'name')); // "Bob"
console.log(Reflect.has(user, 'age')); // true
console.log(Reflect.ownKeys(user)); // ["name", "age", "greet"]

// Set property
Reflect.set(user, 'city', 'New York');
console.log(user.city); // "New York"

// Call method
const greeting = Reflect.apply(user.greet, user, []);
console.log(greeting); // "Hello, I'm Bob"

// Create object
const newUser = Reflect.construct(Object, []);
console.log(newUser); // {}
```

## ❓ Practice Questions

### Question 1: Proxy Trap Behavior
```javascript
const obj = { x: 1, y: 2 };
const proxy = new Proxy(obj, {
  get(target, property) {
    if (property === 'z') {
      return target.x + target.y;
    }
    return target[property];
  }
});

console.log(proxy.z); // What will this output and why?
```

**Answer**: This will output `3`. The proxy intercepts the `get` operation for property `z`. Since `z` doesn't exist on the target object, the trap returns the sum of `target.x` (which is 1) and `target.y` (which is 2), resulting in 3.

### Question 2: Set Trap Return Value
```javascript
const obj = {};
const proxy = new Proxy(obj, {
  set(target, property, value) {
    target[property] = value;
    return false; // What happens with false?
  }
});

proxy.x = 10; // What happens?
console.log(proxy.x); // What will this output?
```

**Answer**: 
- `proxy.x = 10` will throw a TypeError because the `set` trap returned `false`
- When a `set` trap returns `false` (or any falsy value), it indicates that the assignment failed
- `console.log(proxy.x)` will output `undefined` because the assignment never completed

### Question 3: Proxy with Function Calls
```javascript
const fn = function(a, b) { return a + b; };
const proxy = new Proxy(fn, {
  apply(target, thisArg, argumentsList) {
    console.log('Function called with:', argumentsList);
    return target.apply(thisArg, argumentsList);
  }
});

const result = proxy(5, 3); // What will be logged and what will result be?
```

**Answer**: 
- The console will log: "Function called with: [5, 3]"
- `result` will be `8` (5 + 3)
- The `apply` trap intercepts function calls and logs the arguments before executing the original function

## 🔍 Deep Dive

### 1. Advanced Property Validation

```javascript
const schema = {
  name: { type: 'string', required: true, minLength: 2 },
  age: { type: 'number', required: true, min: 0, max: 150 },
  email: { type: 'string', pattern: /^[^\s@]+@[^\s@]+\.[^\s@]+$/ }
};

function createValidatedObject(schema) {
  return new Proxy({}, {
    set(target, property, value) {
      const fieldSchema = schema[property];
      
      if (!fieldSchema) {
        throw new Error(`Unknown property: ${property}`);
      }
      
      if (fieldSchema.required && value === undefined) {
        throw new Error(`${property} is required`);
      }
      
      if (value !== undefined) {
        if (typeof value !== fieldSchema.type) {
          throw new TypeError(`${property} must be a ${fieldSchema.type}`);
        }
        
        if (fieldSchema.minLength && value.length < fieldSchema.minLength) {
          throw new Error(`${property} must be at least ${fieldSchema.minLength} characters`);
        }
        
        if (fieldSchema.min !== undefined && value < fieldSchema.min) {
          throw new Error(`${property} must be at least ${fieldSchema.min}`);
        }
        
        if (fieldSchema.max !== undefined && value > fieldSchema.max) {
          throw new Error(`${property} must be at most ${fieldSchema.max}`);
        }
        
        if (fieldSchema.pattern && !fieldSchema.pattern.test(value)) {
          throw new Error(`${property} format is invalid`);
        }
      }
      
      target[property] = value;
      return true;
    }
  });
}

const user = createValidatedObject(schema);
user.name = "John"; // Works
user.age = 25; // Works
user.email = "john@example.com"; // Works

try {
  user.age = "twenty-five"; // Throws TypeError
} catch (error) {
  console.log(error.message);
}
```

### 2. Immutable Object Proxy

```javascript
function createImmutableObject(obj) {
  return new Proxy(obj, {
    set(target, property, value) {
      throw new Error(`Cannot modify property '${property}' on immutable object`);
    },
    
    deleteProperty(target, property) {
      throw new Error(`Cannot delete property '${property}' on immutable object`);
    },
    
    defineProperty(target, property, descriptor) {
      throw new Error(`Cannot define property '${property}' on immutable object`);
    }
  });
}

const immutableUser = createImmutableObject({
  name: "Alice",
  age: 30
});

console.log(immutableUser.name); // "Alice" (reading works)

try {
  immutableUser.age = 31; // Throws Error: Cannot modify property 'age' on immutable object
} catch (error) {
  console.log(error.message);
}
```

### 3. Performance Monitoring Proxy

```javascript
function createPerformanceProxy(obj, name = 'Object') {
  const metrics = {
    reads: 0,
    writes: 0,
    calls: 0,
    startTime: Date.now()
  };
  
  return new Proxy(obj, {
    get(target, property, receiver) {
      if (property === 'getMetrics') {
        return () => ({
          ...metrics,
          duration: Date.now() - metrics.startTime
        });
      }
      
      metrics.reads++;
      return target[property];
    },
    
    set(target, property, value, receiver) {
      metrics.writes++;
      target[property] = value;
      return true;
    },
    
    apply(target, thisArg, argumentsList) {
      metrics.calls++;
      return target.apply(thisArg, argumentsList);
    }
  });
}

const calculator = createPerformanceProxy({
  add(a, b) { return a + b; },
  multiply(a, b) { return a * b; }
}, 'Calculator');

calculator.add(5, 3);
calculator.multiply(4, 6);
calculator.result = 42;

console.log(calculator.getMetrics());
// Output: { reads: 1, writes: 1, calls: 2, startTime: ..., duration: ... }
```

## 📝 Summary

### Key Takeaways

1. **Proxies** intercept and customize fundamental object operations
2. **Traps** are handler methods that define proxy behavior
3. **Reflect API** provides methods for all proxy operations
4. **Revocable proxies** can be disabled when no longer needed
5. **Common use cases** include validation, logging, and meta-programming
6. **Performance impact** should be considered for frequently accessed properties

### Mind Map

```
Object Proxies & Reflect
├── Proxy Creation
│   ├── new Proxy(target, handler)
│   ├── Proxy.revocable(target, handler)
│   └── Handler object with traps
├── Common Traps
│   ├── get(target, property, receiver)
│   ├── set(target, property, value, receiver)
│   ├── apply(target, thisArg, argumentsList)
│   ├── construct(target, argumentsList, newTarget)
│   └── has(target, property)
├── Reflect API
│   ├── Reflect.get()
│   ├── Reflect.set()
│   ├── Reflect.apply()
│   ├── Reflect.construct()
│   └── Reflect.ownKeys()
├── Use Cases
│   ├── Property validation
│   ├── Logging and debugging
│   ├── Default values
│   ├── Immutability
│   └── Performance monitoring
└── Best Practices
    ├── Return appropriate values from traps
    ├── Consider performance implications
    ├── Use Reflect for consistency
    └── Handle edge cases properly
```

### Next Steps

Ready to move to the next concept? Continue to:
**[Iterators & Generators](./07-iterators-generators/)** → Learn how to create custom iteration behavior and lazy evaluation

---

**💡 Pro Tip**: Use Reflect API methods in your proxy traps for consistency and to ensure you're handling all the edge cases correctly.

