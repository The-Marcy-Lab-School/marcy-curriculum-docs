{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-0-hof-callbacks)!
{% endhint %}

**Table of Contents**:

- [First, Animation!](#first-animation)
- [What is a Higher-Order Function?](#what-is-a-higher-order-function)
- [A Basic higher-order Function and Callback Example](#a-basic-higher-order-function-and-callback-example)
  - [Examples using `setTimeout` and `setInterval`](#examples-using-settimeout-and-setinterval)
- [Sorting](#sorting)

# Higher-Order Functions and Callbacks

**Table of Contents:**
- [First, Animation!](#first-animation)
- [What is a Higher-Order Function?](#what-is-a-higher-order-function)
- [A Basic higher-order Function and Callback Example](#a-basic-higher-order-function-and-callback-example)
  - [Examples using `setTimeout` and `setInterval`](#examples-using-settimeout-and-setinterval)
- [Sorting](#sorting)

## First, Animation!

Check out this script below which continuously prints out a character 👾 moving back and forth across the terminal. How can something "animated" like this be created?

![A script where an alien emoji is moving back and forth across the terminal](img/8-alien-bouncing.gif)

An animation is nothing more than a series of changing images shown in rapid succession. To simulate that experience, we can write a program that executes an `animateAlien` function every 0.05 seconds which changes the position of the 👾 character on each execution.

The only way this is possible is with **higher-order functions** and **callbacks**.

## What is a Higher-Order Function?

In the past, we have compared functions to recipes. In both, they are a series of instructions to execute. There may also be opportunities to customize the ingredients (parameters) to change the end result of the dish (the returned value).

Now, imagine that you have a personal chef. They can cook anything you want, all you had to do is give them a recipe and they will add their "special sauce". If a recipe is a function, then the chef that can make any recipe is a "higher-order" function.

> A **higher-order function (HOF)** is a function that takes in and/or returns a function

Another example of "higher-order" function is a 3D printer. When operating a 3D printer, you provide instructions to print your designed object (the callback function) and the 3D printer executes those instructions for you.

![A gif of a 3d printer making a pumpkin](img/8-3d-printer.webp) 

## A Basic higher-order Function and Callback Example

> A **callback function** is the function that is provided to a higher-order function as an argument. It is invoked by the higher-order function

This higher-order function called `logAndRun` has one parameter: `func`, a callback function.
* It first turns the provided callback function `func` into a string and prints it out so we can see how the function is written.
* It then executes the callback and prints the returned value.

In this case, `func` must be a function with no parameters (this is not a requirement of all higher-order functions, just this one).

{% code title="0-intro-to-callbacks.js" lineNumbers="true" %}
```javascript
const logAndRun = (func) => {
  const funcStr = func.toString();
  console.log("Executing func: ", funcStr);

  // This is where we invoke the callback
  const returned = func();
  console.log("Returned value: ", returned);
}

// These will be the callback functions
const rollDie = () => Math.ceil(Math.random() * 6);

/* 
Here, we invoke the higher-order function with each of our callbacks.
Notice how we DO NOT include () when providing the callback
*/
logAndRun(rollDie);
logAndRun(() => {
  console.log('hi')
});
```
{% endcode %}

When using a higher-order function like `logAndRun`, we can either:
1. Create a function and store it in a variable, and then pass the variable to the higher-order function (as we've done with `rollDie`)
2. Invoke the higher-order function with an "anonymous" arrow function declared as an argument.

In both cases, we provide the function itself and **we do NOT invoke the function**. That is, we omit the `()` from the callback function. 

The higher-order function will invoke our callback!

### Examples using `setTimeout` and `setInterval`

Two classic examples of higher-order functions that are built into Node are `setTimeout(callback, delay)` and `setInterval(callback, delay)`. 

These globally available functions each take in a `callback` and a `delay` input and invoke the `callback` after `delay` milliseconds have passed.

`setTimeout` will invoke the `callback` only once. In the example below, the `sayHi` callback is invoked after 2000 milliseconds (2 seconds):

{% code title="1-setTimeout-setInterval.js" lineNumbers="true" %}
```javascript
const sayHi = () => {
  console.log('hi');
};

// we provide our function to setTimeout
// setTimeout will invoke this function after 2000ms (2 seconds)
setTimeout(sayHi, 2000);
```
{% endcode %}

`setInterval` will repeatedly invoke the `callback` function with the given `delay` in between. In the example below, the `loopThroughChars` callback function will be executed every 250 milliseconds.

{% code title="1-setTimeout-setInterval.js" lineNumbers="true" %}
```javascript
const chars = ["\\", "|", "/", "-"];
let x = 0;

// empty the console each time and print the next character in the sequence
const loopThroughChars = () => {
  console.clear()
  console.log(chars[x]);
  x++;
  if (x >= 4) x = 0
};

setInterval(loopThroughChars, 250);
```
{% endcode %}

## Sorting

Another interesting higher-order function is the array method `array.sort()`.

Given an array of numbers, `array.sort()` will sort them in ascending order, from smallest to largest. It does this **in place** which means that the source array is modified.

```js
const nums = [4, 2, 3, 5, 1];
nums.sort(); // sorts nums "in place"
console.log(nums); // [ 1, 2, 3, 4, 5 ]
```

We can change this default behavior though by providing a callback function as an argument:

```js
const nums = [4, 2, 3, 5, 1];

// a and b will be values in the source array to compare
const compareDescending = (a, b) => {
  if (a > b) {
    // put a to the left of b
    return -1;
  }
  if (a < b) {
    // put a to the right of b
    return 1;
  }
  // they are the same, order doesn't matter
  return 0;
}
nums.sort(compareDescending);
console.log(nums); // [ 5, 4, 3, 2, 1 ]
```

`arr.sort` has some particular expectations around the callback function that we provide. It should:
* Have two parameters, together representing a pair of values in the source array `arr` to compare. Let's call them `a` and `b`.
* Return `-1` (or any negative number) if value `a` is meant to go before value `b`.
* Return `1` (or any positive number) if value `a` is meant to go after after value `b`.
* Return `0` if the order of values `a` and `b` does not matter.

**Challenge**

Implement your own callback function `sortAscendingByLength` that sorts an array of strings according to their length with shorter strings coming first.

```js
const animals = ['aardvark', 'bear', 'cheetah', 'deer'];
const sortAscendingByLength = (a, b) => {
  if (???) {
    // put a to the left of b
    return -1;
  }
  if (???) {
    // put a to the right of b
    return 1;
  }
  // they are the same, order doesn't matter
  return 0;
}
animals.sort(sortAscendingByLength);
console.log(animals);
// ['bear', 'deer', 'cheetah', 'aardvark'];
```

**<details><summary>Solution</summary>**

```js
const sortAscendingByLength = (a, b) => {
  if (a.length < b.length) {
    // put a to the left of b
    return -1;
  }
  if (a.length > b.length) {
    // put a to the right of b
    return 1;
  }
  // they are the same, order doesn't matter
  return 0;
}
```

</details>