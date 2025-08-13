# 🔄 Iterators & Generators

**Level 2: Advanced Patterns** | **Concept 3 of 4**

## 📖 Theory

Iterators and Generators are powerful ES6+ features that provide a standardized way to iterate over data structures and create custom iteration behavior. They enable lazy evaluation, memory-efficient processing, and custom iteration logic.

### Key Concepts

- **Iterator**: An object that provides a way to access elements sequentially
- **Iterable**: An object that can be iterated over (has a `Symbol.iterator` method)
- **Generator**: A special function that can pause and resume execution
- **Yield**: Keyword used in generators to pause execution and return values
- **Lazy Evaluation**: Computing values only when needed, not all at once

## 💡 Interactive Examples

### 1. Basic Iterator Implementation

```javascript
// Custom iterator for a range of numbers
const range = {
  start: 1,
  end: 5,
  
  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;
    
    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        } else {
          return { done: true };
        }
      }
    };
  }
};

// Use with for...of loop
for (const num of range) {
  console.log(num); // 1, 2, 3, 4, 5
}

// Manual iteration
const iterator = range[Symbol.iterator]();
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: 4, done: false }
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator.next()); // { done: true }
```

### 2. Generator Function Basics

```javascript
// Basic generator function
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
}

const gen = numberGenerator();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: 4, done: false }
console.log(gen.next()); // { value: 5, done: false }
console.log(gen.next()); // { done: true }

// Use with for...of loop
for (const num of numberGenerator()) {
  console.log(num); // 1, 2, 3, 4, 5
}
```

### 3. Generator with Parameters and Logic

```javascript
function* fibonacci() {
  let [prev, curr] = [0, 1];
  
  while (true) {
    yield curr;
    [prev, curr] = [curr, prev + curr];
  }
}

const fib = fibonacci();

// Get first 10 Fibonacci numbers
for (let i = 0; i < 10; i++) {
  console.log(fib.next().value);
}
// Output: 1, 1, 2, 3, 5, 8, 13, 21, 34, 55

// Generator with parameters
function* rangeGenerator(start, end, step = 1) {
  for (let i = start; i <= end; i += step) {
    yield i;
  }
}

for (const num of rangeGenerator(0, 10, 2)) {
  console.log(num); // 0, 2, 4, 6, 8, 10
}
```

### 4. Custom Iterable Object

```javascript
class BookCollection {
  constructor(books) {
    this.books = books;
  }
  
  *[Symbol.iterator]() {
    for (const book of this.books) {
      yield book;
    }
  }
  
  // Custom iterator method
  *filterByGenre(genre) {
    for (const book of this.books) {
      if (book.genre === genre) {
        yield book;
      }
    }
  }
}

const books = [
  { title: "The Hobbit", genre: "Fantasy", author: "Tolkien" },
  { title: "1984", genre: "Dystopian", author: "Orwell" },
  { title: "Pride and Prejudice", genre: "Romance", author: "Austen" },
  { title: "The Lord of the Rings", genre: "Fantasy", author: "Tolkien" }
];

const collection = new BookCollection(books);

// Iterate over all books
for (const book of collection) {
  console.log(book.title);
}

// Filter by genre
for (const book of collection.filterByGenre("Fantasy")) {
  console.log(`${book.title} by ${book.author}`);
}
```

### 5. Generator with Return and Throw

```javascript
function* generatorWithReturn() {
  yield 1;
  yield 2;
  return "Finished!"; // Return value
  yield 3; // This will never execute
}

const gen = generatorWithReturn();

console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: "Finished!", done: true }
console.log(gen.next()); // { done: true }

// Generator with error handling
function* generatorWithError() {
  try {
    yield 1;
    yield 2;
    throw new Error("Something went wrong!");
  } catch (error) {
    yield `Error caught: ${error.message}`;
  }
  yield 3;
}

const errorGen = generatorWithError();

console.log(errorGen.next()); // { value: 1, done: false }
console.log(errorGen.next()); // { value: 2, done: false }
console.log(errorGen.next()); // { value: "Error caught: Something went wrong!", done: false }
console.log(errorGen.next()); // { value: 3, done: false }
console.log(errorGen.next()); // { done: true }
```

### 6. Async Generator (ES2018+)

```javascript
// Async generator for fetching data
async function* asyncDataGenerator(urls) {
  for (const url of urls) {
    try {
      const response = await fetch(url);
      const data = await response.json();
      yield data;
    } catch (error) {
      yield { error: error.message };
    }
  }
}

// Simulate async operations
async function* simulateAsyncGenerator() {
  for (let i = 1; i <= 3; i++) {
    await new Promise(resolve => setTimeout(resolve, 1000)); // Wait 1 second
    yield `Data ${i}`;
  }
}

// Use async generator
async function processAsyncData() {
  for await (const data of simulateAsyncGenerator()) {
    console.log(data);
  }
}

processAsyncData();
// Output after 1 second: "Data 1"
// Output after 2 seconds: "Data 2"
// Output after 3 seconds: "Data 3"
```

## ❓ Practice Questions

### Question 1: Iterator Behavior
```javascript
const obj = {
  data: [1, 2, 3, 4, 5],
  [Symbol.iterator]() {
    let index = 0;
    return {
      next() {
        if (index < obj.data.length) {
          return { value: obj.data[index++], done: false };
        }
        return { done: true };
      }
    };
  }
};

for (const item of obj) {
  console.log(item);
}
// What will be the output and why?
```

**Answer**: The output will be `1, 2, 3, 4, 5`. The iterator implementation:
- Creates a closure over the `obj.data` array and `index` variable
- Each call to `next()` returns the current element and increments the index
- When index reaches the array length, it returns `{ done: true }`
- The `for...of` loop automatically calls `next()` until `done` is `true`

### Question 2: Generator State
```javascript
function* counter() {
  let count = 0;
  while (true) {
    count++;
    yield count;
  }
}

const gen1 = counter();
const gen2 = counter();

console.log(gen1.next().value); // What will this output?
console.log(gen2.next().value); // What will this output?
console.log(gen1.next().value); // What will this output?
```

**Answer**: 
- `gen1.next().value` outputs `1`
- `gen2.next().value` outputs `1` 
- `gen1.next().value` outputs `2`

Each generator instance maintains its own state. `gen1` and `gen2` are independent instances, so they each have their own `count` variable. The first call to each generator starts with `count = 0`, then increments to `1` and yields it. The second call to `gen1` increments `count` to `2` and yields it.

### Question 3: Iterator Protocol
```javascript
const array = [1, 2, 3];
const iterator = array[Symbol.iterator]();

console.log(iterator.next()); // What will this output?
console.log(iterator.next()); // What will this output?
console.log(iterator.next()); // What will this output?
console.log(iterator.next()); // What will this output?
```

**Answer**: 
- `iterator.next()` outputs `{ value: 1, done: false }`
- `iterator.next()` outputs `{ value: 2, done: false }`
- `iterator.next()` outputs `{ value: 3, done: false }`
- `iterator.next()` outputs `{ done: true }`

The array iterator returns each element sequentially. After the last element (`3`), the iterator returns `{ done: true }` to indicate completion. Any subsequent calls will continue to return `{ done: true }`.

## 🔍 Deep Dive

### 1. Infinite Generator with Take

```javascript
function* infiniteNumbers() {
  let num = 0;
  while (true) {
    yield num++;
  }
}

function take(iterator, count) {
  return {
    [Symbol.iterator]() {
      return {
        next() {
          if (count > 0) {
            count--;
            return iterator.next();
          }
          return { done: true };
        }
      };
    }
  };
}

// Take only first 5 numbers from infinite sequence
const firstFive = take(infiniteNumbers(), 5);
for (const num of firstFive) {
  console.log(num); // 0, 1, 2, 3, 4
}

// Take next 3 numbers
const nextThree = take(infiniteNumbers(), 3);
for (const num of nextThree) {
  console.log(num); // 0, 1, 2 (starts over because it's a new generator)
}
```

### 2. Generator Composition

```javascript
function* map(iterator, fn) {
  for (const item of iterator) {
    yield fn(item);
  }
}

function* filter(iterator, predicate) {
  for (const item of iterator) {
    if (predicate(item)) {
      yield item;
    }
  }
}

function* take(iterator, count) {
  let taken = 0;
  for (const item of iterator) {
    if (taken >= count) break;
    yield item;
    taken++;
  }
}

// Compose operations
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const result = take(
  filter(
    map(numbers, x => x * 2), // Double each number
    x => x > 10 // Filter numbers greater than 10
  ),
  3 // Take first 3 results
);

for (const num of result) {
  console.log(num); // 12, 14, 16, 18, 20
}
```

### 3. Custom Iterable with Multiple Iterators

```javascript
class Matrix {
  constructor(rows, cols, defaultValue = 0) {
    this.rows = rows;
    this.cols = cols;
    this.data = Array(rows).fill().map(() => Array(cols).fill(defaultValue));
  }
  
  // Row iterator
  *[Symbol.iterator]() {
    for (let i = 0; i < this.rows; i++) {
      yield this.data[i];
    }
  }
  
  // Column iterator
  *columns() {
    for (let j = 0; j < this.cols; j++) {
      const column = [];
      for (let i = 0; i < this.rows; i++) {
        column.push(this.data[i][j]);
      }
      yield column;
    }
  }
  
  // Diagonal iterator
  *diagonal() {
    const min = Math.min(this.rows, this.cols);
    for (let i = 0; i < min; i++) {
      yield this.data[i][i];
    }
  }
  
  // Set value at position
  set(row, col, value) {
    if (row >= 0 && row < this.rows && col >= 0 && col < this.cols) {
      this.data[row][col] = value;
    }
  }
}

const matrix = new Matrix(3, 3);
matrix.set(0, 0, 1);
matrix.set(1, 1, 2);
matrix.set(2, 2, 3);

console.log("Rows:");
for (const row of matrix) {
  console.log(row);
}

console.log("Columns:");
for (const col of matrix.columns()) {
  console.log(col);
}

console.log("Diagonal:");
for (const item of matrix.diagonal()) {
  console.log(item);
}
```

### 4. Generator for File Processing

```javascript
function* processFileLines(lines) {
  let lineNumber = 0;
  let inCommentBlock = false;
  
  for (const line of lines) {
    lineNumber++;
    const trimmedLine = line.trim();
    
    // Skip empty lines
    if (trimmedLine === '') {
      continue;
    }
    
    // Handle comment blocks
    if (trimmedLine.startsWith('/*')) {
      inCommentBlock = true;
      continue;
    }
    
    if (trimmedLine.endsWith('*/')) {
      inCommentBlock = false;
      continue;
    }
    
    if (inCommentBlock) {
      continue;
    }
    
    // Skip single-line comments
    if (trimmedLine.startsWith('//')) {
      continue;
    }
    
    // Process valid line
    yield {
      lineNumber,
      content: trimmedLine,
      type: 'code'
    };
  }
}

// Simulate file lines
const fileLines = [
  'function example() {',
  '  // This is a comment',
  '  const x = 42;',
  '  /*',
  '    Multi-line comment',
  '  */',
  '  return x;',
  '}',
  ''
];

for (const processed of processFileLines(fileLines)) {
  console.log(`Line ${processed.lineNumber}: ${processed.content}`);
}
```

## 📝 Summary

### Key Takeaways

1. **Iterators** provide a standardized way to iterate over any data structure
2. **Generators** are functions that can pause and resume execution
3. **Symbol.iterator** is the key to making objects iterable
4. **Yield** pauses generator execution and returns values
5. **Lazy evaluation** enables memory-efficient processing of large datasets
6. **Generator composition** allows building complex iteration pipelines

### Mind Map

```
Iterators & Generators
├── Iterators
│   ├── Symbol.iterator method
│   ├── next() method
│   ├── done property
│   └── value property
├── Generators
│   ├── function* syntax
│   ├── yield keyword
│   ├── Generator object
│   └── State management
├── Iterable Objects
│   ├── Arrays (built-in)
│   ├── Strings (built-in)
│   ├── Maps & Sets (built-in)
│   └── Custom objects
├── Generator Features
│   ├── Parameters
│   ├── Return values
│   ├── Error handling
│   └── Async generators
├── Use Cases
│   ├── Custom iteration
│   ├── Lazy evaluation
│   ├── Data processing
│   ├── Memory efficiency
│   └── Stream processing
└── Best Practices
    ├── Use for...of when possible
    ├── Handle generator state properly
    ├── Consider memory implications
    └── Compose generators for complex logic
```

### Next Steps

Ready to move to the next concept? Continue to:
**[Symbols & Well-known Symbols](./08-symbols/)** → Learn about unique identifiers and built-in symbols for advanced JavaScript features

---

**💡 Pro Tip**: Use generators for processing large datasets or infinite sequences. They provide memory efficiency by computing values only when needed, rather than storing everything in memory at once.

