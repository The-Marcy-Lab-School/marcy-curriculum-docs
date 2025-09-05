# Data Types and Variables

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-1-data-types-variables)!
{% endhint %}

**Table of Contents:**
- [Summary](#summary)
- [Computation is All About Data](#computation-is-all-about-data)
- [Data Types](#data-types)
- [Operators](#operators)
  - [Ternary Operator](#ternary-operator)
  - [typeof Operator](#typeof-operator)
  - [Resolution Order of Operations](#resolution-order-of-operations)
- [Type Coercion \& Truthy vs. Falsy](#type-coercion--truthy-vs-falsy)
- [Variables](#variables)
  - [Using Variables: Declare, Assign, Reference](#using-variables-declare-assign-reference)
  - [Scope](#scope)
  - [The Four Ways To Declare Variables](#the-four-ways-to-declare-variables)
  - [Hoisting: Why We Don't Use `var`](#hoisting-why-we-dont-use-var)

## Summary

* **Data types** are categories of values in JavaScript. There are 5 primitive types (String, Number, Boolean, undefined, null) and 3 reference types (Object, Array, Function). Knowing the type of a value helps determines how you can use that value. Choosing the right type to represent your data is essential.
* **Operators** are symbols (e.g. `+`, `>=`, `&&`) that generate new data from existing values. Arithmetic, comparison, and logical operators allow you to perform calculations and make decisions.
* **Type coercion** is the process of converting a value from one data type to another, sometimes automatically. Values can be "truthy" or "falsy" depending on their content.
* **Variables** are named containers for data. You can reference and reassign variables to store and update information in your program.
  * There are four ways to declare variables: `const` (preferred for values that don't change), `let` (for values that do change), `var` (legacy, avoid using), and global variables (avoid using).
* **Scope** refers to where a variable is declared. It impacts the "reachability" of the variable (where it can be referenced).
* **Hoisting** allows some variables and functions to be referenced before they are declared, which can lead to confusing bugs. Prefer `const` and `let` to avoid hoisting

## Computation is All About Data

Every computational device, even the earliest device the abacus, is used to manipulate and generate data.

![The abacus is the earliest known computational device](img/abacus.png)

Our modern computers, while much more powerful, are still oriented around this singular goal: manipulating and generating data. 

What has changed and evolved over time is what we do with those calculations and how quickly our devices can perform them. Here are some modern applications of how our computers make use of data:
* **Rendering images & video**: Calculating pixel positions, color values, and frame rates turns raw math into Netflix streams and 3D games.
* **Navigation systems**: Continuous GPS data (latitude/longitude as numbers) is transformed into live maps, directions, and traffic updates.
* **Social media**: Posts are stored as text data, likes as counts, and relationships as graph data — powering your feed and recommendations.
* **Online shopping**: Prices (numbers), product info (strings), and cart contents (arrays of objects) are combined to let you browse, compare, and buy.
* **Music & audio**: Sound waves are stored as numerical data, then processed and streamed as songs, podcasts, and phone calls.
* **Healthcare & fitness apps**: Heart rate, steps, and sleep cycles (all data points) are tracked and analyzed to monitor your health.
* **Artificial intelligence**: Images, text, or speech are converted into numbers (vectors, matrices) that models process to recognize faces, generate text, or translate languages.

In this lesson, we'll learn about the different types of data, how our computers store data in variables, and then the many ways that we can compute new data.

## Data Types

All values have a **data type** that determines what you can do with that value. 

For example, you can use numbers to perform mathematical calculations and can use strings to construct messages. Knowing the different data types allows you to choose the best data type for your particular use case.

There are five main **primitive data types**: Number, String, Boolean, `undefined`, and `null`

| Data Type     | Syntax Example           | What It Represents                                          | Common Use Cases                                                      |
| ------------- | ------------------------ | ----------------------------------------------------------- | --------------------------------------------------------------------- |
| **String**    | `"hello"` or `'@abc123'` | Text (any sequence of characters)                           | Usernames, chat messages, product descriptions, search queries        |
| **Number**    | `50` or `10.5` or `-2`   | Numeric values                                              | Scores in a game, count of likes on a post, timers                    |
| **Boolean**   | `true` or `false`        | Truth value                                                 | Login status, feature toggles (dark mode on/off), win/lose conditions |
| **Undefined** | `undefined`              | A variable that has been declared but not yet given a value | “Not yet set” values                                                  |
| **Null**      | `null`                   | Intentional absence of a value                              | Representing “no result”                                              |

There are also three **reference data types**: Objects, Arrays, and Functions.

| Reference Type | Syntax Example                             | What It Represents              | Common Use Cases                                               |
| -------------- | ------------------------------------------ | ------------------------------- | -------------------------------------------------------------- |
| **Object**     | `{ name: "Alex", age: 25 }`                | A collection of data relating to one "thing" | Storing user profile data, storing data about product (name, description, price, etc...) |
| **Array**      | `[ "apple", "banana", "cherry" ]`          | An ordered list of similar values       | Storing lists (shopping cart items, game high scores, chat messages)  |
| **Function**   | `function greet() { console.log("Hi!"); }` | A reusable block of code        | Performing actions, handling events, calculations              |

## Operators

Now that we know the types of data, let's use our computers to generate data with **operators**!

An operator is a symbol that uses data values to generate a new piece of data, forming an **expression**:

```js
5 + 2; // the value 7 is created by the + operator in this expression
```

Let's start by reviewing the basic categories of operators: arithmetic, comparison, and logical.

**Challenge:** Can you tell what each expression will produce?

```javascript
// Arithmetic Operators
console.log("Arithmetic Operators:");
console.log(5 + 2);   // Addition
console.log(5 - 2);   // Subtraction
console.log(5 * 2);   // Multiplication
console.log(5 / 2);   // Division
console.log(5 % 2);   // Modulus (remainder)
console.log(5 ** 2);  // Exponentiation

// Comparison Operators
console.log("\nComparison Operators:");
console.log(5 > 2);     // x greater than y
console.log(5 < 2);     // x less than y
console.log(5 >= 5);    // x greater than or equal to y
console.log(5 <= 4);    // x less than or equal to y
console.log(5 == "5");  // x is loosely equal to y
console.log(5 === "5"); // x is strictly equal to y
console.log(5 != 2);    // x is loosely not equal to y
console.log(5 !== "5"); // x is strictly not equal to y

// Logical Operators
console.log("\nLogical Operators:");
console.log(true && false); // And
console.log(true || false); // Or
console.log(!true);         // Not
```

### Ternary Operator

The ternary operator `boolExpression ? valA : valB` produces one of two values based on the `boolExpression` value:
* `valA` is produced if the `boolExpression` evaluates to `true`
* `valB` is produced if the `boolExpression` evaluates to `false`

```js
console.log(5 % 2 === 0 ? "it is even!" : "it is odd!");
```

### typeof Operator

The `typeof` operator returns the type of a given value as a string.

```javascript
console.log(typeof "hi");         // Prints "string"
console.log(typeof 4);            // Prints "number"
console.log(typeof NaN);          // Prints "number"
console.log(typeof true);         // Prints "boolean"
console.log(typeof undefined);    // Prints "undefined"
console.log(typeof console.log);  // Prints "function"

// Beware: objects, null, and arrays all show up as "object"
console.log(typeof {});     // Prints "object"
console.log(typeof null);   // Prints "object"
console.log(typeof []);     // Prints "object"
```

### Resolution Order of Operations

Operators can be combined to form complex expressions. In these cases, understanding the order in which operators are evaluated — also called **operator precedence** — is crucial.

Let's look at this code snippet as an example, what is the value of `result`?:

```js
const result = Math.random() > 0.5 ? "Heads" : "Tails";
```

{% hint style="info" %}

In JavaScript `Math` is a globally available object with methods and properties for doing various mathematical calculations. `Math.random()` returns a random number that is `≥0` and `<1`

{% endhint %}

Below is the order of precedence:
1. Function calls are evaluated first
2. Then arithmetic operators. `*`, `/`, `%` are evaluated before `+` and `-`.
3. Followed by comparison operators. Relational operators (`<`, `<=`, `>`, `>=`) are evaluated before equality operators (`===`, `!==`, `==`, `!=`)
4. Then logical operators. AND (`&&`) has higher precedence than logical OR (`||`).
5. Ternary operator (`x ? y :z`) and assignment happens last (`=`)

Parentheses `()` can always be used to override the default order and make the evaluation order explicit.

```js
const result = Math.random() > 0.5 ? "Heads" : "Tails";
```

**<details><summary>Q: So, what is the order of operations? What is the value stored in result?</summary>**

The order of operations is as follows:
1. `Math.random()` resolves to a random number between 0 and 1.
2. `Math.random() > 0.5` resolves to `true` or `false` based on the value from step 1.
3. `Math.random() > 0.5 ? "Heads" : "Tails"` resolves to `"Heads"` or `"Tails"` based on the result from step 2.
4. `const result = Math.random() > 0.5 ? "Heads" : "Tails"` --> assigns the `"Heads"` or `"Tails"` to the variable `result` based on step 3.

</details>

**Challenge**: what values are produced by the code snippet below?

```js
// Arithmetic
console.log(5 + 2 * 3);  
console.log((5 + 2) * 3);

// Comparison
console.log(5 + 2 > 6);     
console.log(5 + (2 > 6));   

// Logical
console.log(true || false && false);    
console.log((true || false) && false);  

// Combined
console.log(5 > 3 && 2 + 2 === 4);     
console.log((5 > 3 && 2 + 2) === 4);
console.log(Math.ceil(Math.random() * 6))
```

**<details><summary>Answers</summary>**

```js
// Arithmetic
console.log(5 + 2 * 3);       // 11 -> Multiplication before addition
console.log((5 + 2) * 3);     // 21 -> Parentheses override precedence

// Comparison
console.log(5 + 2 > 6);       // true -> Addition happens before comparison
console.log(5 + (2 > 6));     // 5 -> Comparison happens first due to parentheses

// Logical
console.log(true || false && false);     // true -> && has higher precedence than ||
console.log((true || false) && false);   // false -> Parentheses change the evaluation order

// Combined
console.log(5 > 3 && 2 + 2 === 4);       // true -> Arithmetic and comparison combined with logical
console.log((5 > 3 && 2 + 2) === 4);     // true -> Parentheses change how the logical operation is grouped
```
</details>

## Type Coercion & Truthy vs. Falsy

We can "coerce" a value of one data type to become another data type using type casting functions:

```js
// EVerything can easily be turned into a string
console.log(String(1))          // -> "1"
console.log(String(true))       // -> "true"

// Not everything becomes a number in a nice way
console.log(Number(false))      // -> 0
console.log(Number("hello"))    // -> NaN (not a number)

// Values that are "non-values" or "empty values" are considered "falsy"
// All other values are "truthy"
console.log(Boolean(0))         // -> false
console.log(Boolean(''))        // -> false
console.log(Boolean(null))      // -> false
console.log(Boolean(undefined)) // -> false
console.log(Boolean(100))       // -> true
console.log(Boolean('hello'))   // -> true
```

In some cases, type coercion happens automatically, potentially without us knowing.

For example, what is the result of the expression below? What data type is produced? Do you think we should even be allowed to do something like this?

```js
console.log("1" + 1);
```

**<details><summary>Answer</summary>**

`"1" + 1;` produces the string `11` because the number `1` is coerced into a string.

Depending on your perspective, you may think that it is nonsensical to allow this kind of operation. Why would anyone want to add a string and a number in this way? 

Throwing an error in this case would alert the programmer to the problem so that they can fix it. If this is the intention, then the program should be written with more explicit coercion.

```js
console.log("1" + String(1));
```
</details>

## Variables

A **Variable** in JavaScript is a named container for data. By labeling our data, variables enable us to perform a series of computations in a program and "save" our progress along the way. Descriptive variable names dramatically improve the readability of our code.

Consider the two approaches below to produce the same result. The first does not use variables while the second does. What are the tradeoffs of each approach?

**Approach 1: No Variables**

```js
console.log(`The sum of 4 + 3 + 2 + 1 is ${4 + 3 + 2 + 1} and the average is ${(4 + 3 + 2 + 1) / 4}`);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5
```

**Approach 2: Variables**

```js
const sum = 4 + 3 + 2 + 1;  
const average = sum / 4;   
const message = `The sum of 4 + 3 + 2 + 1 is ${sum} and the average is ${average}`;
console.log(message);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5
```

**<details><summary>Tradeoffs of each approach</summary>**

The first approach can be written in one line, however, it is very long. This makes it more difficult to read and predict the output. Additionally the `4 + 3 + 2 + 1` expression must be calculated twice since that calculation is not saved anywhere.

With variables, each expression is nicely labeled for readability and comprehension. Additionally, the calculation for the sum is stored in the `sum` variable allowing us to use it to calculate the `average` and to help construct the `message`. However, more lines of code are used. 

In most cases, the benefits of readability, comprehension, and minimizing repetition outweigh the extra space taken up in a file.

</details>

### Using Variables: Declare, Assign, Reference

There are three things we can do with variables:
* Create a new variable ("declare the variable"). Use `let` or `const`.
* Give the variable a new value to hold ("assign the variable a value"). Only possible with `let` variables.
* Use the value held by the variable ("reference the variable")

```js
// Declare a variable without an initial value
let count;

// Assigning the variable the value 0
count = 0;

// Reference the count variable to access its current value, 0
console.log(`count starts at ${count}`);

// Reassign the variable to hold a new value: 1
count = 1;

// Reference the count variable again to access its current value, 1
console.log(`count is now ${count}`); 
```

We can even reference the current value of a variable to calculate the new value we assign to it:

```js
// The right side of `=` resolves before reassignment. Count is 2
count = count + 1;

// The "increment" operator is the same as `count = count + 1`. Count is 3
count++;

// The "addition assignment" operator is the same as `count = count + 3`. Count is 6
count += 3;

console.log(`count is now ${count}`); // Output: count is now 6
```

**<details><summary>Q: Can you declare a `const` variable without an initial value? Why or why not?</summary>**

You cannot! Since a `const` variable cannot be assigned a new value, it must be given a value immediately when declared otherwise it will forever be `undefined` which isn't very useful. Doing so will throw a `SyntaxError`:

```js
let y; // Totally fine since we can change the value assigned to a let variable.

const x; // Uncaught SyntaxError: Missing initializer in const declaration
```

</details>

### Scope

**Scope** refers to where a variable is declared. The scope of the variable impacts the "reachability" of the variable (where it can be referenced).

For example, a variable declared in the **scope** of a function can only be referenced within that function. It is not reachable outside of the function.

```js
const printX = () => {
  // x is reachable anywhere in this function
  const x = 10;

  console.log(x); 
}
printX(); 

console.log(x); // ReferenceError: x is not defined
```

Variables can be declared at the following levels of scope, listed from the least reachability to the most reachability:
* **Block-Scoped Variables** — Variables declared within a "block statement" `{}` (e.g. `if`, `if else`, `else`, `for`, `while`, etc...).
* **Function-Scoped Variables** — Variables and parameters declared within a function.
* **Module-Scoped Variables** — Variables declared within a file and outside of any function.
* **Global-Scoped Variables** — Variables provided by the runtime environment (Node, the Browser). These are things like the `global` and `window` objects. Avoid adding data to these objects.
 
Scopes can contain other scopes. For example, a file can contain a function that has an `if` block statement inside. Variables declared in the "outer" scope can be referenced anywhere within the "inner" scopes, but not vice-versa.

```javascript
// myName is module-scoped. Reachable anywhere in this file.
const myName = 'John';

const sayHi = () => {
  // `myName` is reachable anywhere within this file, even in lower scopes like in this function
  console.log(`Hi, my name is ${myName}`)
}

const greetFriend = (friend) => {
  // Parameters like `friend` are function-scoped. Reachable anywhere in this function.

  if (!friend) {
    // message is block-scoped. Reachable only in this block.
    const message = "I can't say hi if I don't know your name!";
    console.log(message);
  } else {
    // message is block-scoped. Reachable only in this block.
    const message = `Hi, ${friend}, I'm ${myName}. Nice to meet you.`;
    console.log(message);
  }
}

greetFriend("");
// Output: I can't say hi if I don't know your name!

greetFriend('Jane');
// Output: "Hi, Jane, I'm John. Nice to meet you."

// We get ReferenceErrors if we try to reference variables out of scope.
console.log(message, myName);
```

When a variable that is out of scope is referenced, a `ReferenceError` is thrown.

{% hint style="info" %}
As a best practice, aim to declare variables in the lowest possible scope where the variable is needed (block or function scope rather than module or global scope). 

For example, in the code snippet above, we must declare the `myName` variable in the module scope since it is referenced in both the `sayHi` function and `greetFriend` function.

However, we can consolidate the two `message` variables in the conditional block statements and create one shared variable in the function scope. This helps us avoid repetitive code:

```js
const greetFriend = (friend) => {
  let message;
  if (!friend) {
    message = "I can't say hi if I don't know your name!";
  } else {
    message = `Hi, ${friend}, I'm ${myName}. Nice to meet you.`;
  }
  console.log(message);
}
```
{% endhint %}

**<details><summary>Q: Why is it even possible to have two variables called `message` within the `greetFriend` function?</summary>**

We can have two variables with the same name as long as they are in different scopes. This is the entire point of scope! It limits name collision errors like `Uncaught SyntaxError: Identifier 'x' has already been declared`

</details>

### The Four Ways To Declare Variables
There are 4 ways to declare a new variable, *but only the first two of should be used*:
 1. `const` declares a block-scoped variable that cannot be reassigned. This should be your go-to variable declaration.
 2. `let` declares a re-assignable block-scoped variable. Use only when reassignment is necessary.
 3. `var` declares a re-assignable, hoisted, function-scoped variable. **Do not use `var`**. Hoisting and function-scoped variables can cause unexpected behavior (read more below).
 4. Global-scoped variables are declared without a keyword. **Do not use global variables**.

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

### Hoisting: Why We Don't Use `var`

{% hint style="info" %}
**TLDR:** Don't use `var` or `function`. Do not reference a variable or function before it has been declared.
{% endhint %}

**Hoisting** is a feature of JavaScript that allows certain variables/functions to be referenced before they are declared.
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
