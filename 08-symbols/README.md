# 🔯 Symbols & Well-known Symbols

**Level 2: Advanced Patterns** | **Concept 4 of 4**

## 📖 Theory

Symbols are a primitive data type introduced in ES6 that serve as unique identifiers. They are guaranteed to be unique and are commonly used as property keys to avoid naming conflicts. Well-known symbols are built-in symbols that define how objects behave in certain situations.

### Key Concepts

- **Symbol**: A unique, immutable primitive value that can be used as an object property key
- **Symbol()**: Function that creates a new unique symbol
- **Symbol.for()**: Function that creates or retrieves a symbol from the global symbol registry
- **Well-known Symbols**: Built-in symbols that control object behavior (e.g., `Symbol.iterator`, `Symbol.toStringTag`)
- **Symbol Registry**: Global registry for sharing symbols across different parts of code

## 💡 Interactive Examples

### 1. Basic Symbol Creation

```javascript
// Create unique symbols
const sym1 = Symbol();
const sym2 = Symbol();
const sym3 = Symbol('description'); // Symbol with description

console.log(sym1 === sym2); // false (each symbol is unique)
console.log(sym1 === sym1); // true (same symbol reference)
console.log(sym3.toString()); // "Symbol(description)"
console.log(sym3.description); // "description"

// Symbols as object properties
const obj = {
  [sym1]: 'value1',
  [sym2]: 'value2',
  regularProperty: 'regular value'
};

console.log(obj[sym1]); // "value1"
console.log(obj[sym2]); // "value2"
console.log(obj.regularProperty); // "regular value"

// Symbols are not enumerable in for...in loops
for (const key in obj) {
  console.log(key); // Only "regularProperty" (symbols are hidden)
}

// But they are accessible via Object.getOwnPropertySymbols
const symbols = Object.getOwnPropertySymbols(obj);
console.log(symbols); // [Symbol(), Symbol()]
```

### 2. Symbol Registry

```javascript
// Create or retrieve symbols from global registry
const globalSym1 = Symbol.for('shared');
const globalSym2 = Symbol.for('shared');
const globalSym3 = Symbol.for('another');

console.log(globalSym1 === globalSym2); // true (same symbol from registry)
console.log(globalSym1 === globalSym3); // false (different symbols)

// Check if a symbol exists in registry
console.log(Symbol.keyFor(globalSym1)); // "shared"
console.log(Symbol.keyFor(globalSym2)); // "shared"
console.log(Symbol.keyFor(globalSym3)); // "another"

// Local symbols are not in the registry
const localSym = Symbol('local');
console.log(Symbol.keyFor(localSym)); // undefined

// Registry is global across different modules/contexts
const sameSymbol = Symbol.for('shared');
console.log(sameSymbol === globalSym1); // true
```

### 3. Well-known Symbols: Symbol.iterator

```javascript
// Custom iterable object using Symbol.iterator
const customCollection = {
  data: [1, 2, 3, 4, 5],
  
  [Symbol.iterator]() {
    let index = 0;
    return {
      next() {
        if (index < customCollection.data.length) {
          return { value: customCollection.data[index++], done: false };
        }
        return { done: true };
      }
    };
  }
};

// Use with for...of loop
for (const item of customCollection) {
  console.log(item); // 1, 2, 3, 4, 5
}

// Manual iteration
const iterator = customCollection[Symbol.iterator]();
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
```

### 4. Well-known Symbols: Symbol.toStringTag

```javascript
class CustomArray {
  constructor(...items) {
    this.items = items;
  }
  
  get [Symbol.toStringTag]() {
    return 'CustomArray';
  }
  
  toString() {
    return `[${this.items.join(', ')}]`;
  }
}

const customArr = new CustomArray(1, 2, 3);

console.log(customArr.toString()); // "[1, 2, 3]"
console.log(Object.prototype.toString.call(customArr)); // "[object CustomArray]"

// Without Symbol.toStringTag
class RegularArray {
  constructor(...items) {
    this.items = items;
  }
}

const regularArr = new RegularArray(1, 2, 3);
console.log(Object.prototype.toString.call(regularArr)); // "[object Object]"
```

### 5. Well-known Symbols: Symbol.toPrimitive

```javascript
class Temperature {
  constructor(celsius) {
    this.celsius = celsius;
  }
  
  [Symbol.toPrimitive](hint) {
    switch (hint) {
      case 'string':
        return `${this.celsius}°C`;
      case 'number':
        return this.celsius;
      case 'default':
        return this.celsius + 273.15; // Convert to Kelvin
    }
  }
}

const temp = new Temperature(25);

console.log(String(temp)); // "25°C" (string hint)
console.log(+temp); // 25 (number hint)
console.log(temp + 0); // 298.15 (default hint - addition)
console.log(`${temp}`); // "25°C" (string hint)
```

### 6. Well-known Symbols: Symbol.species

```javascript
class CustomArray extends Array {
  // Override the constructor used by Array methods
  static get [Symbol.species]() {
    return Array; // Use regular Array instead of CustomArray
  }
}

const customArr = new CustomArray(1, 2, 3);
const mapped = customArr.map(x => x * 2);

console.log(mapped instanceof CustomArray); // false
console.log(mapped instanceof Array); // true
console.log(mapped.constructor === Array); // true

// Without Symbol.species override
class AnotherCustomArray extends Array {
  // Inherits Symbol.species from Array (returns this constructor)
}

const anotherArr = new AnotherCustomArray(1, 2, 3);
const anotherMapped = anotherArr.map(x => x * 2);

console.log(anotherMapped instanceof AnotherCustomArray); // true
console.log(anotherMapped instanceof Array); // true
```

### 7. Well-known Symbols: Symbol.split, Symbol.match, Symbol.replace

```javascript
class CustomString {
  constructor(value) {
    this.value = value;
  }
  
  [Symbol.split](separator) {
    console.log(`Splitting "${this.value}" by "${separator}"`);
    return this.value.split(separator);
  }
  
  [Symbol.match](regexp) {
    console.log(`Matching "${this.value}" against regexp`);
    return this.value.match(regexp);
  }
  
  [Symbol.replace](searchValue, replaceValue) {
    console.log(`Replacing "${searchValue}" with "${replaceValue}" in "${this.value}"`);
    return this.value.replace(searchValue, replaceValue);
  }
}

const customStr = new CustomString('hello world');

// These operations now use our custom methods
customStr.split(' '); // Logs: "Splitting "hello world" by " ""
customStr.match(/world/); // Logs: "Matching "hello world" against regexp"
customStr.replace('world', 'JavaScript'); // Logs: "Replacing "world" with "JavaScript" in "hello world""
```

## ❓ Practice Questions

### Question 1: Symbol Uniqueness
```javascript
const sym1 = Symbol('test');
const sym2 = Symbol('test');
const sym3 = Symbol.for('test');
const sym4 = Symbol.for('test');

console.log(sym1 === sym2); // What will this output?
console.log(sym3 === sym4); // What will this output?
console.log(sym1 === sym3); // What will this output?
```

**Answer**: 
- `sym1 === sym2` outputs `false` because each call to `Symbol()` creates a unique symbol, even with the same description
- `sym3 === sym4` outputs `true` because `Symbol.for()` returns the same symbol from the global registry when given the same key
- `sym1 === sym3` outputs `false` because `sym1` is a local symbol and `sym3` is from the registry - they are completely different symbols

### Question 2: Symbol Property Access
```javascript
const sym = Symbol('mySymbol');
const obj = {
  [sym]: 'symbol value',
  regularProp: 'regular value'
};

console.log(obj[sym]); // What will this output?
console.log(obj['mySymbol']); // What will this output?
console.log(obj.regularProp); // What will this output?
```

**Answer**: 
- `obj[sym]` outputs `"symbol value"` because we're accessing the property using the actual symbol reference
- `obj['mySymbol']` outputs `undefined` because `'mySymbol'` is just a string, not the symbol key
- `obj.regularProp` outputs `"regular value"` because this is a regular string property

### Question 3: Symbol.iterator Behavior
```javascript
const obj = {
  data: ['a', 'b', 'c'],
  [Symbol.iterator]() {
    return this.data[Symbol.iterator]();
  }
};

for (const item of obj) {
  console.log(item);
}
// What will be the output and why?
```

**Answer**: The output will be `"a", "b", "c"`. The `Symbol.iterator` method returns the iterator from the `data` array, which allows the `for...of` loop to iterate over the array elements. This is a common pattern for delegating iteration to an internal data structure.

## 🔍 Deep Dive

### 1. Advanced Symbol Usage Patterns

```javascript
// Private properties using symbols
const _privateData = Symbol('privateData');
const _privateMethod = Symbol('privateMethod');

class Example {
  constructor() {
    this[_privateData] = 'secret';
  }
  
  [_privateMethod]() {
    return this[_privateData];
  }
  
  publicMethod() {
    return this[_privateMethod]();
  }
}

const example = new Example();
console.log(example.publicMethod()); // "secret"

// Symbols are not completely private, but they provide a level of encapsulation
console.log(example[_privateData]); // "secret" (if you have access to the symbol)
console.log(Object.getOwnPropertySymbols(example)); // [Symbol(privateData), Symbol(privateMethod)]

// Symbol-based event system
class EventEmitter {
  constructor() {
    this[Symbol.for('events')] = new Map();
  }
  
  on(event, handler) {
    if (!this[Symbol.for('events')].has(event)) {
      this[Symbol.for('events')].set(event, []);
    }
    this[Symbol.for('events')].get(event).push(handler);
  }
  
  emit(event, ...args) {
    if (this[Symbol.for('events')].has(event)) {
      this[Symbol.for('events')].get(event).forEach(handler => handler(...args));
    }
  }
}

const emitter = new EventEmitter();
emitter.on('message', (msg) => console.log('Received:', msg));
emitter.emit('message', 'Hello World'); // "Received: Hello World"
```

### 2. Custom Well-known Symbol Implementations

```javascript
// Custom Symbol.asyncIterator for async iteration
class AsyncDataStream {
  constructor(data) {
    this.data = data;
    this.index = 0;
  }
  
  async *[Symbol.asyncIterator]() {
    while (this.index < this.data.length) {
      // Simulate async operation
      await new Promise(resolve => setTimeout(resolve, 100));
      yield this.data[this.index++];
    }
  }
}

// Custom Symbol.hasInstance for instanceof behavior
class SpecialArray {
  static [Symbol.hasInstance](instance) {
    return Array.isArray(instance) && instance.length > 0;
  }
}

console.log([] instanceof SpecialArray); // false (empty array)
console.log([1, 2, 3] instanceof SpecialArray); // true (non-empty array)
console.log('string' instanceof SpecialArray); // false (not an array)

// Custom Symbol.unscopables to control with statement behavior
const obj = {
  foo: 'bar',
  baz: 'qux',
  [Symbol.unscopables]: {
    foo: false, // foo can be used in with statement
    baz: true   // baz cannot be used in with statement
  }
};

with (obj) {
  console.log(foo); // "bar" (works)
  // console.log(baz); // ReferenceError: baz is not defined
}
```

### 3. Symbol-based Metaprogramming

```javascript
// Symbol-based method delegation
const methods = {
  [Symbol.for('add')](a, b) { return a + b; },
  [Symbol.for('multiply')](a, b) { return a * b; },
  [Symbol.for('divide')](a, b) { return a / b; }
};

class Calculator {
  constructor() {
    // Delegate method calls to the methods object
    Object.getOwnPropertySymbols(methods).forEach(sym => {
      this[sym] = methods[sym];
    });
  }
}

const calc = new Calculator();
console.log(calc[Symbol.for('add')](5, 3)); // 8
console.log(calc[Symbol.for('multiply')](4, 6)); // 24

// Symbol-based plugin system
class PluginHost {
  constructor() {
    this[Symbol.for('plugins')] = new Map();
  }
  
  register(name, plugin) {
    this[Symbol.for('plugins')].set(name, plugin);
  }
  
  execute(name, ...args) {
    const plugin = this[Symbol.for('plugins')].get(name);
    if (plugin) {
      return plugin(...args);
    }
    throw new Error(`Plugin '${name}' not found`);
  }
}

const host = new PluginHost();
host.register('greet', (name) => `Hello, ${name}!`);
host.register('calculate', (a, b) => a + b);

console.log(host.execute('greet', 'World')); // "Hello, World!"
console.log(host.execute('calculate', 10, 20)); // 30
```

### 4. Performance and Memory Considerations

```javascript
// Symbol creation performance
function measureSymbolCreation() {
  const start = performance.now();
  
  for (let i = 0; i < 1000000; i++) {
    Symbol(`symbol_${i}`);
  }
  
  const end = performance.now();
  console.log(`Created 1M symbols in ${end - start}ms`);
}

// Symbol registry lookup performance
function measureSymbolLookup() {
  // Pre-create symbols
  const symbols = [];
  for (let i = 0; i < 10000; i++) {
    symbols.push(Symbol.for(`key_${i}`));
  }
  
  const start = performance.now();
  
  for (let i = 0; i < 1000000; i++) {
    Symbol.for(`key_${i % 10000}`);
  }
  
  const end = performance.now();
  console.log(`Looked up 1M symbols in ${end - start}ms`);
}

// Memory usage comparison
function compareMemoryUsage() {
  const stringKeys = {};
  const symbolKeys = {};
  
  for (let i = 0; i < 10000; i++) {
    stringKeys[`key_${i}`] = i;
    symbolKeys[Symbol(`key_${i}`)] = i;
  }
  
  console.log('String keys object size:', JSON.stringify(stringKeys).length);
  console.log('Symbol keys object size:', Object.keys(symbolKeys).length);
}

// measureSymbolCreation();
// measureSymbolLookup();
// compareMemoryUsage();
```

## 📝 Summary

### Key Takeaways

1. **Symbols** are unique, immutable primitive values used as property keys
2. **Symbol()** creates unique symbols, while **Symbol.for()** uses a global registry
3. **Well-known symbols** control object behavior in specific situations
4. **Symbol.iterator** enables custom iteration behavior
5. **Symbol.toStringTag** customizes object string representation
6. **Symbols provide encapsulation** and help avoid naming conflicts

### Mind Map

```
Symbols & Well-known Symbols
├── Symbol Creation
│   ├── Symbol() - Unique symbols
│   ├── Symbol.for() - Registry symbols
│   └── Symbol.keyFor() - Registry lookup
├── Well-known Symbols
│   ├── Symbol.iterator - Iteration
│   ├── Symbol.toStringTag - String representation
│   ├── Symbol.toPrimitive - Type conversion
│   ├── Symbol.species - Constructor selection
│   ├── Symbol.split/match/replace - String operations
│   └── Symbol.asyncIterator - Async iteration
├── Use Cases
│   ├── Private properties
│   ├── Custom iteration
│   ├── Metaprogramming
│   ├── Plugin systems
│   └── Avoiding conflicts
├── Advanced Features
│   ├── Symbol.hasInstance
│   ├── Symbol.unscopables
│   ├── Symbol registry
│   └── Performance considerations
└── Best Practices
    ├── Use for private properties
    ├── Leverage well-known symbols
    ├── Consider performance impact
    └── Document symbol usage
```

### Next Steps

Congratulations! You've completed **Level 2: Advanced Patterns**! 🎉

Ready to move to the next level? Continue to:
**[Level 3: Async & Performance](./09-promises/)** → Learn about Promises, Async/Await, and advanced asynchronous programming patterns

---

**💡 Pro Tip**: Use symbols for properties that should be "private" or when you need to avoid naming conflicts. Well-known symbols are powerful tools for customizing object behavior in ways that integrate seamlessly with JavaScript's built-in features.

