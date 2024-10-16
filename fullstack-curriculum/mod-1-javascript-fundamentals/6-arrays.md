# Arrays

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-1-1-arrays)!
{% endhint %}

## Slides

{% embed url="https://docs.google.com/presentation/d/1jbe807oy9VTKov1ADxN1NteO7lxq1nscqkWApNSdkkc/embed?start=false&loop=false&delayms=3000" %}

## Array Basics

- Arrays are lists of data (order matters). Values in an array are called **elements of the array**.
- Arrays use square brackets `[]` to encapsulate the data
- Arrays are a type of object
- Like strings arrays also...
  - have indexes starting at `0`
  - use bracket notation to access individual elements: `array[index]`
  - have a `.length` property that returns the number of elements in the array
  - have many of the same read-only methods like `slice`, `indexOf` and `includes`
- Unlike strings, arrays are mutable.

```js
const friends = ['bert', 'ernie', 'big bird', 'kermit', 'miss piggy', 'elmo'];
console.log(`typeof on an Array returns ${typeof friends} because Arrays are Objects!`);

console.log(`I have ${friends.length} friends`);
console.log(`My best friend is ${friends[2]}`);
console.log(`My first three friends are ${friends.slice(0, 3)}`)
console.log('But here are all of my friends:');

for (let i = 0; i < friends.length; i += 1) {
  // friends[i] holds the "current" friend that we're looking at in this iteration of the loop
  console.log(friends[i]);
}
```

**Challenge: This function can verify whether or not a given array `arr` contains a given value `value`. For each numbered comment below, add a comment explaining the logic of the function!**

```js
const hasValue = (arr, value) => {
  for (let i = 0; i < arr.length; i++) { // 1.
    if (arr[i] === value) { // 2.   
      return true; // 3.
    }
  }
  return false; // 4.
}

const letters = ['a', 'b', 'c', 'd'];
console.log(hasValue(letters, 'c')); // Prints true
console.log(hasValue(letters, 'e')); // Prints false
```

**<details><summary>Check out this example</summary>**
```js
const hasValue = (arr, value) => {
  for (let i = 0; i < arr.length; i++) { // Loop through every index in the array
    if (arr[i] === value) { // Check each element of arr to see if it matches the value   
      return true; // If it does, we can immediately return true
    }
  }
  return false; // If we make it to the end of the loop, we must not have found it. Return false.
}

const letters = ['a', 'b', 'c', 'd'];
console.log(hasValue(letters, 'c')); // Prints true
console.log(hasValue(letters, 'e')); // Prints false
```
</details>

### Arrays Are Mutable


Strings have read-only methods like `toUpperCase()` and `slice()` that make a copy of the string but don't change the original string.

```js
const myName = 'ben';
myName.toUpperCase(); // doesn't mutate the string, it makes a copy
myName[0] = "J"; // doesn't even do anything
console.log(myName);  // still 'ben'
```

Unlike strings, arrays let you **mutate** them directly (change their contents without reassignment).

```js
const endLetters = ["x", "y", "z"];
endLetters[1] = "foo";
console.log(endLetters); // ["x", "foo", "z"]
```

We can even modify the `.length` of an Array to empty them:

```js
endLetters.length = 0; // endLetters now has no values
console.log(endLetters); // []
```

**<details><summary>Question: How are we allowed to modify the array if it is stored in a `const` variable?</summary>**
> `const` prevents us from reassigning the variable. Notice that we never reassign `endLetters`! The variable still references the same array, we're just changing the contents of the array. 
</details>

### Array Methods for Adding and/or Removing Values

Since arrays are mutable, they not only have read-only methods like `slice`, `indexOf`, and `includes`, they also have methods for adding to and removing from the array.

These methods let you add values to an array
* `arr.push(value)` — adds a given value to the end of an array, increasing the length by 1
* `arr.unshift(value)` — adds a given value to the front of an array, increasing the length by 1

These methods let you remove values from an array
* `arr.pop()` — removes a given value from the end of an array, reducing the length by 1
* `arr.shift()` — removes a given value from the front of an array, reducing the length by 1

Splice lets you add and remove values to and from an array all at once!
* `arr.splice(index, qtyToRemove, valuesToAdd)` — removes or replaces elements in an array and/or adds new elements to an array

```js
const letters = ['a', 'b', 'c', 'd', 'e'];

letters.push('f'); // adds 'f' to the end of letters
letters.unshift('z'); // adds 'z' to the beginning of letters
console.log(letters); // Prints ['z', 'a', 'b', 'c', 'd', 'e', 'f']

letters.pop(); // removes the last element ('f')
letters.shift(); // removes the first element ('z')
console.log(letters); // Prints ['a', 'b', 'c', 'd', 'e']

letters.splice(2, 0, 'Hi!'); // At index 2, removes no elements and inserts 'Hi!' 
console.log(letters); // Prints ['a', 'b', 'Hi!', 'c', 'd', 'e']

letters.splice(2, 1, 'Hey ;)'); // At index 2, replaces 1 element with 'Hey ;)'
console.log(letters); // Prints ['a', 'b', 'Hey ;)', 'c', 'd', 'e']

letters.splice(2, 2, 'Nope', 'Bye!'); // At index 2, replaces 2 elements with 'Nope' and 'Bye!'
console.log(letters); // Prints ['a', 'b', 'Nope', 'Bye!', 'd', 'e']
``` 

## Reference vs. Primitive Values

Arrays and Objects are considered **reference types**. Let's see why.

### How Reference Types (Arrays and Objects) are Stored in Memory

When a variable is created, a chunk of your computer's RAM is assigned to hold some data. Primitive values are small enough to be stored in memory. Arrays and objects (a.k.a "reference types") however can grow to be any size and therefore cannot be contained within a single **memory address**. 

So, instead, they are stored in an area called the **heap** and a **reference to their heap address** is stored in the variable instead.

{% embed url="https: //docs.google.com/presentation/d/13d9TXTogmt4hV9DLuHzARExbcu3A7Nr9dnY69Zz5fKw/embed?start=false&loop=false&delayms=3000" %}

As a result, we can mutate the contents of an array without reassigning the variable because the variable doesn't hold the array, it holds a reference to the array!

### Pass by Reference
when you copy an array or object from one variable to another, or you pass an array or object into a function, the reference is being passed, not the array or object itself.

```js
const emptyTheArray = (arr) => {
  // 3. Upon receiving the reference, this function modifies array
  arr.length = 0;
}

// 1. The array below is created in the heap and the "reference" to its location is stored in letters
const letters = ['a', 'b', 'c'];

// 2. When we invoke emptyTheArray, we "pass the reference" to the function
emptyTheArray(letters);

// 4. The array referenced by letters has been modified by the function!
console.log(letters); // Prints []
```

Because variables store immutable raw data values (not references), it is said that we "pass by value" when we pass strings, numbers, or booleans into a function. There is no way to mutate the incoming value in a way that would affect the variable in the outer scope.

```js
// 3. When this function is invoked, the variables oldName and newName are created locally
const changeName = (oldName, newName) => {
  // 4. I can reassign oldName, but this isn't doing anything to the myName variable whose value was passed in
  oldName = newName;
}

// 1. I declare the variable myName to store the immutable string 'ben'`
const myName = 'ben';

// 2. I pass the value itself to changeName
changeName(myName, 'fred');

// 5. There is nothing that changeName can do to mutate the value held by myName aside from reassigning the myName variable itself
console.log(myName); // Prints 'ben'
```

### Impure and Pure Functions

Functions are considered **impure functions** if they:

1. Produce different outputs when given the same inputs
2. Produce side effects (like mutating incoming values)

The functions below are impure functions:

```js

// This function can't return the same output each time it is invoked.
const rollDie = () => {
  return Math.ceil(Math.random() * 6);
}
console.log(rollDie()); // ???
console.log(rollDie()); // ???


let count = 0;
// This function has the side effect of modifying the count variable which is outside of its scope
const incrementBy = (x) => {
  count += x;
  return count;
};
console.log(incrementBy(2)); // 2
console.log(incrementBy(2)); // 4
console.log(incrementBy(2)); // 6
console.log(count); // 6


// This function mutates the incoming arr value
const emptyTheArray = (arr) => {
  arr.length = 0;
}

const letters = ['a', 'b', 'c'];
emptyTheArray(letters):

console.log(letters); // Prints []
```

### Making Copies of Arrays to Make Pure Functions with the Spread Syntax

Functions that accept Arrays and modify them are impure. To make a function that modifies an Array pure, we need to make a copy of it first. The most effective way to do this is using the spread syntax: `[...arr]`

```js
const extend = (arr, value) => {
  const newArr = [...arr, value];
  return newArr;
}

const letters = ['a', 'b', 'c'];
const moreLetters = extend(letters, 'd');

console.log(letters); // Prints ['a', 'b', 'c']
console.log(moreLetters); // Prints ['a', 'b', 'c', 'd'];
```

## Advanced Array Syntax

### 2D Arrays

An Array can contain other Arrays! When the inner arrays contain values, we call this a "2-Dimensional (2D)" Array or a **"matrix"**.

The matrix below has 2 columns and 3 rows:

```js
const coordinates = [
  [30, 90],
  [40, 74],
  [34, 118],
];

const newOrleans = coordinates[0];
const newOrleansLat = coordinates[0][0];
const newOrleanLong = coordinates[0][1];

console.log(newOrleans);
console.log(newOrleansLat);
console.log(newOrleanLong);
```

When accessing a 2D array, the first index references the "row" and the second index references the "column"

### Destructuring Assignment & Rest Operator

The **destructuring assignment** syntax is a JavaScript expression that makes it possible to "unpack" values from arrays (or properties from objects) into distinct variables.

The syntax uses `[]` on the left side of an assignment operator `=` to define which values to unpack from the Array on the right side (the "source variable");

```js
const coordinates = [
  [30, 90],
  [40, 74],
  [34, 118],
  [42, 88],
  [39, 77]
];

// Unpack the first three arrays from coordinates (we are choosing to ignore the rest)
const [newOrleans, newYork, losAngeles] = coordinates;
console.log(newYork);

// Unpack the two values from the newYork array.
const [newYorkLat, newYorkLong] = newYork;

console.log(newYorkLat);
console.log(newYorkLong);
```

In the example above, we only unpacked the first 3 values of `coordinates`. 

If we want to, we can use the "rest" operator (`...rest`) to store the remaining unpacked values in a variable:

```js
const [newOrleans, newYork, ...lesserCities] = coordinates;

console.log(lesserCities);
/* 
[ 
  [34, 118],
  [42, 88],
  [39, 77]
];
*/
```