# 1. Intro to OOP, Encapsulation, Factory Functions, and Closure

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-0-0-encapsulation-factories-closure)!
{% endhint %}

**Table of Contents:**

- [Closures](#closures)
  - [A Closure Counter](#a-closure-counter)
- [Encapsulation and `this`](#encapsulation-and-this)
  - [Hiding Data From Direct Access](#hiding-data-from-direct-access)
  - [Factory Functions and Closures Let Us Hide Data](#factory-functions-and-closures-let-us-hide-data)
  - [Always Return Copies of Pass-By-Reference Values](#always-return-copies-of-pass-by-reference-values)
  - [Every Instance Creates a New Closure](#every-instance-creates-a-new-closure)
- [Quiz!](#quiz)
- [Challenge](#challenge)
- [Summary](#summary)

## Closures

Consider this function `addSuffixToEachWord` below. It takes in an array of words and returns a new array with a given `suffix` added to the end of each word.

```js
const addSuffixToEachWord = (words, suffix) => {
  return words.map((word) => word + suffix);
}

const words = ['lime', 'lemon', 'gator'];
const drinks = addSuffixToEachWord(words, 'ade');

console.log(drinks); // ['limeade', 'lemonade', 'gatorade'];
```

Without you knowing it, this example creates something called a **closure**

**Closures are created when an "inner function" maintains references to variables in its surrounding scope (an "outer function").**

**<details><summary>Q: In this example, what is the "outer" function and what is the "inner" function? Which variable from the outer function is referenced by the inner function?</summary>**

The outer function is `addSuffixToEachWord` and the inner function is the anonymous callback function `(word) => word + suffix`.

The inner function references the parameter `suffix` from the outer function's scope.

</details>

### A Closure Counter

The applications of closures are really interesting. Consider this classic example:

```js
// The "outer function" declares the count variable in its scope
const makeCounter = () => {
  let count = 0;

  // the "inner function" references the count variable in the surrounding scope
  const counter = () => {
    count++;
    console.log(count);
  }

  return counter;
}

const myCounter = makeCounter();
myCounter(); // prints 1
myCounter(); // prints 2
myCounter(); // prints 3

console.log(count); // ReferenceError: count is not defined
```

In this example, the outer function `makeCounter` declares a variable `count` that is referenced by the inner function `counter`. Once the `makeCounter` function finishes executing, we have no way to directly reference the `count` variable. However, due to the closure, the `myCounter` function can still reference it and can increment and print it!

One cool thing about closure is that each time the outer function is invoked, a new `count` variable and a new inner function are created, each with their own instances of closure:

```js
const myCounter = makeCounter();
const yourCounter = makeCounter();

myCounter(); // 1
myCounter(); // 2
yourCounter(); // 1
yourCounter(); // 2
```

Let's see how we can leverage this in object-oriented programming. 

## Encapsulation and `this`

Let's imagine we want to create a program that manages a list of friends.

In functional programming, might write something like this:

```js
const addFriendToList = (friends, newFriend) => {
  const clone = [...friends]
  clone.push(newFriend);
  return clone;
};

const printFriends = (friends) => {
  friends.forEach((friend) => {
    console.log(`I am friends with ${friend}`)
  });
};

const friends = ['ada', 'bart'];

const newFriends = addFriendToList(friends, 'caleb');
printFriends(newFriends);
```

Note how the functions are pure: they do not reference global variables and aim to avoid modifying variables passed into them.

In object-oriented programming, we have a different goal entirely: encapsulation. 

**Encapsulation is achieved by grouping data and the functions that operate on that data into one object**, like the `friendsManager` object below. In this example, the `friendsManager` object contains both the list of `friends` as well as the actions that you can perform with that list: `addFriend` and `printFriend`.

```js
// Object Oriented Programming encapsulates data with functionality
const friendsManager = {
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
    this.friends.push(newFriend);
  },
  printFriends() {
    this.friends.forEach((friend) => {
      console.log(`I am friends with ${friend}`);
    });
  }
}

friendsManager.addFriend('caleb');
friendsManager.addFriend('deema');
friendsManager.printFriends();
```

Object-oriented programming and encapsulation gives us a new, incredibly powerful, and often misunderstood tool: the `this` keyword.

**`this` refers to the object that is executing the current function**. The `this` keyword allows the methods `addFriend` and `printFriends` to avoid needing to have the `friends` array explicitly passed into them — they just refer to `this.friends`!

{% hint style="info" %}
Check out this video to learn more about the [this Keyword](https://www.youtube.com/watch?v=gvicrj31JOM\&ab_channel=ProgrammingwithMosh)!
{% endhint %}

<summary><strong>Q: How would you add a <code>friendsManager.removeLast()</code> method that removes the last friend that was added?</strong></summary>

```js
const friendsManager = {
  friends: [],
  // a "method" is a function stored inside of an object
  addFriend(newFriend) {
    this.friends.push(newFriend);
    // `this` refers to the "owner" of the method. 
  },
  printFriends() {
    console.log(this.friends)
  },
  removeLast() {
    this.friends.pop()
  }
}
```

</details>

### Hiding Data From Direct Access

Another core principle of encapsulation is **restricting access to and object's data.**

Consider the `friendsManager` example again. Notice that we've added a guard clause to ensure that only strings are added as friends.

```js
const friendsManager = {
  friends: [],
  addFriend(newFriend) {
    if (typeof newFriend !== 'string') {
      throw Error('new friends must be strings');
    };
    this.friends.push(newFriend);
  },
  printFriends() {
    console.log(this.friends)
  }
}

friendsManager.addFriend('daniel'); // this gets added
friendsManager.addFriend(true);     // this does not

// What about this is NOT consistent or predictable?
friendsManager.friends.push('emmaneul');
friendsManager.friends.push(42);
```

<details>

<summary><strong>Q: What about the last two lines in the example are NOT consistent or predictable?</strong></summary>

You can modify the `friendsManager.friends` array either through the `addFriend()` method or by directly mutating the `friends` array. When modifying the array directly, there are no safeguards.

* The `friends` array can be directly modified from the outside, which leads to uncontrolled additions like `friendsManager.friends.push(42)`. This results in an array that might have invalid data types, such as numbers and booleans mixed with strings, violating the intended structure of the friends list.
* This unpredictability leads to bugs and confusion, as the integrity of the data inside `friendsManager` cannot be guaranteed. How to Make the Code More Consistent Using Closures:

</details>

If we are able to ensure `friendsManager.friends` is only interacted with through the methods `addFriend` and `printFriends`, we can guarantee that our rules for adding new friends are enforced.

### Factory Functions and Closures Let Us Hide Data

We can leverage closures to control access to the `friends` array by doing the following:

* We declare a "factory function" called `makeFriendsManager`. This is our "outer" function.
* Inside, we declare the `friendsManager` object and return it.
* We declare the `friends` array as its own variable, outside of `friendsManager`. It can no longer be accessed directly via the object but can be referenced by the methods `addFriend` and `printFriends`

```js
// First, we make a function that returns our friendsManager. 
// This is an "outer" function
const makeFriendsManager = () => {
  // This variable is in the "outer" function's scope. 
  // friends is referenced by addFriend and printFriends but is not in the friendsManager itself.
  const friends = [];

  const friendsManager = {
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        throw Error('new friends must be strings');
      };
      friends.push(newFriend);
    },
    printFriends() {
      console.log(friends);
    }
  }
  return friendsManager;
}

const bensFriendsManager = makeFriendsManager();
bensFriendsManager.addFriend('zo')
bensFriendsManager.addFriend('motun')
bensFriendsManager.printFriends() // ['zo', 'motun']

console.log(bensFriendsManager.friends) // undefined
console.log(friends); // reference error!
```

The full effect of the closure is seen when we invoke `makeFriendsManager`. After we return from `makeFriendsManager`, we can't reference `friends` anymore. However, the methods `addFriend` and `printFriends` keep their references to `friends`, even after they've been returned.

Because `addFriend` and `printFriends` were declared within the same scope as `friends`, they can continue using their reference to `friends` even after leaving that scope.

### Always Return Copies of Pass-By-Reference Values

Suppose we wanted to provide a way to access the list of `friends`, we would add a "getter" method like `getFriends`:

```js
const makeFriendsManager = () => {
  const friends = [];

  const friendsManager = {
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        throw Error('new friends must be strings');
      };
      friends.push(newFriend);
    },
    printFriends() {
      console.log(friends);
    }
    getFriends() {
      // return a copy of the array, not the array itself
      return [...friends]; 
    },
  }
  return friendsManager;
}

const myFriendsManager = makeFriendsManager();
myFriendsManager.addFriend('reuben')
myFriendsManager.addFriend('maya')
myFriendsManager.addFriend('carmen')

const allFriends = myFriendsManager.getFriends()

// A copy is returned. We can modify the copy all we want...
allFriends[0] = 'ben';
console.log(allFriends); // ['ben', 'maya', 'carmen']

// ... but the original is not disturbed. It remains the "source of truth"
console.log(myFriendsManager.getFriends()); // ['reuben', 'maya', 'carmen']
```

To ensure `friends` remains hidden, the `getFriends` method returns a copy of the `friends` array to avoid sharing the array reference.

Whenever you are dealing with a pass-by-reference data type (arrays and objects), always make sure to return a copy of it!

### Every Instance Creates a New Closure

The cool thing about closures is that each time we invoke this function, we will create a new `friends` array and a new object with methods that reference that specific **instance** of the friends array.

```js
const bensFriendsManager = makeFriendsManager();
bensFriendsManager.addFriend('zo')
bensFriendsManager.addFriend('motun')

const gonzalosFriendsManager = makeFriendsManager();
gonzalosFriendsManager.addFriend('carmen');

console.log(bensFriendsManager.getFriends()) // ['zo', 'motun']
console.log(gonzalosFriendsManager.getFriends()) // ['carmen']
```

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
      if (typeof newFriend !== 'string') {
        throw Error('new friends must be strings');
      };
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
      if (typeof newFriend !== 'string') {
        throw Error('new friends must be strings');
      };
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
