# Control Flow & Conditionals

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-0-2-control-flow)!
{% endhint %}

## Slides

{% embed url="https://docs.google.com/presentation/d/102SIHvxRHHJ-_979yJdyq-3dI5NEVSME0uyBrSmgvrk/embed?start=false&loop=false&delayms=3000" %}

## Overview

**Objectives**

**Key Terms**
* **Comparison Operators** -  Operators that compare two values and return a boolean
* **Logical Operators** - Operators used to combine conditions, such as && (AND), || (OR), and ! (NOT)
* **Type Coercion** - The automatic conversion of a value from one data type to another
* **Guard Clause** - A statement used to check if the remaining lines of code should be executed or not
* **Scope** - Scope is the current context of execution in which values and expressions are "visible" or can be referenced


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

| Operator | Name             | Example         |
| -------- | ---------------- | --------------- |
| ===      | Equal To         | `'Hi' === 'Hi'` |
| !==      | Not Equal To     | `6 !== 7`       |
| >        | Greater Than     | `10 > 1`        |
| <        | Less Than        | `3 < 5`         |
| >=       | Greater or Equal | `5 >= 5`        |
| <=       | Less or Equal    | `2 <= 2`        |


#### Logical Operators:
| Operator | Name | Example           |
| -------- | ---- | ----------------- |
| &&       | AND  | `true && false`   |
| \|\|     | OR   | `true \|\| false` |
| !        | NOT  | `!true`           |

### `if/else` statements

- 3 parts: `if`, `else if`, `else`.
- Starts checking at the top and stops checking when it hits a match.
- Only 1 block is executed, so be specific first and get more general.

**Example:**

```javascript
const happyBirthday = (age) => {
  if (age === 25) {
    return "Happy birthday, you can rent a car!";
  } else if (age < 25) {
    return "No rental cars on your birthday yet.";
  } else {
    return "Enjoy car rentals on your birthday!";
  }
};
```

### Coercions and Truthiness

- Type Coercion — JavaScript will automatically convert (coerce) types when necessary. For example, `'1' + 1` results in `'11'`.
- Strict Equality (`===`) — Strict equality is used to avoid type coercion, ensuring both value and type must match for a comparison to be true. This is preferred over loose equality (`==`), which allows type coercion.
- Truthy/Falsy Values — A type of coercion where non-boolean values are coerced into `true` or `false` in contexts like conditionals. For example, `0`, `""`, `null`, `undefined`, and `NaN` are considered falsy, while all other values are truthy

## Simplify Your Logic

### Don’t nest too much

- Code should be readable.
- Avoid deeply nested `if/else` statements.
- Use guard clauses and ternarys.
  - A guard clause is an if statement that returns before subsequent has a chance to be executed.

#### Example of nested vs. simplified code:

```javascript
const doAdminThings = (role) => {
  if (role === "admin") {
    console.log("You can do admin things");
  } else {
    console.log("Nothing to do");
  }
};

const doAdminThingsBetter = (role) => {
    // Guard clause: If role is not 'admin', exit early
  if (role !== "admin") return console.log("Nothing to do");
  console.log("You can do admin things");
};
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

- `typeof` is used to check types. It’s an operator, not a function.
- Example usages:

```javascript
console.log(typeof "hi"); // string
console.log(typeof 4); // number
console.log(typeof true); // boolean
```

## Math Operations

- Use basic math operators: `+`, `-`, `/`, `*`.
- The power operator `x ** y` raises x to the power of y.
- `%` is modulo, meaning remainder division.

**Example:**

```javascript
Math.round(2.5); // 3
Math.floor(2.99); // 2
Math.ceil(2.01); // 3
```

```javascript
console.log(5 % 2); // 1 (5 divided by 2 is 2 with a remainder of 1)
console.log(10 % 3); // 1 (10 divided by 3 is 3 with a remainder of 1)
console.log(15 % 5); // 0 (15 divided by 5 is 3 with no remainder)
```
