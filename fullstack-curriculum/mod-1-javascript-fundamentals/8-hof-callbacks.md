{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-0-hof-callbacks)!
{% endhint %}

# Higher-Order Functions and Callbacks

**Table of Contents:**
- [First, Something Kinda Neat!](#first-something-kinda-neat)
- [What is a Higher-Order Function?](#what-is-a-higher-order-function)
- [A Basic higher-order Function and Callback Example](#a-basic-higher-order-function-and-callback-example)
  - [Examples using `setTimeout` and `setInterval`](#examples-using-settimeout-and-setinterval)
- [Sorting](#sorting)
- [Array Iterators: forEach, filter, and map](#array-iterators-foreach-filter-and-map)
  - [forEach](#foreach)
  - [filter](#filter)
  - [map](#map)

## First, Something Kinda Neat!

## What is a Higher-Order Function?

In the past, we have compared functions to recipes. In both, they are a series of instructions to execute. There may also be opportunities to customize the ingredients (parameters) to change the end result of the dish (the returned value).

Now, imagine that you have a personal chef. They can cook anything you want, all you had to do is give them a recipe and they will add their "special sauce". If a recipe is a function, then the chef that can make any recipe is a "higher-order" function.

> A **higher-order function (HOF)** is a function that takes in and/or returns a function

Another example of "higher-order" function is a 3D printer. When operating a 3D printer, you provide instructions to print your designed object (the callback function) and the 3D printer executes those instructions for you.

![A gif of a 3d printer making a pumpkin](img/3d-printer.webp) 

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

## Array Iterators: forEach, filter, and map

The most commonly used and perhaps most powerful higher-order functions are these "iterator" array methods:
* `arr.forEach` — useful for performing some repetitive task for each value in the source array
* `arr.filter` — useful for testing every value in the source array and keeping only the values that pass the test (e.g. only the even numbers in a numbers array)
* `arr.map` — useful for transforming every value in the source array into another value (e.g. capitalize every string in an array of sentences);

Let's take a look at each of them. We can see how they are used and how we can create our own versions of them.

### forEach

`arr.forEach(callback)` iterates over the source array and invokes the given `callback` with each `value`, that value's `index`, and the source array `arr` as inputs.

**Examples:**

In this example, we want to print a message about each value and its location in the `fruits` array:

{% code title="3-forEach.js" %}
```javascript
const fruits = ['apples', 'bananas', 'cherries'];

// Invoke `printLoudly(fruits[i], i, fruits)` for every value in fruits
const printLoudly = (value, index, arr) => {
  console.log(`${value}!!! at index ${index} in array [${arr}]`);
};
fruits.forEach(printLoudly);
```
{% endcode %}

In this example, we provide an anonymous inline function. We have chosen to ignores the `index` and `arr` inputs

{% code title="3-forEach.js" %}
```js
fruits.forEach(value => console.log(value.toUpperCase()));
```
{% endcode %}

**Challenge:** 

Implement your own `forEach` function that takes an `array` and a `callback` and invokes the `callback` on every value in the `array`. The callback should be invoked with 3 inputs for every value:
1. The current `value`
2. The current value's `index` in the source `array`
3. The source `array` itself

Nothing is returned.

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
  for (let i = 0; i < array.length; i++) {
    callback(array[i], i, array);
  }
}
```

</details>


### filter

`arr.filter(callback)` iterates over the source array, invoking the given `callback` with each `value`, `index`, and the source array `arr` as inputs. If the `callback` returns `true`, the `value` is added to an array which is returned.

**Examples:**

In this example, we want to know how many scores in an array of numbers are greater than or equal to 75. Using `arr.filter`, we can get a copy of the array containing only those passing scores and then read its length.

{% code title="4-filter.js" %}
```javascript
const scores = [100, 85, 90, 70, 74];

// We only care about the value, the index and arr can be ignored
const isPassing = (score) => {
  return score >= 75;
}

// This is a copy of scores that has been filtered.
const passingScores = scores.filter(isPassing);

// Using its length, we can get the number of passing scores
console.log(`There were ${passingScores.length} passing scores`);
```
{% endcode %}

In this example, we will have an array of user objects. We want to get a copy of the array that only contains admins. Filter lets us check each object and only those with `isAdmin: true` will pass the test and be returned in the new array.

{% code title="4-filter.js" %}
```js
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];

const admins = users.filter(user => user.isAdmin);

console.log(admins);
/* 
[
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
];
*/
```
{% endcode %}

**Challenge:** 

Implement your own `filter` function that takes an `array` and a `test` callback and returns an array. The `test` callback should be invoked with 3 inputs for every value:
1. The current `value`
2. The current value's `index` in the source `array`
3. The source `array` itself

The `test` callback should be expected to return `true` or `false`. If `test` returns `true`, the value it was called on should be added to the returned array.

```js
const filter = (array, test) => {
  // ??? 
};

const result = test([1,2,3,4,5], value => value >= 3);
console.log(result);
// [3,4,5]
```

**<details><summary>Solution</summary>**

```js
const filter = (array, test) => {
  // Create a new array to be returned
  const filtered = [];
  for (let i = 0; i < array.length; i++) {
    // If array[i] passes the test, add it to filtered
    if (test(array[i], i, array)) {
      filtered.push(array[i]);
    }
    // Otherwise, continue
  }
  // Return the filtered values only.
  return filtered;
}
```

</details>

### map

`arr.map(callback)` iterates over the source array, invoking the given `callback` with each `value`, `index`, and the source array `arr` as inputs. The value returned by the `callback` is added to an array which is returned.

**Examples:**

In this example, we have an array `inchesArr` containing values each representing a number of inches. Using `arr.map`, we transform each `inches` value into a string in the format `x feet y inches`. Each of these new strings is added to the array returned by `map` and stored in `feetAndInches`:

```js
const inchesArr = [70, 55, 62, 12, 36];
const feetAndInches = inchesArr.map(inches => {
  const feet = Math.floor(inches / 12);
  const remainingInches = inches % 12;
  return `${feet} feet ${remainingInches} inches`;
});

console.log(feetAndInches);
```

**Challenge:** 

Implement your own `map` function that takes an `array` and a `modify` callback and returns an array. The `modify` callback should be invoked with 3 inputs for every value:
1. The current `value`
2. The current value's `index` in the source `array`
3. The source `array` itself

The `modify` callback should be expected to return a value that should be added to the returned array.

```js
const map = (array, modify) => {
  // ??? 
};

const result = test([1,2,3,4,5], value => value * 2);
console.log(result);
// [2,4,6,8,10]
```

**<details><summary>Solution</summary>**

```js
const map = (array, modify) => {
  const mapped = [];
  for (let i = 0; i < array.length; i++) {
    const result = modify(array[i], i, array);
    mapped.push(result);
  }
  return mapped;
}
```

</details>