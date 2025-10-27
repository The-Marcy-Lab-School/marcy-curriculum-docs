# Higher-Order Functions and Callbacks

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-0-hof-callbacks)!
{% endhint %}

**Table of Contents**:

- [Summary](#summary)
- [Functions are "First-Class" Values](#functions-are-first-class-values)
  - [Functions Stored in Objects are Referred to as "Methods"](#functions-stored-in-objects-are-referred-to-as-methods)
  - [Functions Passed into Other Functions Are "Callbacks". The Function That Receives the Callback is a "Higher Order Function".](#functions-passed-into-other-functions-are-callbacks-the-function-that-receives-the-callback-is-a-higher-order-function)
  - [Some Fun Examples](#some-fun-examples)
  - [Do Not Invoke The Callback](#do-not-invoke-the-callback)
- [Array Iterators](#array-iterators)
  - [forEach](#foreach)

## Summary

* **Functions are "first-class" values** meaning they can be stored in variables and data structures (arrays/objects), passed to functions as arguments, and returned from functions.
* A **method** is a function stored in an object.
* **Higher-order functions (HOFs)** are functions that accept other functions as input and/or return functions, enabling powerful programming patterns and code reuse.
* **Callback functions** are functions passed as arguments to higher-order functions, allowing you to customize behavior without modifying the HOF itself.
* When passing callbacks to HOFs, avoid invoking them (don't use parentheses) - the HOF will handle the invocation with the correct parameters.
* **Inline callback functions** (anonymous arrow functions) provide a concise way to define callbacks when they won't be reused elsewhere.
* Common HOF examples include `setTimeout`, `setInterval`, and array methods like `forEach`, which demonstrate how callbacks enable asynchronous and iterative programming.
* Understanding HOFs and callbacks is fundamental to modern JavaScript development and functional programming concepts.

## Functions are "First-Class" Values

Pop Quiz! Identify the data types stored in each variable below:

```js
const a = 'hello world';
const b = 42;
const c = true;
const d = [1, 2, 3];
const e = { name: 'Ada' };
const f = () => { 
  console.log("hello world"); 
};
```

**<details><summary>Answers</summary>**
1. String
2. Number
3. Boolean
4. Array
5. Object
6. Function
</details>

What we can see from this is that functions are just another type of data and, like other dat types, it can be stored in a variable. This ability to be stored in a variable is the first quality that describes **functions as "first class" values**.

### Functions Stored in Objects are Referred to as "Methods"

Functions can also be stored in objects and, less commonly, arrays. When stored in an object, we refer to the function as a **method**.

```js
const methods = {
  sayHi: () => {
    console.log("Hi");
  },
  greet: (name) => {
    console.log(`Hello ${name}`);
  },
  isEven: (number) => {
    return number % 2 === 0;
  }
};

methods.sayHi();                // Hi
methods.greet('Ada');           // Hello Ada
console.log(methods.isEven(4)); // true
```

Try defining your own method called `isOdd` and test it out!

### Functions Passed into Other Functions Are "Callbacks". The Function That Receives the Callback is a "Higher Order Function".

Here is a "normal" function that takes in a string as an argument:

```js
const greet = (name) => {
  console.log(`Hello ${name}`);
}

greet('Ada');  // Passing a string as an argument
```

Just like strings, functions can be passed to other functions as arguments. In these cases, we call the function argument a **"callback function"** and the function receiving the callback is a **"higher order function"**

```js
// First, we define the callback
const tick = () => {
  console.log("Tick");
}
setInterval(tick, 1000); // This higher order function takes in a callback and a number

// setTimeout also takes in a callback and a number. 
// Here, we define the callback "anonymously" or "inline"
setTimeout(() => {
  console.log("10 seconds have passed");
}, 10000);
```

Two classic examples of higher-order functions that are built into Node are:
1. `setTimeout(callback, delay)`
2. `setInterval(callback, delay)`

Each function takes in a `callback` function and a `delay` number as arguments and then invoke the `callback` after `delay` milliseconds have passed.

**Q: Can you tell the difference between how `setTimeout` and `setInterval` work?**

**<details><summary>Answer</summary>**

`setTimeout` only invokes the callback once whereas `setInterval` will continuously invoke the callback.

</details>

### Some Fun Examples

This one creates a "loading wheel" animation:

{% code title="2-fun-examples.js" %}
```javascript
const chars = ["\\", "|", "/", "-"];
let i = 0;

// empty the console each time and print the next character in the sequence
const loopThroughChars = () => {
  console.clear()
  console.log(chars[i]);
  i++;
  if (i >= 4) i = 0;
};

setInterval(loopThroughChars, 250);
```
{% endcode %}

This one animates an alien that bounces across the screen:

{% code title="2-fun-examples.js" overflow="wrap" %}
```javascript
let str = '👾';
let forward = true;

const animateAlien = () => {
  // console.clear();   // uncomment this for a single-row experience
  console.log(str);

  if (forward) {
    // add white space to the front
    str = ' ' + str;
  } else {
    // remove 1 white space from the front
    str = str.slice(1);
  }

  // Turn around when reaching either side of the terminal
  const terminalWidth = process.stdout.columns;
  if (str.length === terminalWidth || str.length === 2) {
    forward = !forward;
  }
};
```
{% endcode %}

### Do Not Invoke The Callback

When passing in a callback to a higher-order function, avoid invoking the callback. 

In this example, since `executeCallback` is the higher-order function, it will invoke the callback on our behalf. Invoking the callback will produce an error:

```js
executeCallback(sayHello);
// hello world

executeCallback(sayHello());
// TypeError: callback is not a function
```

<details>
<summary>Q: Why does an error get thrown? Why is it saying callback is not a function</summary>

When you invoke `executeCallback(sayHello())`, the `sayHello()` function call gets resolved first. Since `sayHello()` returns `undefined`, that is what `executeCallback` gets as its callback:

```js
sayHello() // this gets resolved first and returns undefined
executeCallback(undefined) // undefined is not a function
```

</details>

## Array Iterators

The most commonly used higher-order functions are these "iterator" array methods:

| Method            | Description  (For every value in the source array...)                       | Example(s)                                                             |
| ----------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `array.forEach`   | Perform a task that produces a side effect using the value                  | Mutate each value in the array or print each value to the console      |
| `array.filter`    | Test the value and add it to a new array if it passes the test              | Keep only the even numbers in the source array                         |
| `array.map`       | Transform the value and add it to a new array                               | Double every number in the source array                                |
| `array.find`      | Test the value and return the first value that passes the test              | Find the first number in an array that is greater than 10              |
| `array.findIndex` | Test the value and return the index of the first value that passes the test | Find the index of the first number in an array that is greater than 10 |
| `array.reduce`    | Determine how it can be combined with the other values in the array         | Calculate the sum of all numbers in an array                           |
| `array.sort`      | Compare it with another value to determine which should go first            | Sort an array of numbers in descending order                           |

In the next lesson, we'll learn more about these but for now, let's focus on `forEach`

### forEach

`arr.forEach(callback)` iterates over the source array and invokes the given `callback` with each `value`, that value's `index`, and the source array `arr` as inputs.

Use `forEach` to execute callbacks that produce side-effects (like printing to the console or mutating values).

In this example, we want to print a message about each value and its location in the `fruits` array:

{% code title="2-forEach.js" %}
```javascript
const fruits = ['apples', 'bananas', 'cherries'];

// print every fruit and its position in the array!
fruits.forEach((input, i, arr) => {
  console.log(`${input}!!! at index ${i} in array [${arr}]`);
});

// Here we only use the value parameter
fruits.forEach((value) => {
  console.log(`${input}!!!`);
});
```
{% endcode %}

Remember, `array.forEach` is used for callbacks that result in a side effect. For example, mutating the contents of an array is considered a side effect. In this example, we use `user.forEach` to change the `isAdmin` property of every object in the `users` array:

{% code title="2-forEach.js" %}
```js
// revoke isAdmin status from all users
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];
users.forEach((user) => user.isAdmin = false)
```
{% endcode %}

**Challenge 1:**

Use `users.forEach` to update the `username` property for each object in the `users` array such that the first letter is capitalized.

**<details><summary>Solution</summary>**

```js
users.forEach((user) => user[0].toUppercase() + user.slice(1));
```
</details>


**Challenge 2:**

Implement your own `forEach` function that takes an `array` and a `callback`:

It should iterate through the input `array` and do the following:
* Invoke the `callback` with the following arguments:
  * The value at the current index
  * The current index
  * The source array itself
* Return nothing (or manually return `undefined`)

**Usage Example**
```js
const forEach = (array, callback) => {
  // ??? 
};

forEach(['a','b','c'], console.log);
/* 
a 0 [ 'a', 'b', 'c' ]
b 1 [ 'a', 'b', 'c' ]
c 2 [ 'a', 'b', 'c' ]
*/
```

**<details><summary>Solution</summary>**

```js
const forEach = (array, callback) => {
  // Iterate through the input array
  for (let i = 0; i < array.length; i++) {
    callback(array[i], i, array);
  }
}
```
</details>