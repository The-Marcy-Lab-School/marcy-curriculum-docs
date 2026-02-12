# JavaScript Fundamentals Cheat Sheet

Use this as a reference while working.

**Table of Contents:**
- [Data Types and Variables](#data-types-and-variables)
  - [Data Types](#data-types)
  - [Operators](#operators)
  - [Variables and Scope](#variables-and-scope)
- [Functions](#functions)
  - [Defining Functions](#defining-functions)
  - [Parameters, Arguments, and Return Values](#parameters-arguments-and-return-values)
- [Strings](#strings)
  - [String Basics](#string-basics)
  - [String Methods](#string-methods)
- [Conditional Statements](#conditional-statements)
- [Node Modules and Testing](#node-modules-and-testing)
  - [Modules](#modules)
  - [npm and package.json](#npm-and-packagejson)
  - [Jest Testing](#jest-testing)
- [Loops](#loops)
- [Arrays](#arrays)
  - [Array Basics](#array-basics)
  - [Mutating Arrays](#mutating-arrays)
  - [Reference Types and Pure Functions](#reference-types-and-pure-functions)
  - [Destructuring Arrays](#destructuring-arrays)
- [Objects](#objects)
  - [Object Basics](#object-basics)
  - [Modifying Objects](#modifying-objects)
  - [Iterating Over Objects](#iterating-over-objects)
  - [Destructuring Objects](#destructuring-objects)
- [Error Handling](#error-handling)
- [Higher-Order Functions and Callbacks](#higher-order-functions-and-callbacks)
- [Array Higher-Order Methods](#array-higher-order-methods)
  - [map, filter, find, findIndex](#map-filter-find-findindex)
  - [reduce](#reduce)
  - [sort](#sort)
  - [Method Chaining](#method-chaining)
- [Regular Expressions (Bonus)](#regular-expressions-bonus)

## Data Types and Variables

### Data Types

**Primitive Types** (immutable, stored directly in memory):

| Type        | Example                   | Description                     |
| ----------- | ------------------------- | ------------------------------- |
| `String`    | `"hello"`, `'world'`     | Sequence of characters          |
| `Number`    | `42`, `10.5`, `-2`       | Numeric values                  |
| `Boolean`   | `true`, `false`          | Truth values                    |
| `Undefined` | `undefined`              | Variable declared but not assigned |
| `Null`      | `null`                   | Intentional absence of value    |

**Reference Types** (mutable, stored in heap memory):

| Type       | Example                           | Description                   |
| ---------- | --------------------------------- | ----------------------------- |
| `Object`   | `{ name: "Alex", age: 25 }`      | Collection of key:value pairs |
| `Array`    | `["apple", "banana", "cherry"]`   | Ordered list of values        |
| `Function` | `(x) => x * 2`                   | Reusable block of code        |

**Type Coercion:**

```js
String(1)           // "1"
Number("hello")     // NaN
Boolean(0)          // false

// Falsy values: 0, '', null, undefined, NaN
// Everything else is truthy
```

Use `typeof` to check a value's type:

```js
typeof "hello"    // "string"
typeof 42         // "number"
typeof true       // "boolean"
typeof undefined  // "undefined"
typeof null       // "object" (this is a known JS quirk)
typeof [1, 2]     // "object"
```

### Operators

| Category   | Operators                                          | Notes                                       |
| ---------- | -------------------------------------------------- | ------------------------------------------- |
| Arithmetic | `+`, `-`, `*`, `/`, `%` (modulus), `**` (exponent) |                                             |
| Comparison | `>`, `<`, `>=`, `<=`                               |                                             |
| Equality   | `===` (strict), `!==` (strict not equal)           | Always use strict equality, never `==`/`!=` |
| Logical    | `&&` (AND), `\|\|` (OR), `!` (NOT)                |                                             |
| Ternary    | `condition ? valueIfTrue : valueIfFalse`           | Shorthand for if/else                       |

### Variables and Scope

**Three operations** you can perform with variables:
1. **Declare** — create a new variable with `let` or `const`
2. **Assign** — give the variable a value (only with `let`)
3. **Reference** — use the variable to access its value

```js
const preferred = 'use const';   // Cannot be reassigned (preferred)
let flexible = 'use let';        // Use when reassignment is needed
flexible = 'new value';          // OK

// DON'T use var or undeclared variables
```

**Scope** determines where a variable can be accessed:
- **Block scope** — inside `{}` of if/for/while blocks
- **Function scope** — inside a function
- **Module scope** — within a file
- **Global scope** — accessible everywhere (avoid!)

```js
const a = 'module scope';

const myFunc = () => {
  const b = 'function scope';
  if (true) {
    const c = 'block scope';
    console.log(a, b, c); // all accessible
  }
  // c is NOT accessible here
};
// b is NOT accessible here
```

## Functions

### Defining Functions

Arrow functions are preferred:

```js
// Arrow function (preferred)
const add = (x, y) => {
  return x + y;
};

// Implicit return (single expression, no curly braces)
const add = (x, y) => x + y;
```

### Parameters, Arguments, and Return Values

- **Parameters** — placeholders in the function definition
- **Arguments** — actual values passed when calling the function
- **Return values** — the value sent back to the caller; terminates the function

```js
const greet = (name) => {        // name is a parameter
  return `Hello, ${name}!`;
};
greet('Ada');                     // 'Ada' is an argument
// returns "Hello, Ada!"

// Functions without a return statement return undefined
```

**Function calls resolve from the inside out:**

```js
add(add(1, 2), add(3, 4))
// add(3, 7)
// 10
```

## Strings

### String Basics

- Each character has an **index** starting at `0`
- Strings are **immutable** — string methods return new strings

```js
const message = 'Hello there!';
message[0]                    // 'H'
message[message.length - 1]   // '!'
message.length                // 12
```

**Template Literals** (string interpolation):

```js
const name = 'Ada';
const greeting = `Hello, ${name}! 2 + 2 = ${2 + 2}`;
// "Hello, Ada! 2 + 2 = 4"
```

**Escape Characters:** `\"` (quote), `\n` (newline), `\t` (tab)

### String Methods

**Read-only methods** (return booleans or numbers):

| Method              | Returns   | Description                              |
| ------------------- | --------- | ---------------------------------------- |
| `.includes(str)`    | `boolean` | Does the string contain `str`?           |
| `.startsWith(str)`  | `boolean` | Does the string start with `str`?        |
| `.endsWith(str)`    | `boolean` | Does the string end with `str`?          |
| `.indexOf(str)`     | `number`  | Index of first match (`-1` if not found) |

**Methods that return a modified copy:**

| Method                        | Description                       |
| ----------------------------- | --------------------------------- |
| `.slice(start, end)`          | Extract a portion of the string   |
| `.toUpperCase()`              | Convert to uppercase              |
| `.toLowerCase()`              | Convert to lowercase              |
| `.repeat(n)`                  | Repeat string `n` times          |
| `.replace(str, replacement)`  | Replace first occurrence          |
| `.replaceAll(str, replacement)` | Replace all occurrences         |

```js
const str = 'Hello World';
str.includes('World')         // true
str.slice(0, 5)               // 'Hello'
str.toLowerCase()             // 'hello world'
str.replace('World', 'Ada')   // 'Hello Ada'
```

## Conditional Statements

```js
// if / else if / else
if (condition1) {
  // runs if condition1 is true
} else if (condition2) {
  // runs if condition2 is true
} else {
  // runs if all conditions are false
}
```

**Order matters!** Only the first `true` condition executes.

**Guard Clauses** — return early to simplify logic:

```js
const canVote = (age) => {
  if (age < 18) return false;
  return true;
};
```

**Ternary Operator** — shorthand for simple if/else:

```js
const message = num % 2 === 0 ? 'even' : 'odd';
```

## Node Modules and Testing

### Modules

Modules let you split code across files and share functionality.

**Exporting:**

```js
// Default export (single value)
module.exports = myFunction;

// Named exports (multiple values)
module.exports = { functionA, functionB, CONSTANT };
```

**Importing:**

```js
// Import default export
const myFunction = require('./myFunction.js');

// Import named exports (with destructuring)
const { functionA, functionB } = require('./helpers.js');

// Import from node_modules
const prompt = require('prompt-sync');
```

### npm and package.json

- `npm i package-name` — install a package
- `npm i --save-dev jest` — install as a dev dependency
- `package.json` — lists dependencies and scripts
- `node_modules/` — contains downloaded packages (don't commit this!)

**npm scripts** in `package.json`:

```json
"scripts": {
  "test": "jest",
  "test:w": "jest --watchAll",
  "dev": "nodemon index.js"
}
```

Run with: `npm test`, `npm run test:w`, `npm run dev`

### Jest Testing

```js
const sum = require('../src/sum');

test('adds two numbers', () => {
  expect(sum(1, 2)).toBe(3);        // strict equality (===)
});

test('returns an array', () => {
  expect(double([1, 2])).toEqual([2, 4]); // deep equality (objects/arrays)
});

test('negation', () => {
  expect(sum(1, 2)).not.toBe(4);    // .not negates any matcher
});
```

## Loops

**For Loop** — use when you know how many iterations:

```js
for (let i = 0; i < 10; i++) {
  console.log(i);
}
// Order: initialize → check condition → execute body → update → repeat
```

**While Loop** — use when the number of iterations is unknown:

```js
while (true) {
  const input = prompt("Enter 'q' to quit: ");
  if (input === 'q') break;       // exit the loop
  if (isNaN(Number(input))) continue; // skip to next iteration
  console.log(`You entered: ${input}`);
}
```

**`break` vs `return`:**
- `break` — exits the current loop, continues after the loop
- `return` — exits the loop AND the current function

**Nested Loops:**

```js
for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 5; j++) {
    console.log(`${i} - ${j}`);
  }
}
// Outer runs 3 times × inner runs 5 times = 15 total iterations
```

## Arrays

### Array Basics

```js
const fruits = ['apple', 'banana', 'cherry'];
fruits[0]           // 'apple'
fruits.length       // 3
fruits.includes('banana')  // true
fruits.indexOf('cherry')   // 2
fruits.slice(0, 2)         // ['apple', 'banana'] (non-mutating)
```

### Mutating Arrays

Unlike strings, arrays **are mutable**:

```js
const arr = ['a', 'b', 'c'];

arr[1] = 'B';              // change element at index
arr.push('d');              // add to end
arr.unshift('z');           // add to beginning
arr.pop();                  // remove from end
arr.shift();                // remove from beginning
arr.splice(1, 0, 'new');   // insert 'new' at index 1
arr.splice(1, 1, 'replaced'); // replace 1 element at index 1
```

### Reference Types and Pure Functions

Reference type variables store a **reference** to data in memory, not the data itself:

```js
const original = [1, 2, 3];
const copy = original;     // both point to the SAME array
copy.push(4);
console.log(original);     // [1, 2, 3, 4] — original is affected!
```

**Pure functions** avoid mutating input values. Use the spread operator to copy:

```js
const extend = (arr, value) => {
  return [...arr, value];  // creates a new array
};

const nums = [1, 2, 3];
const result = extend(nums, 4); // [1, 2, 3, 4]
console.log(nums);               // [1, 2, 3] — unchanged
```

### Destructuring Arrays

```js
const [first, second, ...rest] = [1, 2, 3, 4, 5];
first   // 1
second  // 2
rest    // [3, 4, 5]
```

## Objects

### Object Basics

Objects store related data as **key:value pairs** (properties):

```js
const user = {
  userId: 44292,
  username: 'c0d3rkid',
  friends: ['alice', 'bob'],
  favoriteMeal: {
    name: 'PB&J',
    ingredients: ['peanut butter', 'jelly', 'bread']
  }
};
```

**Accessing properties:**

```js
// Dot notation (preferred)
user.username              // 'c0d3rkid'
user.favoriteMeal.name     // 'PB&J'

// Bracket notation (required when key is in a variable)
const key = 'username';
user[key]                  // 'c0d3rkid'
```

### Modifying Objects

```js
user.password = 'newpass';   // modify existing property
user.isAdmin = false;        // add new property
delete user.password;        // delete property
```

Objects are reference types — use spread to copy:

```js
const copy = { ...original };  // shallow copy
```

**Object shorthand** — when key names match variable names:

```js
const name = 'ben';
const age = 30;
const user = { name, age };  // { name: 'ben', age: 30 }
```

### Iterating Over Objects

```js
const scores = { math: 95, english: 88, science: 92 };

Object.keys(scores)     // ['math', 'english', 'science']
Object.values(scores)   // [95, 88, 92]
Object.entries(scores)  // [['math', 95], ['english', 88], ['science', 92]]

Object.entries(scores).forEach(([subject, score]) => {
  console.log(`${subject}: ${score}`);
});
```

### Destructuring Objects

```js
const { username, friends } = user;
console.log(username);  // 'c0d3rkid'

// In function parameters
const introduce = ({ name, age }) => {
  console.log(`${name} is ${age}`);
};
```

## Error Handling

**Common error types:**

| Error Type       | Cause                                     |
| ---------------- | ----------------------------------------- |
| `SyntaxError`    | Invalid syntax (missing quotes, brackets) |
| `ReferenceError` | Variable not defined                      |
| `TypeError`      | Wrong data type or method doesn't exist   |
| `RangeError`     | Value outside acceptable range            |

**Reading an error message:** look for the error type, the description, and the call stack (which shows the chain of function calls that led to the error).

**Try/Catch** — handle errors without crashing:

```js
try {
  // code that might throw an error
  arr.push('value');
} catch (err) {
  console.log(`Oops! ${err.message}`);
}
// program continues running
```

## Higher-Order Functions and Callbacks

- **Higher-Order Function (HOF)** — a function that accepts another function as an argument or returns a function
- **Callback** — a function passed as an argument to a HOF

**Important:** Pass the function reference, don't invoke it:

```js
// Wrong — invokes immediately
setTimeout(printMessage(), 1000);

// Right — passes the function reference
setTimeout(printMessage, 1000);

// Inline callback
setTimeout(() => {
  console.log('1 second passed');
}, 1000);
```

**`Array.forEach`** — iterate and perform side effects:

```js
const fruits = ['apples', 'bananas', 'cherries'];

fruits.forEach((value, index) => {
  console.log(`${index}: ${value}`);
});
```

## Array Higher-Order Methods

| Method            | Callback Returns    | Method Returns              | Use Case                    |
| ----------------- | ------------------- | --------------------------- | --------------------------- |
| `forEach(fn)`     | nothing             | `undefined`                 | Perform side effects        |
| `map(fn)`         | transformed value   | new array (same length)     | Transform each element      |
| `filter(fn)`      | `boolean`           | new array (subset)          | Keep elements that pass     |
| `find(fn)`        | `boolean`           | first matching element      | Get first match             |
| `findIndex(fn)`   | `boolean`           | index of first match        | Get index of first match    |
| `reduce(fn, init)`| accumulated value   | single value                | Combine into one value      |
| `sort(fn)`        | comparison number   | sorted array (mutates!)     | Sort elements               |

### map, filter, find, findIndex

```js
const nums = [1, 2, 3, 4, 5];

// map — transform each element
const doubled = nums.map((num) => num * 2);
// [2, 4, 6, 8, 10]

// filter — keep elements that pass a test
const evens = nums.filter((num) => num % 2 === 0);
// [2, 4]

// find — get the first element that passes
const firstEven = nums.find((num) => num % 2 === 0);
// 2

// findIndex — get the index of the first element that passes
const firstEvenIdx = nums.findIndex((num) => num % 2 === 0);
// 1
```

### reduce

```js
const nums = [1, 2, 3, 4, 5];

// Sum
const sum = nums.reduce((total, current) => {
  return total + current;
}, 0); // 15

// Frequency counter
const letters = ['a', 'b', 'a', 'c', 'b', 'a'];
const freqs = letters.reduce((acc, letter) => {
  acc[letter] = (acc[letter] || 0) + 1;
  return acc;
}, {});
// { a: 3, b: 2, c: 1 }
```

### sort

```js
const nums = [4, 2, 5, 1, 3];

// Ascending
nums.sort((a, b) => a - b);   // [1, 2, 3, 4, 5]

// Descending
nums.sort((a, b) => b - a);   // [5, 4, 3, 2, 1]
```

**Warning:** `.sort()` mutates the original array!

### Method Chaining

```js
const nums = [1, 2, 3, 4, 5];

const result = nums
  .map((num) => num * 3)       // [3, 6, 9, 12, 15]
  .filter((num) => num > 10);  // [12, 15]
```

## Regular Expressions (Bonus)

Regular expressions are patterns for searching, matching, and manipulating text.

**Common patterns:**

| Pattern    | Meaning              |
| ---------- | -------------------- |
| `^`        | Start of string      |
| `$`        | End of string        |
| `\w`       | Word character       |
| `\d`       | Digit (0-9)          |
| `\s`       | Whitespace           |
| `.`        | Any character        |
| `+`        | One or more          |
| `*`        | Zero or more         |
| `?`        | Zero or one          |
| `[abc]`    | Character class      |
| `[^abc]`   | Negated class        |
| `{n}`      | Exactly n times      |
| `{n,m}`    | Between n and m      |

**Flags:** `i` (case insensitive), `g` (global — find all matches)

**Methods:**

```js
// test() — returns boolean
/cat/i.test('the Cat in the hat');    // true

// match() — returns array of matches
'My cat loves catnip'.match(/cat/g);  // ['cat', 'cat']

// replace() — replace matches
'hello world'.replace(/world/, 'JS'); // 'hello JS'
```
