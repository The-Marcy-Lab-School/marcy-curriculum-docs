# Control Flow & Conditionals

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-0-2-control-flow)!
{% endhint %}

## Slides

{% embed url="https://docs.google.com/presentation/d/102SIHvxRHHJ-_979yJdyq-3dI5NEVSME0uyBrSmgvrk/embed?start=false&loop=false&delayms=3000" %}

## Overview

**Objectives**

**Key Terms**

## Flow Control Review
- Programs usually change their behavior based on inputs.
- Conditions and Booleans help decide if something is `true` or `false`.
- Use `if/else`, `switch`, or `ternarys` to decide what to do in a given situation.

### Conditions and Comparisons
- Conditions evaluate to `true` or `false`.
- They do this with Comparison Operators and Logical Operators.
  - Comparison Operators compare two values of the same type.
  - Logical Operators compare the outcomes of two conditions.

#### Comparison Operators:
| Operator | Name             | Example        |
|----------|------------------|----------------|
| ===      | Equal To          | `'Hi' === 'Hi'`|
| !==      | Not Equal To      | `6 !== 7`      |
| >        | Greater Than      | `10 > 1`       |
| <        | Less Than         | `3 < 5`        |
| >=       | Greater or Equal  | `5 >= 5`       |
| <=       | Less or Equal     | `2 <= 2`       |

#### Logical Operators:
| Operator | Name             | Example        |
|----------|------------------|----------------|
| &&       | AND               | `true && false`|
| ||       | OR                | `true || false`|
| !        | NOT               | `!true`        |

### `if/else` statements
- 3 parts: `if`, `else if`, `else`.
- Starts checking at the top and stops checking when it hits a match.
- Only 1 block is executed, so be specific first and get more general.

#### Example:
```javascript
const happyBirthday = (age) => {
  if (age === 25) {
    return "Happy birthday, you can rent a car!";
  } else if (age < 25) {
    return "No rental cars on your birthday yet.";
  } else {
    return "Enjoy car rentals on your birthday!";
  }
}
```

## Simplify Your Logic
### Don’t nest too much
- Code should be readable.
- Avoid deeply nested `if/else` statements.
- Use guard clauses and ternarys.

#### Example of nested vs. simplified code:
```javascript
const doAdminThings = (role) => {
  if (role === 'admin') {
    console.log('You can do admin things');
  } else {
    console.log('Nothing to do');
  }
}

const doAdminThingsBetter = (role) => {
  if (role !== 'admin') return console.log('Nothing to do');
  console.log('You can do admin things');
}
```

### Scope and Variables
- If you want to get variables out of blocks, you have to define them in a higher scope.
- Values in a higher scope are visible to lower scopes.

#### Example:
```javascript
const getBioBest = (age, first, last) => {
  let dynamicBio = `Wow, ${first} ${last} is already ${age} years old. Getting up there!`
  if (age < 18) {
    dynamicBio = `${first} ${last} is only ${age} years old. Still a child!`
  } else if (age < 50) {
    dynamicBio = `${first} ${last} is ${age} years old. Still young at heart!`
  }
  console.log(dynamicBio);
}
```

## typeof Operator
- `typeof` is used to check types. It’s an operator, not a function.
- Example usages:
```javascript
console.log(typeof 'hi'); // string
console.log(typeof 4);    // number
console.log(typeof true); // boolean
```

## Math Operations
- Use basic math operators: `+`, `-`, `/`, `*`.
- The power operator `x ** y` raises x to the power of y.
- `%` is modulo, meaning remainder division.

#### Example:
```javascript
Math.round(2.5);  // 3
Math.floor(2.99); // 2
Math.ceil(2.01);  // 3
```































## Skills we're teaching

- (CORE) How conditions work **REVIEW**
- (CORE) if/else statements **REVIEW**
- specific => general statements 
- (CORE) Comparison and logical operators **REVIEW**
- (CORE) truthy/falsy values
- guard clauses
- ternary
- (CORE) scope to lift up variables
- +,-,/,\* **REVIEW**
- \*\*, %
- .ceil(), .round(), .floor()
- (CORE) typeof operator
- typeof quirks: arrays, null, NaN

## Expected Existing skills

- basic understanding of if/else syntax
- basic understanding of comparison syntax
- familiarity with 4 arithmetic operators
