# 🧬 Prototypal Inheritance

**Level 2: Advanced Patterns** | **Concept 1 of 4**

## 📖 Theory

Prototypal inheritance is JavaScript's fundamental mechanism for sharing properties and methods between objects. Unlike classical inheritance in other languages, JavaScript uses a prototype chain where objects inherit from other objects.

### Key Concepts

- **Prototype Chain**: The mechanism by which objects inherit properties from other objects
- **`__proto__`**: The internal property that points to an object's prototype
- **`Object.create()`**: Method to create objects with a specified prototype
- **`Object.getPrototypeOf()`**: Method to get an object's prototype
- **Constructor Functions**: Functions used to create objects with shared properties

## 💡 Interactive Examples

### 1. Basic Prototype Chain

```javascript
// Create a simple object
const animal = {
  eats: true,
  walk() {
    console.log("Animal walking");
  }
};

// Create a rabbit that inherits from animal
const rabbit = Object.create(animal);
rabbit.jumps = true;

console.log(rabbit.eats); // true (inherited)
console.log(rabbit.jumps); // true (own property)
rabbit.walk(); // "Animal walking" (inherited method)

// Check the prototype chain
console.log(Object.getPrototypeOf(rabbit) === animal); // true
console.log(rabbit.__proto__ === animal); // true (deprecated but still works)
```

### 2. Constructor Functions

```javascript
// Constructor function
function Animal(name) {
  this.name = name;
  this.eats = true;
}

// Add methods to prototype
Animal.prototype.walk = function() {
  console.log(`${this.name} is walking`);
};

Animal.prototype.sleep = function() {
  console.log(`${this.name} is sleeping`);
};

// Create instances
const dog = new Animal("Rex");
const cat = new Animal("Whiskers");

dog.walk(); // "Rex is walking"
cat.sleep(); // "Whiskers is sleeping"

// Check inheritance
console.log(dog.__proto__ === Animal.prototype); // true
console.log(Animal.prototype.__proto__ === Object.prototype); // true
```

### 3. Method Overriding

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.speak = function() {
  console.log("Some animal sound");
};

function Dog(name, breed) {
  Animal.call(this, name); // Call parent constructor
  this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Fix constructor reference

// Override the speak method
Dog.prototype.speak = function() {
  console.log("Woof! Woof!");
};

// Add dog-specific method
Dog.prototype.fetch = function() {
  console.log(`${this.name} is fetching the ball`);
};

const myDog = new Dog("Buddy", "Golden Retriever");
myDog.speak(); // "Woof! Woof!"
myDog.fetch(); // "Buddy is fetching the ball"
console.log(myDog.name); // "Buddy" (inherited)
```

### 4. Modern Class Syntax (ES6+)

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    this.eats = true;
  }
  
  walk() {
    console.log(`${this.name} is walking`);
  }
  
  sleep() {
    console.log(`${this.name} is sleeping`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }
  
  speak() {
    console.log("Woof! Woof!");
  }
  
  fetch() {
    console.log(`${this.name} is fetching the ball`);
  }
}

const dog = new Dog("Max", "Labrador");
dog.walk(); // "Max is walking"
dog.speak(); // "Woof! Woof!"
dog.fetch(); // "Max is fetching the ball"

// Check inheritance
console.log(dog instanceof Dog); // true
console.log(dog instanceof Animal); // true
console.log(dog instanceof Object); // true
```

### 5. Object.create() with Properties

```javascript
const person = {
  greet() {
    console.log(`Hello, I'm ${this.name}`);
  }
};

const john = Object.create(person, {
  name: {
    value: "John",
    writable: true,
    enumerable: true,
    configurable: true
  },
  age: {
    value: 30,
    writable: true,
    enumerable: true,
    configurable: true
  }
});

john.greet(); // "Hello, I'm John"
console.log(john.name); // "John"
console.log(john.age); // 30
```

## ❓ Practice Questions

### Question 1: Prototype Chain Understanding
```javascript
const a = { x: 1 };
const b = Object.create(a);
const c = Object.create(b);

console.log(c.x); // What will this output and why?
```

**Answer**: This will output `1`. The prototype chain works as follows:
- `c` inherits from `b`
- `b` inherits from `a`
- `a` has the property `x: 1`
- When accessing `c.x`, JavaScript looks up the prototype chain and finds `x` in object `a`

### Question 2: Constructor Function Behavior
```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  console.log(`Hello, I'm ${this.name}`);
};

const person1 = new Person("Alice");
const person2 = Person("Bob"); // Missing 'new'

person1.sayHello(); // What happens?
person2.sayHello(); // What happens?
```

**Answer**: 
- `person1.sayHello()` works correctly and outputs "Hello, I'm Alice"
- `person2.sayHello()` throws an error because `person2` is `undefined`
- Without `new`, `Person("Bob")` is called as a regular function, not a constructor
- Regular functions don't return objects, so `person2` becomes `undefined`

### Question 3: Method Overriding
```javascript
const parent = {
  greet() {
    console.log("Hello from parent");
  }
};

const child = Object.create(parent);
child.greet = function() {
  console.log("Hello from child");
};

child.greet(); // What will this output?
```

**Answer**: This will output "Hello from child". The child object has its own `greet` method that overrides the inherited one from the parent. JavaScript first looks for properties on the object itself before checking the prototype chain.

## 🔍 Deep Dive

### 1. Prototype Pollution Prevention

```javascript
// Dangerous: Modifying Object.prototype
Object.prototype.customMethod = function() {
  console.log("This affects ALL objects!");
};

// Safe: Use Object.create(null) for pure objects
const pureObject = Object.create(null);
console.log(pureObject.hasOwnProperty); // undefined (no inherited methods)

// Safe: Check before adding to prototypes
if (!Object.prototype.customMethod) {
  Object.prototype.customMethod = function() {
    console.log("Safe addition");
  };
}
```

### 2. Multiple Inheritance Simulation

```javascript
function mixin(target, ...sources) {
  sources.forEach(source => {
    Object.getOwnPropertyNames(source).forEach(name => {
      Object.defineProperty(target, name, 
        Object.getOwnPropertyDescriptor(source, name));
    });
  });
  return target;
}

const canSwim = {
  swim() {
    console.log("Swimming...");
  }
};

const canFly = {
  fly() {
    console.log("Flying...");
  }
};

const duck = mixin({}, canSwim, canFly);
duck.swim(); // "Swimming..."
duck.fly();  // "Flying..."
```

### 3. Performance Considerations

```javascript
// Slow: Accessing properties deep in prototype chain
function slowAccess() {
  const obj = Object.create(Object.create(Object.create({ x: 1 })));
  for (let i = 0; i < 1000000; i++) {
    obj.x; // Has to traverse 3 levels
  }
}

// Fast: Direct property access
function fastAccess() {
  const obj = { x: 1 };
  for (let i = 0; i < 1000000; i++) {
    obj.x; // Direct access
  }
}

// Use performance.now() to measure the difference
```

## 📝 Summary

### Key Takeaways

1. **Prototypal inheritance** is JavaScript's native inheritance mechanism
2. **`Object.create()`** is the modern way to set up inheritance
3. **Constructor functions** use the `new` keyword and prototype property
4. **ES6 classes** are syntactic sugar over prototypal inheritance
5. **Method overriding** works by defining properties on the object itself
6. **Prototype chain** can be traversed using `Object.getPrototypeOf()`

### Mind Map

```
Prototypal Inheritance
├── Prototype Chain
│   ├── __proto__ (deprecated)
│   ├── Object.getPrototypeOf()
│   └── Object.setPrototypeOf()
├── Object.create()
│   ├── Pure objects (null prototype)
│   └── Property descriptors
├── Constructor Functions
│   ├── new keyword
│   ├── prototype property
│   └── instanceof operator
├── ES6 Classes
│   ├── extends keyword
│   ├── super() call
│   └── static methods
└── Best Practices
    ├── Avoid prototype pollution
    ├── Use Object.create() over __proto__
    └── Consider performance implications
```

### Next Steps

Ready to move to the next concept? Continue to:
**[Object Proxies & Reflect](./06-object-proxies/)** → Learn how to intercept and customize object operations

---

**💡 Pro Tip**: Always use `Object.create()` instead of directly manipulating `__proto__` for better compatibility and cleaner code.

