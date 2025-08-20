# Data Types and Variables

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-1-data-types-variables)!
{% endhint %}

**Table of Contents:**
- [Computation is Data Generation](#computation-is-data-generation)
- [Data Types](#data-types)
- [Operators](#operators)
  - [Resolution Order of Operations](#resolution-order-of-operations)
  - [Ternary Operator](#ternary-operator)
  - [typeof Operator](#typeof-operator)
- [Type Coercion \& Truthy vs. Falsy](#type-coercion--truthy-vs-falsy)
- [Variables](#variables)
  - [Referencing and Reassigning Variables](#referencing-and-reassigning-variables)
  - [The Four Ways To Declare Variables](#the-four-ways-to-declare-variables)
  - [Hoisting: Why We Don't Use `var`](#hoisting-why-we-dont-use-var)

## Computation is Data Generation

The first computational device, the abacus, was used for making it easier to perform mathematical calculations. In other words, it was a data-generating device.

![The abacus is the earliest known computational device](img/abacus.png)

Our modern computers, while much more powerful, are still oriented around this singular goal: generating data. 

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

There are also three **reference data types**: Objects, Arrays, and Functions (we'll talk about these later on).

| Reference Type | Syntax Example                             | What It Represents              | Common Use Cases                                               |
| -------------- | ------------------------------------------ | ------------------------------- | -------------------------------------------------------------- |
| **Object**     | `{ name: "Alex", age: 25 }`                | A collection of key–value pairs | Modeling real-world entities (user profile, product, settings) |
| **Array**      | `[ "apple", "banana", "cherry" ]`          | An ordered list of values       | Storing lists of items (shopping cart, leaderboard, messages)  |
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

### Resolution Order of Operations

Operators can be combined to form complex expressions. In these cases, understanding the order in which operators are evaluated — also called **operator precedence** — is crucial.

Below is the order of precedence:
1. Arithmetic operators are evaluated first. `*`, `/`, `%` are evaluated before `+` and `-`.
2. Comparison operators are evaluated next.
3. Logical operators are last. AND (`&&`) has higher precedence than logical OR (`||`).

Parentheses `()` can always be used to override the default order and make the evaluation order explicit.

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

A **Variable** in JavaScript is a named container for data. By labeling our data, variables enable us to perform a series of computations in a program and "save" our progress along the way.

Consider the two approaches below to produce the same result. The first does not use variables while the second does. What are the tradeoffs of each approach?

```js
console.log(`The sum of 4 + 3 + 2 + 1 is ${4 + 3 + 2 + 1} and the average is ${(4 + 3 + 2 + 1) / 4}`);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5

const sum = 4 + 3 + 2 + 1;  
const average = sum / 4;   
const message = `The sum of 4 + 3 + 2 + 1 is ${sum} and the average is ${average}`;
console.log(message);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5
```

**<details><summary>Tradeoffs of each approach</summary>**

The first approach can be written in one line, however, it is very long. This makes it more difficult to read and predict the output. Additionally the `4 + 3 + 2 + 1` expression must be calculated twice.

With variables, each expression is nicely labeled for readability and comprehension. Additionally, the `sum` variable can be used to calculate the `average` and to help construct the `message`. However, more lines of code are used. 

In most cases, the benefits of readability, comprehension, and minimizing repetition outweigh the extra space taken up in a file.

</details>

### Referencing and Reassigning Variables

We can either "reference" a variable by name to access its current value or "reassign" the variable to hold a new value:

```js
// Use let because we will reassign this variable
let count = 0;

// Reference the count variable to access its current value, 0
console.log(`count starts at ${count}`);

// Reassign the variable to hold a new value: 1
count = 1;

// Reference the count variable again to access its current value, 1
console.log(`count is now ${count}`); 
```

We can even use the current value of a variable to calculate the value we reassign it to:

```js
// The right side of `=` resolves before reassignment. Count is 2
count = count + 1;

// The "increment" operator is the same as `count = count + 1`. Count is 3
count++;

// The "addition assignment" operator is the same as `count = count + 3`. Count is 6
count += 3;

console.log(`count is now ${count}`); // Output: count is now 6
```

### The Four Ways To Declare Variables
There are 4 ways to declare a new variable, *but only the first two of should be used*:
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
