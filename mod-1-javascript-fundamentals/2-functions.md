# Functions

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-functions)!
{% endhint %}

**Table of Contents:**
- [Variables Review](#variables-review)
- [Functions](#functions-1)
  - [Function Calls](#function-calls)
  - [Parameters and Arguments](#parameters-and-arguments)
  - [Return Statements](#return-statements)
    - [Function Call Resolution](#function-call-resolution)
    - [Multiple Returns](#multiple-returns)
  - [Function Scope](#function-scope)
  - [Arrow Functions vs. Function Declarations vs. Function Expressions](#arrow-functions-vs-function-declarations-vs-function-expressions)


## Variables Review

Recall that variables are named containers for data. 

**<details><summary>Q: What are the benefits of using Variables in our code?</summary>**

By labeling our data, variables improve the readability of our code and minimize the repetition of commonly used calculations.

</details>

```js
console.log(`The sum of 4 + 3 + 2 + 1 is ${4 + 3 + 2 + 1} and the average is ${(4 + 3 + 2 + 1) / 4}`);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5

const sum = 4 + 3 + 2 + 1;  
const average = sum / 4;   
const message = `The sum of 4 + 3 + 2 + 1 is ${sum} and the average is ${average}`;
console.log(message);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5
```

Suppose we wanted to sum, average, and then print the same message as above but with a different set of numbers. We could write code like this:

```js
const sum1 = 4 + 3 + 2 + 1;
const average1 = sum1 / 4;
console.log(`The sum of 4 + 3 + 2 +1 is ${sum1} and the average is ${average1}`);
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5

const sum2 = 5 + 6 + 7 + 8;
const average2 = sum2 / 4;
console.log(`The sum of 5 + 6 + 7 + 8 is ${sum2} and the average is ${average2}`);
// Output: The sum of 5 + 6 + 7 + 8 is 26 and the average is 6.5

const sum3 = -2 + -1 + 1 + 2;
const average3 = sum3 / 4;
console.log(`The sum of -2 + -1 + 1 + 2 is ${sum3} and the average is ${average3}`);
// Output: The sum of -2 + -1 + 1 + 2 is 0 and the average is 0
```

**<details><summary>Q: What isn't great about this code?</summary>**

It breaks the fundamental software engineering principle "DRY" which stands for "Don't Repeat Yourself". Instead, we should use functions!

</details>

## Functions

A **Function** is a named container for statements that can be **invoked** to execute its statements. Functions improve the readability of our code and minimize repetition of commonly used statements.

The same result can be achieved with a `printSumAndAverage` function:

```js
const printSumAndAverage = (a, b, c, d) => {
  const sum = a + b + c + d;
  const average = sum / 4;
  console.log(`The sum of ${a} + ${b} + ${c} + ${d} is ${sum} and the average is ${average}`);
}

printSumAndAverage(4, 3, 2, 1)
// Output: The sum of 4 + 3 + 2 + 1 is 10 and the average is 2.5

printSumAndAverage(5, 6, 7, 8);
// Output: The sum of 5 + 6 + 7 + 8 is 26 and the average is 6.5

printSumAndAverage(-2, -1, 1, 2);
// Output: The sum of -2 + -1 + 1 + 2 is 0 and the average is 0
```

We first create the function to contain the logic, give it a descriptive name, and then invoke the function to execute its statements with minimal repetition.

**Challenge**: Refactor this highly repetitive code for converting a temperature from fahrenheit into celsius. Make sure to give your function a descriptive name!

```js
const tempC1 = 0
const tempF1 = (tempC1 * 9 / 5) + 32;
console.log(`${tempC1}°C is ${tempF1}°F`);
// Output: 0°C is 32°F

const tempC2 = 37
const tempF2 = (tempC2 * 9 / 5) + 32;
console.log(`${tempC2}°C is ${tempF2}°F`);
// Output: 37°C is 98.6°F

const tempC3 = 100
const tempF3 = (tempC3 * 9 / 5) + 32;
console.log(`${tempC3}°C is ${tempF3}°F`);
// Output: 100°C is 212°F
```

### Function Calls

After a function is declared, the function must be **invoked** to use the function's code using the syntax:

```js
// adding parentheses after the function name invokes the function
functionName();
```

Invoking (or "calling") a function causes the program to jump to the first line of code in the function, setting it as the next line of code to be executed.

```js
console.log('This happens first');

const sayHello = () => {
  // not executed until sayHello() is invoked
  console.log('hello');
}

console.log('this happens second');

// hello is printed when sayHello() is invoked
sayHello(); 

// we can invoke a function multiple times!
sayHello();
```

Note that the order in which statements are executed in our code is not always top to bottom. Declaring the function doesn't cause the code inside to run. We only execute the code inside of `sayHello` when it is invoked a few lines later.

**Experiment**: Run this program using the debugger to see how the control flow of the program is affected by invoking a function.

### Parameters and Arguments

The function `sayHello` above has no flexibility in its functionality. It always prints `"hello"`. 

**Parameters** are variables created inside of a function that enable a function to change its behavior based on **arguments** provided in the function call.

* Parameters go in the parentheses of a function declaration (separated by commas).
* Arguments go in the parentheses of the function call (also separated by commas).
* The order of the arguments must match the order of the parentheses.

```js
// printSum can add and print any two given values
// x and y are parameters that reference the values provided when the function is called.
const printSum = (x, y) => {
  console.log(x + y);
}

// 5 and 3 are the arguments. 8 is printed
printSum(5, 3);

// This time, 10 and 2 are the arguments. 12 is printed
printSum(10, 2);

// There is no type-checking, the function will just concatenate these values
// 'hello5' is printed
printSum('hello', 5);

// If an argument is not provided, the parameter will be undefined
// undefined + undefined => NaN is printed
printSum();
```

**Challenge:** Refactor this function so that it can print any name and hobby.

```js
const sayHello = () => {
  console.log("Hi, my name is Ben. I like to code!");
}

sayHello();
```

Test your code by invoking the function with various inputs. What happens when no input is provided?

**<details><summary>Solution</summary>**

```js
const sayHello = (name, hobby) => {
  console.log(`Hi, my name is ${name}$. I like to ${hobby}!`);
}

sayHello("Ben", "rock climb");
// Output: Hi, my name is Ben. I like to rock climb!

sayHello("Gonzalo", "play the bass");
// Output: Hi, my name is Gonzalo. I like to play the bass!

sayHello();
// Output: Hi, my name is undefined. I like to undefined!
```

</details>

### Return Statements

The functions above use `console.log` to print out a result to the console, but that result can't be used later in the program. If we want a function to produce a value that we can be used outside of the function, we add a `return` statement.

A `return` statement does two things:
1. It returns a value to the location of the function call.
2. It terminates the execution of the function

```js
const add = (x, y) => {
  return x + y;
}

// The function call is replaced by its return value, resolving to `console.log(8)`
console.log(add(5, 3));
```

#### Function Call Resolution

When a function like `console.log()` has another function call inside, like `add(5,3)`, the inner function call is resolved first.

**Challenge**: What is the result of this expression? Run this with the debugger to find out!

```js
// What is the result of this code? What is the order in which these calls are resolved?
const result = add(add(1, 2), add(3,4));
console.log(result);
```

{% hint style="info" %}
Use the "step in" and "step out" buttons to go in and out of specific function calls.
{% endhint %}

#### Multiple Returns 
A function can have multiple `return` statements, but only one is ever executed. This is helpful if the value returned depends on the provided argument.

```js
const isPositiveOrNegative = (num) => {
  if (num === 0) {
    return "Neither"
  } 
  if (num > 0) {
    return "Positive"
  }
  return "Negative"
}

console.log(isPositiveOrNegative(-5)); // Output: "Negative"
console.log(isPositiveOrNegative(5)); // Output: "Positive"
console.log(isPositiveOrNegative(0)); // Output: "Neither"
```

### Function Scope

**Scope** refers to where in our code variables can be referenced. Files, functions, and code blocks `{}` each create a new scope.

There can be any number of scopes in a single file but we will describe them as either:
* **Global Scope** — Variables that are accessible across the entire file. Includes things like `console` and `Math` and variables/functions declared at the outermost scope of the file.
* **Local Scope** — Variables that are accessible only in the current function or code block.
 
```javascript
// Global Scope - friend is accessible anywhere in this file.
const friend = 'John';

const greetFriend = (myName) => {
  // The myName parameter has local/function scope. It can only be accessed inside of greetFriend
  
  // We can access the globally scoped `friend` here since we are in an "inner" scope.
  console.log(`Hi, ${friend}, I'm ${myName}`);
}

greetFriend('Jane'); 
// Output: "Hi, John, I'm Jane"

// We can't access myName because we are in an "outer" scope compared to where it is declared.
myName; // ReferenceError: myName is not defined
```

* Outer scopes cannot see variables in inner scopes.
* Inner scopes can see variables in outer scopes.
* Parameters are treated as locally scoped variables.

### Arrow Functions vs. Function Declarations vs. Function Expressions
There are many ways to create a function, but we will use **Arrow Function** syntax:

```js
// Best — Arrow Functions are stored in constant variables and aren't hoisted.
const multiply = (x, y) => {
  return x * y;
}

// Not Great — Function Declaration. It is hoisted which can cause unexpected behavior.
function add(x, y) {
  return x + y;
}


// Not Great — Function Expression. It is not hoisted but just use arrow functions!
const divide = function(x, y) {
  return x / y;
}
```

One nice feature of arrow functions is the **implicit return** syntax: if the function's body is just a `return` statement, you can omit the `{}` and the `return` keyword.

```js
// Totally Fine - An explicit return
const add = (a, b) => {
  return a + b;
}

// Better - An "implicit return"
const add = (a, b) => a + b;
```

**Challenge:** Which of these functions can use implicit returns? Refactor them!

```js
const add = (a, b) => {
  return a + b;
};

const greet = (name) => {
  return `Hello, ${name}!`;
};

const logMessage = (message) => {
  console.log(message);
  return 'message logged';
};
```