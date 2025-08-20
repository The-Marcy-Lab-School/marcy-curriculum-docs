# Strings & Conditional Statements

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-3-strings-conditional-statements)!
{% endhint %}

**Table of Contents:**
- [Zooming in on Strings](#zooming-in-on-strings)
  - [String Interpolation](#string-interpolation)
  - [String Indexes and Bracket Notation](#string-indexes-and-bracket-notation)
  - [Strings are Immutable](#strings-are-immutable)
  - [String Length](#string-length)
  - [String Methods](#string-methods)
- [Conditional Statements](#conditional-statements)
  - [`if/else` statements](#ifelse-statements)
  - [Guard Clauses](#guard-clauses)
  - [Ternary Operators](#ternary-operators)

## Zooming in on Strings

Strings are an incredibly flexible data type. Strings have the broadest set of use cases and have many interesting capabilities. Let's dive in.

![alt text](image.png)

### String Interpolation

When used within backticks, an expression can be **interpolated** into a string for easy formatting:

```js
console.log(`The sum of 10 and 2 is ${10 + 2}`);
// Output: The sum of 10 and 2 is 12
```

### String Indexes and Bracket Notation

Each character in a string, including spaces, has an **index** — a numbered position starting at `0`.

We can get a single character from a string using **Bracket Notation** and the index: `string[index]`

```js
const message = 'Hello there!';

console.log(message[0]);  // Prints 'H'
console.log(message[4]);  // Prints 'o'
console.log(message[5]);  // Prints ???
console.log(message[11]); // Prints ???

console.log("abc"[1]);    // Prints 'b'
```

If you use bracket notation and there is no character at the given index, `undefined` will be returned:

```js
const message = 'Hello there!';

console.log(message[50]); // Prints undefined
```

### Strings are Immutable

* Strings are "immutable" (unable to be mutated). In other words, you cannot use bracket notation to change characters of a string.
* If you try, no error will be thrown, nothing will happen.

```js
const message = 'Hello there!';

// Nothing will happen if you attempt to mutate a string.
message[0] = "J";

console.log(message); // Prints "Hello there!"
```

### String Length

Every string also has a `length` property that is accessed using dot notation: `string.length`

```js
const message = 'Hello there!';
console.log( message.length ); // Prints 12

// The length property is useful for calculating indexes from the end of the string
console.log( message[message.length - 1] ); // Prints "!" 
console.log( message[message.length - 2] ); // Prints "e" 
console.log( message[message.length - 12] ); // Prints "H" 
```

### String Methods

A **method** is a function that is attached to a value. Often, methods are used to manipulate the value they are attached to.
* Methods are invoked using **dot notation**: `value.method()`

First, let's look at some "read only" string methods: `includes`, `startsWith`, `endsWith`, `indexOf`, `lastIndexOf`

```js
const fruits = 'apples, bananas, cherries';

// These return a boolean
console.log( fruits.includes('ana') ) // Prints true
console.log( fruits.includes('ANA') ) // Prints false
console.log( fruits.startsWith('apple') ) // Prints true
console.log( fruits.endsWith('s') ) // Prints true

// These return an index number (or -1 if not found)
console.log( fruits.indexOf('p') ) // Prints 1
console.log( fruits.lastIndexOf('p') ) // Prints 2
console.log( fruits.indexOf('?') ) // Prints -1

const hasOnlyOne = (str, letter) => {
  return str.indexOf(letter) === str.lastIndexOf(letter);
}
console.log(hasOnlyOne('hello', 'h')) // Prints true
console.log(hasOnlyOne('hello', 'l')) // Prints false
```

The following methods make a copy of a string before performing some modifications: `slice`, `toUpperCase`, `toLowerCase`, `repeat`, `replace`, `replaceAll`

```js
const fruits = 'apples, bananas, cherries';

const apples = fruits.slice(0, 6);
console.log(apples); // Prints "apples"

const loudFruits = fruits.toUpperCase()
console.log(loudFruits); // Prints 'APPLES, BANANAS, CHERRIES'

console.log(apples.repeat(3)); // Prints 'applesapplesapples'

console.log(apples.replace('p', 'g')); // Prints 'agples'
console.log(apples.replaceAll('p', 'g')); // Prints 'aggles'
```

## Conditional Statements

- Programs are able to change their behavior based on the data used by the program (a.k.a. the "state" of the program).
- Conditions and Booleans help decide if something is `true` or `false`.
- Use `if/else`, `switch`, or ternary operators to decide what to do in a given situation.
- They do this with Comparison Operators and Logical Operators which return a Boolean.

### `if/else` statements

- 3 parts: `if`, `else if`, `else`.
- Starts checking at the top and stops checking when it hits a match.
- Only 1 block is executed, so be specific first and get more general.

**Example:**

```js
const isItHot = (temp) => {
  if (temp > 75) {
    return "it is hot!"
  } else if (temp > 90) {
    return "it is too hot!"
  } else {
    return "it is NOT!"
  }
}
console.log(isItHot(80)); // Prints "it is hot"
console.log(isItHot(95)); // Prints "it is too hot"
console.log(isItHot(60)); // Prints "it is NOT"
```

- Conditions in an `if` statement must evaluate to `true` or `false`.

### Guard Clauses

- Code should be readable.
- Avoid deeply nested `if/else` statements. Use guard clauses and ternary operators.
- A guard clause is an if statement that returns before subsequent has a chance to be executed.

```javascript
// Okay - if and else statements that each return something
const isTruthy = (value) => {
  if (value) {
    return true;
  }
  else {
    return false;
  }
}

// Better - Guard clause lets us skip the else statement by exiting early
const isTruthy = (role) => {
  if (value) {
    return true;
  }
  // if the guard clause above doesn't run, this line will!
  return false;
};
```

### Ternary Operators
- `if` and `else` statements let you choose between one of two code blocks to execute.
- The ternary operator `condition ? valA : valB` is used to choose between one of two values.

```js
// Okay - Use an If statement to choose which code block to execute
const isThisEven = (num) => {
  let message;
  if (num % 2 === 0) {
    message = 'it is even!';
  } else {
    message = 'it is odd!';
  }
  console.log(message);
}

// Better - Use a ternary to choose which value to assign to message
const isThisEven = (num) => {
  const message = num % 2 === 0 ? "it is even!" : "it is odd!";
  console.log(message);
}
```