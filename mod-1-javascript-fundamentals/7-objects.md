# Objects

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-1-2-objects)!
{% endhint %}

**Table of Contents**:

- [Summary](#summary)
- [The basics](#the-basics)
  - [Accessing Objects with Dot Notation and Bracket Notation](#accessing-objects-with-dot-notation-and-bracket-notation)
  - [Dynamic Properties Challenge](#dynamic-properties-challenge)
- [Objects are Reference Types](#objects-are-reference-types)
- [Iterating Over Keys and Values of an Object](#iterating-over-keys-and-values-of-an-object)
- [Advanced Object Syntax](#advanced-object-syntax)
  - [Object Shorthand Using Variables](#object-shorthand-using-variables)
  - [Destructuring](#destructuring)

## Summary

* **Objects** are data structures that store multiple pieces of data as key-value pairs called properties, useful for representing real-world entities like users or products.
* Object properties can be accessed using **dot notation** (`object.property`) or **bracket notation** (`object["property"]`), with bracket notation required when using variables as keys.
* Objects are **mutable**, meaning you can add, modify, or delete properties after creation using assignment, the `delete` keyword, or direct property access.
* Objects can contain **nested data** including arrays and other objects, allowing for complex data structures.
* **Object shorthand** allows you to create objects more concisely when variable names match property names.
* **Destructuring** provides a convenient way to extract multiple properties from objects into separate variables.

## The basics

* Objects are a data type that can store multiple pieces of data as **key:value** pairs called **properties**
* Objects are also referred to as **dictionaries** and **hashmaps**:

```js
// Arrays store values in an order
const words = [
  "hello",
  "rainbow",
  "cat"
]

// Objects/Dictionaries store key:value pairs
const dictionary = {
  "hello": "a casual greeting",
  "rainbow": "a colorful arc of light",
  "cat": "the superior pet"
}
```

* Objects are useful for storing collections of data related to a single "thing", like data about a user.
* Objects can have arrays and other objects nested inside.

```js
const user = {
  "userId": 44292,
  "username": 'c0d3rkid',
  "password": 'jswiz1234',
  "friends": ['iLoveSoccer123', 'pythonNinja', 'messiGOAT'],
  "favoriteMeal": {
    "name": 'PB&J',
    "ingredients": ['peanut butter', 'jelly', 'bread']
  }
}
```

* When creating an object, the quotes around the key name are optional, except when the key includes spaces or begins with a number:

```js
const dictionary = {
  hello: "a casual greeting",
  rainbow: "a colorful arc of light",
  cat: "the superior pet",
  "see ya": "a casual way to say 'see you later'"
}
```

### Accessing Objects with Dot Notation and Bracket Notation

* Object values can be accessed and/or mutated using dot notation or bracket notation

```js
// Dot notation is the faster to type and easier to read than bracket notation
console.log(`Hi, my name is ${user.username}`);

// Bracket notation requires a string to be provided
console.log(`Hi, my name is ${user["username"]}`);

// Dot (and bracket) notation can be used to modify existing values, or create new ones!
user.password = 'try to break in now!';
user.isAdmin = false;

console.log(user);

// If a property holds an object or array, we can use dot/bracket notation on that value!
console.log(`My favorite meal is ${user.favoriteMeal.name}`);
console.log(`My best friend is ${user.friends[0]}`);

// Delete properties by using the `delete` keyword followed by dot/bracket notation
delete user.userId;
delete user["friends"];
```

### Dynamic Properties Challenge

When the key name of a property is stored in a variable, we _must_ use bracket notation (we can't use dot notation).

```js
const key = 'some key value';
myObj[key] = 'newValue';

// Using dot notation here with the variable won't work.
// JS will think that "key" is the name of the key
myObj.key = 'newValue';
```

Complete the program below so that it lets users add words to the dictionary!

```js
const prompt = require("prompt-sync")({ sigint: true });

const dictionary = {
  "hello": "a casual greeting",
  "rainbow": "a colorful arc of light",
  "cat": "the superior pet"
}

while (true) {
  console.log("Here are your words: ", dictionary);
  const newWord = prompt("Add a word to your dictionary, or press q to exit. ");

  if (newWord === 'q') { // a guard clause
    break;
  }

  const definition = prompt(`Okay, what is the definition of ${newWord}? `);

  // add the new word and its definition to the dictionary!
}
```

<details>

<summary><strong>Solution</strong></summary>

To complete this program, add the line `dictionary[newWord] = definition;` to the end

</details>

## Objects are Reference Types

We've already learned that arrays are reference types and so are objects. In fact, arrays are just a kind of object!

So, when we assign an object to a variable, we are storing a reference to the object's location in memory, not the values themselves. 

And when we assign a variable holding an object to another variable, each variable holds a reference to the same object

```js
const sheep = { name: 'benny', noise: 'baaaa' };
const clone = sheep;    // both variables reference the same object
clone.noise = 'BAAAAA'; // mutating the referenced object

console.log(sheep); // { name: 'benny', noise: 'BAAAAAA' }
console.log(clone); // { name: 'benny', noise: 'BAAAAAA' }
```

We can also use the spread syntax (`...`) to copy the values of one object into a new object. This is particularly useful when creating pure functions:

```js
const sheep = { name: 'benny', noise: 'baaaa' };

const makeLoudClone = (obj) => {
  const clone = { ...obj }; // copy the values of obj into a new object
  clone.noise = clone.noise.toUpperCase();
  return clone;
}

const clone = makeLoudClone(sheep);

console.log(sheep); // { name: 'benny', noise: 'baaaa' }
console.log(clone); // { name: 'benny', noise: 'BAAAA' }
```

## Iterating Over Keys and Values of an Object

One of the key benefits of an Array is that we can easily iterate through its values with a `for` loop. This is possible because we can use the variable `i` to step through the indexes of the array.

```js
const friends = ['bert', 'ernie', 'elmo'];
console.log("here are my friends:");

for (let i = 0; i < friends.length; i++) {
  console.log(friends[i])
}
```

We can't use a `for` loop with an object: it doesn't have countable indexes and their keys aren't in any particular order! 

But we CAN turn the object into an array and _then_ iterate through that array.

The `Object.keys(obj)` function puts all of the keys from the given object (`obj`) into a new array and returns it. We can then loop through those keys and use them to access their associated values:

```js
const dictionary = {
  "hello": "a casual greeting",
  "rainbow": "a colorful arc of light",
  "cat": "the superior pet"
}

// words is an array containing the keys of dictionary
const words = Object.keys(dictionary);

console.log(words); // ["hello", "rainbow", "cat"]

// We can iterate through words to access their definitions
for (let i = 0; i < words.length; i++) {
  const word = words[i];
  console.log(`The definition of ${word} is ${dictionary[word]}`);
}
```

`Object.values(obj)` does the same thing but instead puts the values of the given object (`obj`) into an array.

```js
const dictionary = {
  "hello": "a casual greeting",
  "rainbow": "a colorful arc of light",
  "cat": "the superior pet"
}

const definitions = Object.values(dictionary);

console.log("Can you tell what word each of these definitions are for?");
for (let i = 0; i < definitions.length; i++) {
  console.log(definitions[i]);
}
```

## Advanced Object Syntax

### Object Shorthand Using Variables

When constructing an object from variables, it can be quite repetitive if the key name matches the variable name.

```js
const makeUser = (name, age) => {
  const newUser = {
    "name": name,
    "age": age,
    "isAdmin": false,
    "friends": [],
  }
  return newUser;
}

const user1 = makeUser('ben', 30);
```

If we are storing the value held by a variable in a key with the same name as that variable, we can omit the `:` and just write the name of the variable:

```js
const makeUser = (name, age) => {
  const newUser = {
    name,
    age,
    isAdmin: false,
    friends: [],
  }
  return newUser;
}

const user1 = makeUser('ben', 30);
```

This example makes a user object with the provided properties as well as some default values (`isAdmin` is `false` and an empty `friends` list).

This is the same syntax used when exporting "named exports":

```js
const first = 'a';
const second = 'b';
const third = 'c';

module.exports = {
  first,
  second,
  third
}

/* 
This is shorthand for:

module.exports = {
  "first": first,
  "second": second,
  "third": third,
}
*/
```

### Destructuring

Destructuring is the process of creating variables from an existing array/object. When destructuring an object, the variable names _must_ match the property key names that you wish to extract. The order in which you list the property names doesn't matter.

```js
const dictionary = {
  "hello": "a casual greeting",
  "rainbow": "a colorful arc of light",
  "cat": "the superior pet"
}

// Destructuring creates a variable for the properties of an object.
// Here, we are choosing to ignore rainbow. 
const { hello, cat } = dictionary;
console.log(hello); // Prints "a casual greeting"
console.log(cat);   // Prints "the superior pet"
```

This is the exact same syntax used when importing a "named import".

```js
// importing the entire object
const dictionary = require('./0-basics_index');

// destructuring the imported object
const { hello, rainbow, cat } = require('./0-basics_index');
```

When an object is passed to a function, we will often destructure the object directly in the parameter list, letting us choose only the properties of the object that are relevant to the function:

```js
const userBen = {
  name: "Ben",
  age: 28,
  isAdmin: false
};

const introduceSelf = ({ name, age }) => {
  console.log(`Hello! My name is ${name} and I am ${age} years old.`);
};

introduceSelf(userBen);
```
