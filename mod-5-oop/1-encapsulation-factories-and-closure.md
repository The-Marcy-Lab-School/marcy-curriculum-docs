# Intro to OOP, Encapsulation, Factory Functions, and Closure

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-0-0-encapsulation-factories-closure)!
{% endhint %}

**Table of Contents:**
- [Intro to Mod 5: Object-Oriented Programming (OOP)](#intro-to-mod-5-object-oriented-programming-oop)
- [Encapsulation](#encapsulation)
- [Designing A Consistent and Predictable Interface](#designing-a-consistent-and-predictable-interface)
- [Factory Functions, Privacy, and Closures](#factory-functions-privacy-and-closures)
- [Quiz!](#quiz)
- [Challenge](#challenge)
- [Summary](#summary)

## Intro to Mod 5: Object-Oriented Programming (OOP)

Object-Oriented Programming is a style of programming (a "paradigm") that uses **objects to manage state (data) and behavior** in an application. While OOP does let us do some new things, more than anything, it helps us write better, more organized code.

It can be defined by its 4 pillars:

* **Encapsulation** - bundling data and methods into a single unit while protecting the data
* **Abstraction** - hiding complexity through functions and prototypes
* **Inheritance** - sharing behavior between objects
* **Polymorphism** - similar objects can be used interchangeably

![The four pillars of object oriented programming are abstraction, inheritance, polymorphism, and encapsulation.](img/oop-pillars.png)

Throughout this module, we will be learning about these four pillars and how we implement them in JavaScript using the `class` syntax.

## Encapsulation

In functional programming, we separate data from the functions that act on them. We achieve **consistency** & **predictability** through pure functions.

```js
// Functional Programming separates data from functionality
const friends = ['ahmad', 'brandon', 'carmen'];

const addFriend = (friends, newFriend) => {
  if (typeof newFriend !== 'string') return friends;
  return [...friends, newFriend]; // keep it pure, make a new list
}

const newFriends = addFriend(friends, 'daniel');
```

In OOP, we store data in objects and give those objects methods to manipulate their own data. This is called **encapsulation**.

```js
// Object Oriented Programming encapsulates data with functionality
const friendsManager = {
  friends: [],
  addFriend(newFriend) {
    this.friends.push(newFriend);
    // `this` refers to the "owner" of the method. 
  }
}

friendsManager.addFriend('ahmad');
friendsManager.addFriend('brandon');
friendsManager.addFriend('carmen');
// Here, friendsManager invokes addFriend so this === friendsManager

console.log(friendsManager.friends)
```

**Side Note on `this`**:

* When used as method of an object, **`this` refers to the object that invokes the method.**
* `this` is one of the most complicated topics in JavaScript. Check out this video to learn more: [JavaScript this Keyword](https://www.youtube.com/watch?v=gvicrj31JOM\&ab\_channel=ProgrammingwithMosh).

<details>

<summary><strong>Q: How does the <code>friendsManager</code> object demonstrate encapsulation compared to the first example? How does <code>this</code> enable encapsulation?</strong></summary>

The `friendsManager` object stores the `friends` array inside alongside the `addFriend` method.

When `friendsManager.addFriend()` is invoked, the `addFriend` method uses `this` to manipulate the `friendsManager`'s own `friends` array.

</details>

## Designing A Consistent and Predictable Interface

**Consistency** and **predictability** are major goals in software engineering. This is what motivates us to write pure functions in functional programming. It is just as important in OOP.

Consider the `friendsManager` example again. Notice that we've added a guard clause to ensure that only strings are added as friends.

```js
const friendsManager = {
  friends: [],
  addFriend(newFriend) {
    if (typeof newFriend !== 'string') return;
    this.friends.push(newFriend);
  }
}

// What about this is NOT consistent or predictable?
friendsManager.addFriend('daniel');
friendsManager.addFriend(true);
friendsManager.friends.push('emmaneul');
friendsManager.friends.push(42);
```

<details>

<summary><strong>Q: What about the last two lines in the example are NOT consistent or predictable?</strong></summary>

You can modify the `friendsManager.friends` array either through the `addFriend()` method or by directly mutating the `friends` array. When modifying the array directly, there are no safeguards.

</details>

## Factory Functions, Privacy, and Closures

A core tenet of **encapsulation** in OOP is to hide values like `friends` from being directly accessed.

Let's see how we can do this using **closure**. A **closure** is when an "inner function" maintains references to variables in its surrounding scope (an "outer function").

Here's how we use **closure** to protect the `friends` array:

```js
const makeFriendsManager = () => {
  // this variable is in the "outer" function
  // and referenced in addFriend and getFriends
  const friends = [];

  const friendsManager = {
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') return;
      friends.push(newFriend);
    }
    getFriends() {
      return [...friends]; 
    },
  }
  return friendsManager;
}

const bensFriendsManager = makeFriendsManager();
bensFriendsManager.addFriend('zo')
bensFriendsManager.addFriend('motun')
console.log(bensFriendsManager.friends) // undefined
console.log(bensFriendsManager.getFriends()) // ['zo', 'motun']
```

* We put the object in a function that returns the object.
* We move the `friends` array outside of the object so that the returned object doesn't have direct access to it.
* The returned object still has the `addFriend` method, but it can't reference `this.friends` anymore. Instead it references the `friends` variable from the surrounding scope. **this is closure**.
* The returned object has a new `getFriends` method that returns a copy of the `friends` array to avoid sharing the reference.
* The `friends` array is fully private but we can still interact with it using the object's **setter/getter methods**.

```js
const bensFriendsManager = makeFriendsManager();
bensFriendsManager.addFriend('zo')
bensFriendsManager.addFriend('motun')

const gonzalosFriendsManager = makeFriendsManager();
gonzalosFriendsManager.addFriend('carmen');

console.log(bensFriendsManager.getFriends()) // ['zo', 'motun']
console.log(gonzalosFriendsManager.getFriends()) // ['carmen']
```

The cool thing about closures is that each time we invoke this function, we will create a new `friends` array and a new object with methods that reference that specific **instance** of the friends array.

<details>

<summary><strong>Q: In the example above, identify the "outer" function and the inner function involved in creating a closure</strong></summary>

`makeFriendsManager` is the outer function and both `addFriend` and `getFriends` form a closure around the variable `friends`.

</details>

<details>

<summary><strong>Q: How can we modify the example above to be able to create a new friend manager with a starting set of <code>friends</code> as an argument?</strong></summary>

```js
const makeFriendsManager = (...initialFriends) => {
  const friends = [...initialFriends];

  const friendsManager = {
    getFriends() {
      return [...friends]; 
    },
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') return;
      friends.push(newFriend);
    }
  }
  return friendsManager;
}
const myFriendsManager = makeFriendsManager('ahmad', 'brandon', 'carmen');
```

</details>

## Quiz!

We've seen closures before in non-object-oriented contexts. Consider the functions below. Which of them creates a closure? How?

```js
const sayWordsLoudly = (words) => {
  words.forEach((word) => console.log(`${word}!!!`));
}

const addRandomToNumbers = (nums) => {
  const randomNum = Math.random();
  return nums.map((num) => num + randomNum);
}

const addAmountToNumbers = (nums, amount) => {
  return nums.map((num) => num + amount);
}

// This is an "IIFE" (Immediately Invoked Function Expression)
const getId = ((id = 1) => () => id++)();
```

<details>

<summary><strong>Answer</strong></summary>

* The first function DOES NOT create a closure. Even though there is an inner arrow function defined, that function doesn't reference variables in the scope outside of it
* The second function DOES create a closure because the inner arrow function passed to `nums.map` references the `randomNum` variable in the scope outside of it.
* The third function DOES create a closure for the same reason as the function above. Referencing the parameter `amount` is the same.
* The fourth function DOES create a closure because we have an outer arrow function returning an inner arrow function. The inner arrow function `() => id++` references the `id` parameter in the outer arrow function.

</details>

## Challenge

Below is a `counter` object. The problem is that the `counter.value` property is not private — it can be directly mutated. Your challenge is to create a function `makeCounter` that will protect the value of the counter while still allowing us to `increment()`, `decrement()`, and get the current value of the counter.

As a bonus, make the function accept an argument `startingValue` which sets the starting `value` of the counter. If no value is provided, start at `0`. Then make multiple counters, each starting at a different value.

```js
// challenge.js

const counter = {
  value: 0,
  increment() {
    this.value++;
  },
  decrement() {
    this.value--;
  }
}

console.log(counter.value); // 0
counter.increment();
counter.increment();
console.log(counter.value); // 2
counter.decrement();
console.log(counter.value); // 1
counter.value = 10; // BAD
```

<details>

<summary><strong>Solution</strong></summary>

```js
const makeCounter = (startingValue = 0) => {
  let value = startingValue;

  const counter = {  
    getValue() {
      return value;
    },
    increment() {
      value++;
    },
    decrement() {
      value--;
    }
  }
  return counter;
}

const counter = makeCounter();
console.log(counter.getValue()); // 0
counter.increment();
counter.increment();
console.log(counter.getValue()); // 2
counter.decrement();
console.log(counter.getValue()); // 1
console.log(counter.value); // undefined

const counterFrom5 = makeCounter(5);
console.log(counterFrom5.getValue()); // 5
```

</details>

## Summary

* **Object-Oriented Programming (OOP)**: A programming paradigm that uses objects to manage state (data) and behavior in an application.
* **Encapsulation**: Bundling data and methods into a single unit while protecting the data
* When used as method of an object, **`this` refers to the object that invokes the method.**
* A **closure** is created when an "inner function" references variables in its surrounding scope (an "outer function").
  * The inner function "remembers" the value of the variables in the surrounding scope, even after the outer function returns.
  * Each **instance** of the outer function creates a new closure.
* Benefits of Encapsulation:
  * We can create private variables
  * access to state is provided only through predictable **getter/setter** methods

```js
const makeFriendsManager = (...initialFriends) => {
  const friends = [...initialFriends];

  const friendsManager = {
    getFriends() {
      return [...friends]; 
    },
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') return;
      friends.push(newFriend);
    }
  }
  return friendsManager;
}

const bensFriendsManager = makeFriendsManager('zo', 'motun');
const gonzalosFriendsManager = makeFriendsManager();
gonzalosFriendsManager.addFriend('carmen');

// each instance will maintain its own list of friends
console.log(bensFriendsManager.getFriends()) // ['zo', 'motun']
console.log(gonzalosFriendsManager.getFriends()) // ['carmen']
```
