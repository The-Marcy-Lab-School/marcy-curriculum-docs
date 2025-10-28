# Array Higher Order Methods

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-10-hof-array-methods)!
{% endhint %}

**Table of Contents**

- [Summary](#summary)
- [Imperative vs. Declarative Code: Why we use Higher Order Functions](#imperative-vs-declarative-code-why-we-use-higher-order-functions)
- [Array Iterators](#array-iterators)
  - [.map(mutate)](#mapmutate)
  - [.filter(test)](#filtertest)
  - [.find(test) and .findIndex(test)](#findtest-and-findindextest)
  - [.reduce(accumulate, startingValue)](#reduceaccumulate-startingvalue)
  - [.sort(compare)](#sortcompare)
- [Tips and Tricks](#tips-and-tricks)
  - [Using All Callback Parameters](#using-all-callback-parameters)
  - [Chaining Array Methods](#chaining-array-methods)
  - [Generating a Frequency Counter with Reduce](#generating-a-frequency-counter-with-reduce)
  - [Nested Arrays](#nested-arrays)

## Summary

* **Array higher-order methods** provide declarative ways to work with arrays, making code more readable and reducing the need for explicit loops.
* **`map()`** transforms each element in an array and returns a new array with the transformed values, useful for converting data formats or applying calculations.
* **`filter()`** creates a new array containing only elements that pass a test condition, perfect for filtering data based on criteria.
* **`find()`** and **`findIndex()`** return the first element or index that passes a test, useful for searching arrays.
* **`reduce()`** combines all array elements into a single value, powerful for calculations like sums, averages, or building complex data structures.
* **`sort()`** arranges array elements in order based on a comparison function, with the callback determining the sorting logic.
* **Method chaining** allows you to combine multiple array methods for complex data transformations in a readable, functional style.

## Imperative vs. Declarative Code: Why we use Higher Order Functions

What is the value of Higher Order Functions? They allow us to write our code in a more _declarative_ manner rather than in an _imperative_ manner.

**Imperative code** provides explicit instructions for every step to complete a task. This gives us a lot of control over every step, but it takes more effort.

**Declarative code** just describes the desired solution and uses existing tools to handle the steps. While we give up control to the existing tool, we save time and effort.

For example, suppose we want to double every value in an array. We can either write out every single step imperatively, or we can declaratively describe the desired outcome using `array.map`
```js
// Imperative: High Control & High Effort:
const nums = [1, 5, 10, 20];

const doubleAllNums = (arr) => {
  const newArr = [];
  for (let i = 0; i < arr.length; i++) {
    const result = arr[i] * 2
    newArr.push(result)
  }
  return newArr;
}
const doubledImperatively = doubleAllNums(nums);
console.log(doubledImperatively);

// Declarative: Low Control & Low Effort:
const doubledDeclaratively = nums.map((num) => num * 2);
console.log(doubledDeclaratively);
```

Let's look at how we can use some more array higher-order methods to write more declarative code.

## Array Iterators

The most commonly used higher-order functions are these "iterator" array methods:

| Method            | Description  (For every value in the source array...)                       | Example(s)                                                             |
| ----------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `array.forEach`   | Perform a task that produces a side effect using the value                  | Mutate each value in the array or print each value to the console      |
| `array.map`       | Transform the value and add it to a new array                               | Double every number in the source array                                |
| `array.filter`    | Test the value and add it to a new array if it passes the test              | Keep only the even numbers in the source array                         |
| `array.find`      | Test the value and return the first value that passes the test              | Find the first number in an array that is greater than 10              |
| `array.findIndex` | Test the value and return the index of the first value that passes the test | Find the index of the first number in an array that is greater than 10 |
| `array.reduce`    | Determine how it can be combined with the other values in the array         | Calculate the sum of all numbers in an array                           |
| `array.sort`      | Compare it with another value to determine which should go first            | Sort an array of numbers in descending order                           |

### .map(mutate)

`arr.map` iterates over the source array, invoking the given `callback` with each `value`, `index`, and the source array `arr` as inputs. The value returned by the `callback` is added to an array which is returned.

Use `map` when you want a copy of the source array with each value converted to a new value.

**Example:** In this example, we have an array `inchesArr` containing values each representing a number of inches. Using `arr.map`, we transform each `inches` value into a string in the format `x inches => y feet z inches`. Each of these new strings is added to the array returned by `map` and stored in `feetAndInches`:

{% code title="4-map.js" %}
```js
const inchesArr = [70, 55, 62, 12, 36];

// This example converts inches to feet and inches strings
const getFeetAndInchesStr = inches => {
  const feet = Math.floor(inches / 12);
  const remainingInches = inches % 12;
  return `${inches} inches => ${feet} feet ${remainingInches} inches`;
}
const feetAndInches = inchesArr.map(getFeetAndInchesStr);
console.log(feetAndInches);
```
{% endcode %}

**Challenge:** In this example, we have a `users` array full of user objects. Write a callback for `array.map` that extract all of the usernames into a new array:

{% code title="4-map.js" %}
```js
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];
const usernames = users.map(/* ??? */);
```
{% endcode %}

**<details><summary>Solution</summary>**
```js
const usernames = users.map(user => user.username);
```
</details>

### .filter(test)

`arr.filter` iterates over the source array, invoking the given `callback` with each `value`, `index`, and the source array `arr` as inputs. If the `callback` returns `true`, the `value` is added to an array which is returned.

Use `filter` when you want a copy of the source array with unwanted values removed.

**Example:** In this example, we want to know how many scores in an array of numbers are greater than or equal to 75. Using `arr.filter`, we can get a copy of the array containing only those passing scores and then read its length.

{% code title="3-filter.js" %}
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

**Challenge:** In this example, we have an array of user objects. We want to get a copy of the array that only contains admins. 

Use filter to check each object and keep only those with the property `isAdmin: true`.

{% code title="3-filter.js" %}
```js
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];

const admins = users.filter(/* ??? */);

console.log(admins);
/* 
[
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
];
*/
```
{% endcode %}

**<details><summary>Solution</summary>**
```js
const admins = users.filter((user) => user.admin === true);
```
</details>


### .find(test) and .findIndex(test)

While `.filter` is used to get ALL values that pass a `test` callback, the `.find` and `.findIndex` methods are used to get only the first value that passes the test.

```js
const firstOddValue = nums.find((num) => num % 2);

const firstOddValueIdx = nums.findIndex((num) => num % 2);
```

Remember, the return value of the callback must return a boolean!

### .reduce(accumulate, startingValue)

Unlike the other higher-order array methods, `array.reduce()` takes in a callback `accumulate` AND a second argument `startingValue`.

It is used to "reduce" array values into a single value, built up starting with the `startingValue`.
 * The callback should accept the accumulated value so far and the current value
 * The callback should return the updated accumulated value
 * `.reduce()` returns the final accumulated value

```js
const lunchCosts = [5, 10, 7, 9, 15, 8, 12];

const totalCost = lunchCosts.reduce((accumulator, currentVal) => {
  return accumulator + currentVal
}, 0); // <-- Don't forget the starting value

console.log(totalCost);

// Use descriptive parameter names!
const totalWithBetterNames = lunchCosts.reduce((total, currentExpense) => {
  return total + currentExpense
}, 0);

console.log(totalWithBetterNames);
```

### .sort(compare)

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

## Tips and Tricks

### Using All Callback Parameters

Most of these array higher order methods will iterate through the source array and invoke the given callback with three values:
1. The current value
2. The index of the current value
3. The source array itself

```js
// Don't forget about more arguments!
const multipliedByIndexAndLength = [1, 2, 3, 4, 5]
  .map((num, idx, arr) => num * idx * arr.length);

// 1 * 0 * 5 -> [0]
// 2 * 1 * 5 -> [0, 10]
// 3 * 2 * 5 -> [0, 10, 30]
// 4 * 3 * 5 -> [0, 10, 30, 60]
// 5 * 4 * 5 -> [0, 10, 30, 60, 100]
```

### Chaining Array Methods
```js
// chaining
const myNums = [1, 2, 3, 4, 5];
const numberOfValuesBiggerThan12WhenTripled = myNums
  .map((num) => num * 3) // multiply by 3
  .filter((num) => num > 12) // keep the numbers bigger than 12
  .length // count how many there are

console.log(numberOfValuesBiggerThan12WhenTripled);
// 1
```

### Generating a Frequency Counter with Reduce

```js
const repeaters = [1, 2, 4, 2, 3, 1, 4, 6, 2];

// The starting value here is an empty {}
// 
const frequencies = repeaters.reduce((frequencies, currentNum) => {
  if (!frequencies[currentNum]) {
    frequencies[currentNum] = 1;
  } else {
    frequencies[currentNum]++;
  }
  return frequencies;
}, {});

console.log('frequencies', frequencies);
// frequencies { '1': 2, '2': 3, '3': 1, '4': 2, '6': 1 }
```

### Nested Arrays

```js
// Nested Array
const smileyFace = [
  ['-', '-', '-', '-', '-', '-', '-'],
  ['-', '-', '*', '-', '*', '-', '-'],
  ['-', '-', '-', '*', '-', '-', '-'],
  ['-', '*', '-', '-', '-', '*', '-'],
  ['-', '-', '*', '*', '*', '-', '-'],
  ['-', '-', '-', '-', '-', '-', '-'],
]

// We can nest the forEach calls
smileyFace.forEach((row, r) => {
  let str = `${r}: `;
  row.forEach(cell => {
    str += cell;
  })
  console.log(str)
});
```