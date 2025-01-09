# Debugging & Problem Solving

**Table of Contents:**
- [PEDAC Problem Solving](#pedac-problem-solving)
  - [Example PEDAC](#example-pedac)
- [Tips for Making Your Own PEDAC](#tips-for-making-your-own-pedac)
  - [P — State the Problem](#p--state-the-problem)
  - [E — Examples / Test Cases](#e--examples--test-cases)
  - [D — Data Structures and Tools](#d--data-structures-and-tools)
  - [A — Algorithm](#a--algorithm)
  - [C — Code!](#c--code)

## PEDAC Problem Solving

Imagine that you had the following problem to solve:

> Consider this array.
> ```js
> const users = [
>   { id: 1, name: 'ben', isAdmin: false},
>   { id: 2, name: 'maya', isAdmin: true},
>   { id: 3, name: 'reuben', isAdmin: true},
>   { id: 4, name: 'gonzalo', isAdmin: false},
>   { id: 5, name: 'ana', isAdmin: true},
> ]
> ```
> Write a function `getAdminIds` that has one parameter, an array `users` full of objects that have an `id`, `name`, and `isAdmin` properties. The function should return an array containing the `id` numbers of the users where the `isAdmin` property is `true`. If none are found, return an empty array. If the `users` array is not an array, return `null`. Do not modify the original array.

You might have an idea of how to solve the problem, but before you do, try planning out your approach first! Doing so has a number of benefits:
* You can work on the skill of problem solving separately from the skill of coding!
* You will have a roadmap for writing a solution that you can return to if you get lost while coding.
* If you are having trouble implementing your solution, you can bring your planning to an instructor, mentor, or peer to quickly get them up to speed on the problem.

At Marcy, we use **PEDAC** which is an acronym that stands for the following problem solving strategy:
* State the **P**roblem
* Think of **E**xamples and **e**dge cases
* List the **D**ata Structures and tools you can use
* Plan out the **A**lgorithm
* **C**ode the solution!

### Example PEDAC

Here is an example of our PEDAC process:

```js
/* 
PEDAC

State the Problem
- inputs: 
  - users: an array of objects, each with the properties id, name, and isAdmin

- output:
  - an array of numbers representing the id numbers of the admins in users

Examples / Test Cases:

const users = [
  { id: 1, name: 'ben', isAdmin: false},
  { id: 2, name: 'maya', isAdmin: true},
  { id: 3, name: 'reuben', isAdmin: true},
  { id: 4, name: 'gonzalo', isAdmin: false},
  { id: 5, name: 'ana', isAdmin: true},
]

getAdminIds(users) -> [2, 3, 5]
getAdminIds([{ id: 1, name: 'ben', isAdmin: false}]) -> []
getAdminIds([{ id: 1, name: 'ben', isAdmin: true}]) -> [1]
getAdminIds([]) -> []
getUserById() -> null
getUserById(5) -> null

Data Structures & Tools
- an array to hold the ids of the admins (this is the return value)
- for loop to iterate through the array
- bracket notation to access values in the array
- dot notation to access object properties
- if statements to check if an object is an admin

Algorithm
- Set `ids` to be a new array to store the ids of admins
- Iterate through the input `users` array. For each object...
  - If the current object is an admin...
  - Add that object's id to the `ids` array
- Return the `ids` array after the iteration is complete.
*/

const getAdminIds = (users) => {
  const ids = [];
  for (let i = 0; i < users.length; i++) {
    const currentUser = users[i];
    if (currentUser.isAdmin) {
      ids.push(currentUser.id)
    }
  }
  return ids;
}

// Test Code

const users = [
  { id: 1, name: 'ben', isAdmin: false},
  { id: 2, name: 'maya', isAdmin: true},
  { id: 3, name: 'reuben', isAdmin: true},
  { id: 4, name: 'gonzalo', isAdmin: false},
  { id: 5, name: 'ana', isAdmin: true},
]

console.log(getAdminIds(users)); // -> [2, 3, 5] because three admins with these ids were found
console.log(getAdminIds([{ id: 1, name: 'ben', isAdmin: false}])); // -> [] because no admins were found
console.log(getAdminIds([{ id: 1, name: 'ben', isAdmin: true}])); // -> [1] because only 1 admin was found
console.log(getAdminIds([])); // -> [] because no admins were found
console.log(getUserById()); // -> null because the input was undefined
console.log(getUserById(5)); // -> null because an array was not provided
```

## Tips for Making Your Own PEDAC

Let's look at how we arrived at the PEDAC above.

### P — State the Problem

Before you can solve a problem, you must fully understand the problem. When you have a problem in front of you, start by listing **inputs** and **outputs**

{% hint style="info" %}
Tip: Write this planning in a comment directly in your code!
{% endhint %}

When listing inputs and outputs, you want to be as descriptive as possible. 
* Rather than _"an array"_ say _"an array of numbers"_
* Rather than _"an object"_ say _"an object with the properties x, y, and z"_
* Rather than _"an array of objects" say "an array of objects, each with the properties x, y, and z"_

```js
/* 
PEDAC

State the Problem
- inputs: 
  - users: an array of objects, each with the properties id, name, and isAdmin

- output:
  - an array of numbers representing the id numbers of the admins in users
```

### E — Examples / Test Cases

When listing examples, imagine you are invoking your own function. Come up with a few different examples with "good" inputs, a few with "bad" inputs, and a few with `undefined` inputs. For each example, include the expected result of calling the function.

```js
/*
Examples / Test Cases:

getAdminIds(users) -> [2, 3, 5]
getAdminIds([{ id: 1, name: 'ben', isAdmin: false}]) -> []
getAdminIds([{ id: 1, name: 'ben', isAdmin: true}]) -> [1]
getAdminIds([]) -> []
getUserById() -> null
getUserById(5) -> null
```

### D — Data Structures and Tools

List all of the relevant tools that you might use to solve the problem, including:
* Data you need to store in variables
  * Primitive data values: strings, numbers, booleans to represent x, y or z
  * Reference data values: arrays or objects to hold x, y, and z
* Control Flow Constructs
  * `for` or `while` loops to repeat x or to iterate through y
  * `if`/`else if`/`else` statements to determine whether to x, y, or z
* Global Functions / Methods
  * Array methods
  * String methods
  * Math methods
  * etc...

```js
/* 
Data Structures & Tools
- an array to hold the ids of the admins (this is the return value)
- for loop to iterate through the array
- bracket notation to access values in the array
- dot notation to access object properties
- if statements to check if an object is an admin
```

### A — Algorithm

Plan out the order of operations to process the inputs and return the desired output. During this stage, you should be writing "pseudocode". Pseudocode should be written such a way that anyone with basic programming knowledge could implement your algorithm in any language.

```js
/* 
Algorithm
- Set `ids` to be a new array to store the ids of admins
- Iterate through the input `users` array. For each object...
  - If the current object is an admin...
  - Add that object's id to the `ids` array
- Return the `ids` array after the iteration is complete.
*/
```

### C — Code!

Once you've completed the first four steps, go ahead and build your solution! Use your examples to test your solution.

```js
const getAdminIds = (users) => {
  const ids = [];
  for (let i = 0; i < users.length; i++) {
    const currentUser = users[i];
    if (currentUser.isAdmin) {
      ids.push(currentUser.id)
    }
  }
  return ids;
}

console.log(getAdminIds(users)); // -> [2, 3, 5]
console.log(getAdminIds([{ id: 1, name: 'ben', isAdmin: false}])); // -> []
console.log(getAdminIds([{ id: 1, name: 'ben', isAdmin: true}])); // -> [1]
console.log(getAdminIds([])); // -> []
console.log(getUserById()); // -> null
console.log(getUserById(5)); // -> null
```
