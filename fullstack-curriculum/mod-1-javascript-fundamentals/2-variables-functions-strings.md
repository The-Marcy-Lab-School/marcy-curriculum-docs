# Variables, Functions, and Strings

- [Slides](#slides)
- [Data Types](#data-types)
- [Variables](#variables)
  - [Hoisting: Why We Don't Use `var`](#hoisting-why-we-dont-use-var)
- [Functions](#functions)
- [Scope](#scope)
- [Zooming in on Strings](#zooming-in-on-strings)
  - [String Indexes and Bracket Notation](#string-indexes-and-bracket-notation)
  - [String Length](#string-length)
  - [Strings are Immutable](#strings-are-immutable)
  - [String Methods](#string-methods)


{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-0-1-data-types-variables-functions)!
{% endhint %}

## Slides 

{% embed url="https://docs.google.com/presentation/d/1_BzZepwXrkVL-LzpkPA5ITvM-bH9n0_BnnrzwJJZByo/embed?start=false&loop=false&delayms=3000" %}

## Data Types

* All values have a type. The **data type** determines what you can do with a given value.
* There are five main **primitive data types**: Number, String, Boolean, `undefined`, and `null`

```js
// Strings must have matching "" or '' or ``
const dQuoteString = "Double quotes are nice when a string has a ' in it";
const sQuoteString = 'Single quotes are nice for the "opposite" reason.';
const template = `Template strings allow us to embed expressions like ${5 > 3 ? "yay" : "boo"}`;

// All of these are the Number data type
const number = 10;
const float = 1.5;
const integer = 1;
const negative = -1;

// NaN means Not a Number, but it's technically a Number type.
// They are created when you try to do math on non-Number types.
const notANumber = NaN;
const alsoNaN = 5 * "hello";

// Booleans only have two values. They are case-sensitive!
let boolean = true;
boolean = false;

// Undefined is a type with only one value: undefined
const explicitUndefined = undefined;
const undefinedVariable; // Assigned undefined by default

// Null is a type with only one value: null
// null must be assigned to a variable explicitly
const nullVariable = null;
```

* There are also three **reference data types**: Objects, Arrays, and Functions (we'll talk about these later on).
  * Technically, there is only one reference data type: Objects. Functions and Arrays are subsets of Objects
  
## Variables

* A **Variable** in JavaScript is a named container for data.
* There are 4 ways to declare a new variable, *but only the first two of should be used*:
    1. `const` declares a block-scoped variable that cannot be reassigned. This should be your go-to variable declaration.
    2. `let` declares a re-assignable block-scoped variable. Use only when reassignment is necessary.
    3. `var` declares a re-assignable, hoisted, function-scoped variable. **Do not use `var`**. Hoisting and function-scoped variables can cause unexpected behavior (read more below).
    4. Global variables are declared without a keyword. **Do not use global variables**.

```js
// Good
const myName = 'John';

// Uncaught TypeError: Assignment to constant variable. Hmm, time to use a let.
myName = 'Johnny'; 

// Also good
let myAge = 101;

// No error!
myAge = 5;

// Bad
var status = 'happy';

// Very bad
globalStatus = 'sad';
```

* We can reference a variable by name to either reassign its value or to access its current value:

```js
// use let because we will reassign this variable
let count = 0;

// reference the count variable to access its current value, 0
let message = `the count is ${count}`;

// Prints "the count is 0"
console.log(message); 

// reference the count variable to reassign it
count = 1;

// reference the count variable again to access its new value, 1
message = `the count is now ${count}`

// Prints "the count is now 1
console.log(message); 
```

### Hoisting: Why We Don't Use `var`

{% hint style="info" %}
**TLDR:** Don't use `var` or `function`. Do not reference a variable or function before it has been declared.
{% endhint %}

* **Hoisting** is a feature of JavaScript that allows certain variables/functions to be referenced before they are declared.
  * Variables declared with the `var` keyword and Functions declared with the `function` keyword are hoisted.
  * Variables declared with `const` or `let` and Arrow Functions are NOT hoisted.
* Variables declared with `var` will be given the value `undefined` until their assignment statement is reached. 
* Functions declared with `function` are fully invoke-able anywhere within their scope.

```js
// Good — Uncaught ReferenceError: Cannot access 'name' before initialization
console.log(`Hi ${name}!`); 
const name = 'Jane';

// Also Good — Uncaught ReferenceError: Cannot access 'age' before initialization
console.log(`I'm ${age}.`); 
let age = 101;

// Bad! — Prints "Hi, undefined?" with no Error
console.log(`Hi, ${weird}?`); 
var weird = 'Tom';

// Confusing! — Throws an error but only because `undefined` doesn't have a `toUpperCase` method
console.log(str.toUpperCase());
var str = 'Some string';


// Bad! - This works but it really shouldn't
foo();
function foo() {
  console.log('foo!');
}
```

{% hint style="info" %}
Hoisting (like most weird JS things) stems from the fact that when JS was invented eons ago, there was a desire that JS should never crash a site. Ever. So bugs that would normally get caught in development would sneak through so the page wouldn’t crash, but it also wouldn’t work properly. Nowadays, we prefer to find these errors in development, so crashing is ok.
{% endhint %}


## Functions

* A **Function** is a named container for statements that can be **invoked** to execute its statements.
* There are many ways to create a function, we will use **Arrow Function** syntax:

```js
// Bad — Function Declaration. It is hoisted. Don't do this.
function add(x, y) {
  return x + y;
}

// Good — Arrow Functions are stored in constant variables and aren't hoisted.
const multiply = (x, y) => {
  return x * y;
}

// Advanced — Good: If the function's body is just one line, you can omit the {}
const negate = (x) => return -x;

// Advanced - Better: If the function's body is just a return statement, you can omit the `return` keyword
const isEven = (x) => x % 2 === 0;

// Advanced - Best: If the function only has one parameter, the () can be omitted.
const isOdd = x => x % 2 === 1;
```

* A **function call** statement changes the control flow by "activating" the function. Calling a function sets the first line of code in the function as the next line of code to be executed.
* **Parameters** are variables created when a function is invoked that reference the inputs (a.k.a. **arguments**) provided in the function call.
* A **return statement** terminates a function and returns a value to the location of the function call.

```js
// x and y reference the values provided when the function is called.
const add = (x, y) => {
  return x + y;
}

// 5 and 3 are the inputs (a.k.a arguments)
const sum1 = add(5, 3);

// For this call, 10 and 2 are the arguments
const sum2 = add(10, 2);

console.log(sum1); // Prints 8
console.log(sum2); // Prints 12
```

## Scope

* **Scope** refers to where in our code variables can be referenced. Files, functions, and code blocks `{}` each create a new scope.
* There can be any number of scopes in a single file but we will describe them as either:
  * **Global Scope** — Variables that are accessible across the entire file. Includes things like `console` and `Math` and variables/functions declared at the outermost scope of the file.
  * **Local Scope** — Variables that are accessible only in the current function or code block.
 
* "Outer scopes" cannot see variables in "inner scopes".

{% code title="index.js" overflow="wrap" lineNumbers="true" %}
```javascript
// Global Scope - secretValue is accessible anywhere in the file.
const secretValue = () => {
  // Local Scope - this variable is only accessible in this code block
  const private = 'shhhhh!';

  // Return statements let us send values out of an "inner" scope, but the function must be invoked to do this.
  return private;
}

// We can't access private because we are in an "outer" scope compared to where it is declared.
console.log(private); // ReferenceError: private is not defined

// We can get the value from the function if we invoke it though
console.log(secreteValue()); // Prints "shhhhh!"
```
{% endcode %}

* "Inner scopes" can see variables in "outer scopes".
* Parameters are treated as locally scoped variables.

{% code title="index.js" overflow="wrap" lineNumbers="true" %}
```javascript
// Global Scope - friend is accessible anywhere in this file.
const friend = 'John';

// Global Scope - greetFriend is also accessible anywhere in the file.
const greetFriend = (myName) => {
  // The myName parameter has local/function scope. It can only be accessed inside of greetFriend
  
  // We can access `friend` from greetFriend since we are in an "inner" scope.
  console.log(`Hi, ${friend}, I'm ${myName}`);
}

greetFriend('Jane'); // Prints "Hi, John, I'm Jane"

// We can't access myName because we are in an "outer" scope compared to it is declared.
myName; // ReferenceError: myName is not defined
```
{% endcode %}

## Zooming in on Strings

### String Indexes and Bracket Notation

* A string is a series of characters surrounded by `"double quotes"`, `'single quotes'` or `` `backticks` ``
* Each character in a string, including spaces, has an **index** — a numbered position starting at `0`.
* **Bracket Notation** is used to read a single character in a string by its index: `string[index]`

```js
const message = 'Hello there!';

console.log( message[0] ); // Prints 'H'
console.log( message[4] ); // Prints 'o'
console.log( message[5] ); // Prints ' '

console.log( "abc"[1] ); // Prints 'b'
```

* If you use bracket notation and there is no character at the given index, `undefined` will be returned:

```js
const message = 'Hello there!';

console.log( message[50] ); // Prints undefined
```

### String Length

* Every string also has a `length` property that is accessed using dot notation: `string.length`

```js
const message = 'Hello there!';
console.log( message.length ); // Prints 12

// The length property is useful for calculating indexes from the end of the string
console.log( message[message.length - 1] ); // Prints "!" 
console.log( message[message.length - 2] ); // Prints "e" 
console.log( message[message.length - 12] ); // Prints "H" 

const firstAndLastAreTheSame = (str) => {
  return str[0] === str[str.length-1]
}

console.log(firstAndLastAreTheSame('hello')); // Prints false
console.log(firstAndLastAreTheSame('helloh')); // Prints true
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

### String Methods

* A **method** is a function that is attached to a value. Often, methods are used to manipulate the value they are attached to.
* Methods are invoked using **dot notation**: `value.method()`
* First, let's look at some "read only" string methods: `includes`, `startsWith`, `endsWith`, `indexOf`, `lastIndexOf`

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

* The following methods make a copy of a string before performing some modifications: `slice`, `toUpperCase`, `toLowerCase`, `repeat`, `replace`, `replaceAll`

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