# Control Flow, Conditional Operators, and Math

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-0-2-control-flow)!
{% endhint %}

**Table of Contents**
- [Slides](#slides)
- [Overview](#overview)
- [Control Flow Review](#control-flow-review)
  - [`if/else` statements](#ifelse-statements)
  - [Comparison Operators:](#comparison-operators)
  - [Logical Operators:](#logical-operators)
  - [Coercions and Truthiness](#coercions-and-truthiness)
- [Simplify Your Logic](#simplify-your-logic)
  - [Guard Clauses and Ternary Operators](#guard-clauses-and-ternary-operators)
  - [Scope and Variables](#scope-and-variables)
- [typeof Operator](#typeof-operator)
  - [Identifying `null`, Arrays, and `NaN`](#identifying-null-arrays-and-nan)
- [Math Operations](#math-operations)
  - [The `Math` Object](#the-math-object)


## Slides

{% embed url="https://docs.google.com/presentation/d/102SIHvxRHHJ-_979yJdyq-3dI5NEVSME0uyBrSmgvrk/embed?start=false&loop=false&delayms=3000" %}

## Overview

**Key Terms**
* **Comparison Operators** -  Operators that compare two values and return a boolean
* **Logical Operators** - Operators used to combine conditions, such as && (AND), || (OR), and ! (NOT)
* **Type Coercion** - The automatic conversion of a value from one data type to another
* **Guard Clause** - A statement used to check if the remaining lines of code should be executed or not
* **Scope** - Scope is the current context of execution in which values and expressions are "visible" or can be referenced


## Control Flow Review

- Programs usually change their behavior based on inputs.
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

### Comparison Operators:
  
* Comparison Operators compare two values of the same type.

| Operator | Name               | Example         | Result  |
| -------- | ------------------ | --------------- | ------- |
| ===      | Equal To           | `'Hi' === 'Hi'` | `true`  |
| !==      | Not Equal To       | `6 !== 7`       | `true`  |
| !=       | Loose Equal To     | `"6" == 6`      | `true`  |
| !=       | Loose Not Equal To | `6 != "6"`      | `false` |
| >        | Greater Than       | `10 > 1`        | `true`  |
| <        | Less Than          | `30 < 5`        | `false` |
| >=       | Greater or Equal   | `5 >= 5`        | `true`  |
| <=       | Less or Equal      | `2 <= 2`        | `true`  |


### Logical Operators:

* Logical Operators compare the outcomes of two conditions and let you be more expressive

| Operator | Name | Example            | Result  |
| -------- | ---- | ------------------ | ------- |
| &&       | AND  | `5 > 3 && 2 > 3`   | `false` |
|          |      | `5 > 3 && 4 > 3`   | `true`  |
| \|\|     | OR   | `5 > 3 \|\| 2 > 3` | `true`  |
| !        | NOT  | `!(5 > 3)`         | `false` |

* We can use `()` to group together each condition

```js
const isItHot = (temp, isRaining) => {
  // if it is at least 90 or it is over 75 and isn't raining, its hot
  if (temp >= 90 || (temp > 75 && !isRaining)) {
    return "it is hot!"
  } else {
    return "it is NOT!"
  }
}
console.log(isItHot(90, true)); // Prints "it is hot"
console.log(isItHot(90, false)); // Prints "it is hot"
console.log(isItHot(80, false)); // Prints "it is hot"
console.log(isItHot(80, true)); // Prints "it is NOT"
console.log(isItHot(60, false)); // Prints "it is NOT"
```

### Coercions and Truthiness

- **Type Coercion ("co-er-shun")** — JavaScript will automatically convert (coerce) types when necessary. For example, `'1' + 1` results in `'11'`.
- **Truthy/Falsy Values** — any expression you use for an `if` statement's `(condition)` will be coerced into a Boolean.
  - "Falsy" values are coerced to be `false` and only these 5 values will behave this way: `0`, `""`, `null`, `undefined`, and `NaN`
  - All other values are truthy

```js
const isTruthy = (value) => {
  if (value) { // the value will be coerced to be a Boolean. If it is true, we will return true.
    return true;
  }
  else { // if not, we will return false.
    return false;
  }
}

console.log(isTruthy('')) // Prints false
console.log(isTruthy(0)) // Prints false
console.log(isTruthy(NaN)) // Prints false
console.log(isTruthy(null)) // Prints false
console.log(isTruthy(undefined)) // Prints false
console.log(isTruthy('hello')) // Prints true
console.log(isTruthy(' ')) // Prints true
console.log(isTruthy(5)) // Prints true
console.log(isTruthy([])) // Prints true
console.log(isTruthy({})) // Prints true
```

## Simplify Your Logic

### Guard Clauses and Ternary Operators

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

### Scope and Variables

- If you want to get variables out of blocks, you have to define them in a higher scope.
- Values in a higher scope are visible to lower scopes.

**Example:**

```javascript
const getBioBest = (age, first, last) => {
  let dynamicBio = `Wow, ${first} ${last} is already ${age} years old. Getting up there!`;
  if (age < 18) {
    dynamicBio = `${first} ${last} is only ${age} years old. Still a child!`;
  } else if (age < 50) {
    dynamicBio = `${first} ${last} is ${age} years old. Still young at heart!`;
  }
  console.log(dynamicBio);
};
```

## typeof Operator

- `typeof value` returns the type of a given `value` as a string. It’s an operator, not a function.
- Example usages:

```javascript
console.log(typeof "hi"); // Prints "string"
console.log(typeof 4); // Prints "number"
console.log(typeof NaN); // Prints "number"
console.log(typeof true); // Prints "boolean"
console.log(typeof undefined); // Prints "undefined"
console.log(typeof console.log); // Prints "function"
console.log(typeof {}); // Prints "object"
console.log(typeof null); // Prints "object"
console.log(typeof []); // Prints "object"
```

### Identifying `null`, Arrays, and `NaN`

- Unfortunately, `typeof` tells us that arrays are objects (they are) so we have to use `Array.isArray()` to check if something is an array.
- It also tells us that `null` is an Object so we have to check that manually 
- It also tells us that `NaN` is a number, which it is, but to know if something is `NaN`, we can use the `isNaN()` function
- 
```js 
const arr = [];
console.log(Array.isArray(arr)); // Prints true

const nullVal = null
console.log(nullVal === null); // Prints true

const badNumber = NaN
console.log(isNaN(badNumber)); // Prints true
```

## Math Operations

- Use basic math operators: `+`, `-`, `/`, `*`.
- The power operator `x ** y` raises x to the power of y.
- `%` returns the remainder after division.

```javascript
console.log(5 % 2); // 1 (5 divided by 2 is 2 with a remainder of 1)
console.log(10 % 3); // 1 (10 divided by 3 is 3 with a remainder of 1)
console.log(15 % 5); // 0 (15 divided by 5 is 3 with no remainder)
```

### The `Math` Object

- In JavaScript `Math` is a globally available object with methods and properties for doing more complex math.
```javascript
console.log(Math.round(2.5))    // Prints 3
console.log(Math.floor(2.99))   // Prints 2
console.log(Math.ceil(2.01))    // Prints 3
console.log(Math.random())      // Prints a random decimal from 0 to 1 (exclusive)
console.log(Math.PI)            // Prints 3.141592653589793
console.log(Math.sqrt(25));     // Prints 5
console.log(Math.abs(-25));     // Prints 25
console.log(Math.max(2, 5, 3))  // Prints 5
console.log(Math.min(2, 5, 3))  // Prints 2
```

- Here is an example of a function that makes use of `Math.random()` to flip a coin

```js
const flipCoin = () => {
  return Math.random() > 0.5 ? "Heads" : "Tails"
}
```
