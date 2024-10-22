# Array Higher Order Methods

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-2-1-hof-array-methods)!
{% endhint %}

**Table of Contents**

- [Imperative vs. Declarative](#imperative-vs-declarative)
- [Array Iterators: forEach, filter, and map](#array-iterators-foreach-filter-and-map)
  - [forEach](#foreach)
  - [filter](#filter)
  - [map](#map)
- [Array Higher Order Methods](#array-higher-order-methods-1)
  - [.forEach(callback)](#foreachcallback)
  - [.filter(testCallback)](#filtertestcallback)
  - [.map(modifyCallback)](#mapmodifycallback)
  - [.find(testCallback)](#findtestcallback)
  - [.findIndex(testCallback)](#findindextestcallback)
  - [.reduce(accumulatorCallback, startingValue)](#reduceaccumulatorcallback-startingvalue)
- [Advanced Stuff](#advanced-stuff)


## Imperative vs. Declarative

**Imperative code** provides explicit instructions for exactly HOW to complete a task.

* High control (you write every single line)
* High effort (you write every single line)

```js
// Double every number in the given array
const doubleAllNums = (arr) => {
  const newArr = [];
  for (let i = 0; i < arr.length; i++) {
    const result = arr[i] * 2
    newArr.push(result)
  }
  return newArr;
}
const doubledNumsOld = doubleAllNums([1, 5, 10, 20]);
console.log(doubledNumsOld);
```

**Declarative code** provides the desired solution without specifying HOW to get there.
* Low control
* Low effort

```js
const doubledNums = [1, 5, 10, 20].map((num) => num * 2);
```

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

{% code title="0-forEach.js" %}
```javascript
// print every fruit loudly!
const fruits = ['apples', 'bananas', 'cherries'];
const printLoudly = (input, index, arr) => {
  console.log(`${input}!!! at index ${index} in array [${arr}]`);
}
fruits.forEach(printLoudly);

// print each fruit value capitalized
fruits.forEach(value => {
  console.log(value.toUpperCase())
});
```
{% endcode %}

In this example, we provide an anonymous inline function. We have chosen to ignore the index and source array inputs and just focus on each `user` value in the `users` array:

{% code title="0-forEach.js" %}
```js
// revoke isAdmin status from all users
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];
users.forEach(user => user.isAdmin = false)
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

{% code title="1-filter.js" %}
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

{% code title="1-filter.js" %}
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

const nums = [1, 2, 3, 4, 5];
const evens = filter(nums, value => value % 2 === 0);
console.log(evens);
// [2, 4]
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

In this example, we have an array `inchesArr` containing values each representing a number of inches. Using `arr.map`, we transform each `inches` value into a string in the format `x inches => y feet z inches`. Each of these new strings is added to the array returned by `map` and stored in `feetAndInches`:

{% code title="2-map.js" %}
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

In this example, we take a `users` array full of user objects and extract all of the usernames into an array:

{% code title="2-map.js" %}
```js
const users = [
  { id: 1, username: 'ben', isAdmin: false },
  { id: 2, username: 'maya', isAdmin: true },
  { id: 3, username: 'reuben', isAdmin: true },
  { id: 4, username: 'gonzalo', isAdmin: false },
];
const usernames = users.map(user => user.username);
```
{% endcode %}

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

const nums = [1,2,3,4,5]
const doubled = map(nums, value => value * 2);
console.log(doubled);
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

## Array Higher Order Methods

* The important ones to know are:
    * `.forEach(callback)`
    * `.map(modify)`
    * `.filter(test)`
    * `.find(test)`
    * `.findIndex(test)`
    * `.reduce(accumulator, startingValue)`
* Invoke these directly ON the Array:

```js
const nums = [1,2,3];
const doubleNums = nums.map((num) => num * 2);
```

* These are Higher Order **Methods** because they are functions stored within an Array "Object", not as a stand-alone function.

### .forEach(callback)

Iterate through an array, but don't make a copy

| Callback Should Accept                                        	| Callback Should Return 	| .forEach returns 	|
|----------------------------------------------------------------	|------------------------	|------------------	|
| The current value. Its index and the entire array are optional 	| nothing                	| undefined        	|


```js
const letters = ['a', 'b', 'c'];
const callback = (val, idx, arr) => {
  console.log('Value at index:', val);
  console.log('Current index:', idx);
  console.log('Original array:', arr);
};
letters.forEach(callback);
// Value at index: a
// Current index: 0
// Original array: [ 'a', 'b', 'c' ]
// Value at index: b
// Current index: 1
// Original array: [ 'a', 'b', 'c' ]
// Value at index: c
// Current index: 2
// Original array: [ 'a', 'b', 'c' ]
```

### .filter(testCallback)

Get ALL elements in an Array that pass a given test

| Callback Should Accept                                         | Callback Should Return                                     | .filter returns                          |
| -------------------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------- |
| The current value. Its index and the entire array are optional | a boolean used to determine if the current element is kept | a new array of matches (or an empty one) |

```js
const numbers = [1, 2, 3, 4, 5, 6, 7, 8];

const evenNumbers = numbers.filter((num) => !(num % 2));
console.log('evenNumbers', evenNumbers);
// evenNumbers [ 2, 4, 6, 8 ]

const greaterThan100 = numbers.filter((num) => num > 100);
console.log('greaterThan100', greaterThan100);
// greaterThan100 []
```

### .map(modifyCallback)

Make a copy of an array with changes to each value

| Callback Should Accept                                         | Callback Should Return                | .map returns                       |
| -------------------------------------------------------------- | ------------------------------------- | ---------------------------------- |
| The current value. Its index and the entire array are optional | modified version of the current value | the new array with all the changes |

```js
const numbers = [10, 20, 30];
const bigNumbers = numbers.map((val, idx, arr) => {
  console.log('cb: val, idx, arr', val, idx, arr);
  return val * 100;
});
// cb: val, idx, arr 10 0 [ 10, 20, 30 ]
// cb: val, idx, arr 20 1 [ 10, 20, 30 ]
// cb: val, idx, arr 30 2 [ 10, 20, 30 ]

console.log('bigNumbers: ', bigNumbers); // [1000, 2000, 3000]
```

### .find(testCallback)

Get the first matching element in an Array, or `null`

| Callback Should Accept                                         | Callback Should Return                                   | .find returns              |
| -------------------------------------------------------------- | -------------------------------------------------------- | -------------------------- |
| The current value. Its index and the entire array are optional | a boolean used to determine if the match is found or not | the found value or  `null` |

```js
const myNums = [2, 4, 7, 5];
const firstOddValue = myNums.find((num) => num % 2 === 0);
console.log('firstOddValue', firstOddValue);
// firstOddValue 7
```

Remember, the return value of the callback must return a boolean!

### .findIndex(testCallback)

Get the index of the first matching element in an Array, or `null`

| Callback Should Accept                                         | Callback Should Return                                   | .findIndex returns       |
| -------------------------------------------------------------- | -------------------------------------------------------- | ------------------------ |
| The current value. Its index and the entire array are optional | a boolean used to determine if the match is found or not | the found index or  `-1` |

```js
const firstOddValueIdx = myNums.findIndex((num) => num % 2);
console.log('firstOddValueIdx', firstOddValueIdx);
// firstOddValueIdx 2
```

### .reduce(accumulatorCallback, startingValue)

"reduce" array values into a single value.

| Callback Should Accept               | Callback Should Return                                        | .reduce returns             |
| ------------------------------------ | ------------------------------------------------------------- | --------------------------- |
| An accumulator and the current value | the next value of the accumulator (the eventual return value) | the final accumulated value |

```js
const lunchCosts = [5, 10, 7, 9, 15, 8, 12];
const startingVal = 0;
const addUpCosts = (accumulator, currentVal) => accumulator + currentVal;

const totalCost = lunchCosts.reduce(addUpCosts, startingVal);
console.log('totalCost', totalCost);
// totalCost 66

// reduce is tricky, always use good names!
const addUpCostsBetter = (total, dailyExpense) => total + dailyExpense;
```

## Advanced Stuff

```js
// Don't forget about more arguments!
const multipliedByIndexAndLength = [1, 2, 3, 4, 5]
  .map((num, idx, arr) => num * idx * arr.length);

// chaining
const myNums = [1, 2, 3, 4, 5];
const numValuesBiggerThan12WhenTripled = myNums
  .map((num) => num * 3)
  .filter((num) => num > 12)
  .length

console.log(numValuesBiggerThan12WhenTripled);
// 1

// advanced Reduce example
const repeaters = [1, 2, 4, 2, 3, 1, 4, 6, 2];
const frequencyCounter = (acc, num) => {
  acc[num] = (acc[num] || 0) + 1;
  return acc;
};

const frequencies = repeaters.reduce(frequencyCounter, {});
console.log('frequencies', frequencies);
// frequencies { '1': 2, '2': 3, '3': 1, '4': 2, '6': 1 }

// more HOMs
const hasAtLeastOneEven = myNums.some((num) => !(num % 2));
console.log('hasAtLeastOneEven', hasAtLeastOneEven);
// hasAtLeastOneEven true
```
