# 2. Closures and Data Hiding

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-1-encapsulation-factories-closure)!
{% endhint %}

**Table of Contents:**

- [Key Concepts](#key-concepts)
- [Intro: Closures](#intro-closures)
  - [Why are Closures Important? A Closure Counter](#why-are-closures-important-a-closure-counter)
- [Data Hiding Prevents Invalid Data](#data-hiding-prevents-invalid-data)
  - [Factory Functions and Closures Let Us Hide Data](#factory-functions-and-closures-let-us-hide-data)
  - [Always Return Copies of Pass-By-Reference Values](#always-return-copies-of-pass-by-reference-values)
  - [Every Instance Creates a New Closure](#every-instance-creates-a-new-closure)
- [Quiz!](#quiz)
- [Challenge](#challenge)

## Key Concepts

* **Data hiding** is the act of limiting access to an interface's data in order to prevent users of the interface from introducing invalid data that would break the program's logic (e.g. adding a `null` value to a list of strings). Data hiding is a key outcome of effective encapsulation.
* A **closure** is created when an "inner function" references a variable declared in the surround scope. It can be used to hide data in the factory function pattern.

## Intro: Closures

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

### Why are Closures Important? A Closure Counter

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

// myCounter() can still reference the count variable
myCounter(); // prints 1
myCounter(); // prints 2
myCounter(); // prints 3

console.log(count); // ReferenceError: count is not defined
```

Here's how this works:
* The outer function `makeCounter` declares a variable `count` that is referenced by the inner function `counter`. 
* Due to the closure, the `myCounter` function can still reference `count`, even after the outer function finishes executing.
* Aside from the `myCounter` function, we have no way to directly reference the `count` variable. 

Another cool thing about closures is that each time the outer function is invoked, a new `count` variable and a new inner function are created, each with their own closure:

```js
const myCounter = makeCounter();
const yourCounter = makeCounter();

myCounter(); // 1
myCounter(); // 2
yourCounter(); // 1
yourCounter(); // 2
```

Let's see how we can leverage this in object-oriented programming to support **encapsulation**.

## Data Hiding Prevents Invalid Data

Remember, the goal of any programming paradigm is to write code that will behave *consistently* and *predictably*.

Consider the `friendsManager` example again. Notice that we've added a guard clause to ensure that only strings are added as friends. We want to ensure that we don't introduce any invalid data into our list of friends.

```js
const createFriendsManager = (username) => {
  return {
    username,
    friends: [],
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') { // new guard clause
        console.log('new friends must be strings');
        return;
      }
      this.friends.push(newFriend);
    },
    printFriends() {
      this.friends.forEach((friend) => {
        console.log(`${this.username} is friends with ${friend}`);
      });
    },
    removeLast() {
      this.friends.pop()
    }
  }
}

const bensFriends = createFriendsManager('ben');

bensFriends.addFriend('daniel'); // this gets added
bensFriends.addFriend(true);     // this does not

// What about this is NOT consistent or predictable?
bensFriends.friends.push('emmanuel');
bensFriends.friends.push(42);

bensFriends.printFriends(); // What is printed here?
```

<details>

<summary><strong>Q: What about this example is NOT consistent or predictable? What is printed by the last statement?</strong></summary>

You can modify the `friendsManager.friends` array either through the `addFriend()` method or by directly mutating the `friends` array. When modifying the array directly, there are no safeguards.

* The `friends` array can be directly modified from the outside, which leads to uncontrolled additions like `friendsManager.friends.push(42)`. This results in an array that might have invalid data types, such as numbers and booleans mixed with strings, violating the intended structure of the friends list.
* This unpredictability leads to bugs and confusion, as the integrity of the data inside `friendsManager` cannot be guaranteed.

The final statement shows that `'emmanuel'` and `42` have been added to the list.

</details>

Despite our attempts to prevent invalid data, the `friends` array is directly accessible through the `friendsManager.friends` property. As a result, we can't guarantee that our rules for adding new friends are enforced.

### Factory Functions and Closures Let Us Hide Data

**Data hiding** is the act of limiting access to an interface's data in order to prevent users of the interface from introducing invalid data that would break the program's logic (e.g. adding a `null` value to a list of strings). Data hiding is a key outcome of effective encapsulation.

In this case, we want to hide the `friends` array so that it can't be directly accessed.

In JavaScript, we can modify our factory function to use closure to do this:

```js
const makeFriendsManager = (username) => {
  // This variable is in the "outer" function's scope. 
  const friends = [];

  const friendsManager = {
    username, // we can leave username as a "public" part of the object if we don't care about how it is used or reassigned.
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        console.log('new friends must be strings');
        return;
      }
      // use closure to reference the friends variable
      friends.push(newFriend); 
    },
    printFriends() {
      // use closure to reference the friends variable
      friends.forEach((friend) => { 
        console.log(`${this.username} is friends with ${friend}`);
      });
    },
    // add a getter method to get a copy of the friends array
  }
  return friendsManager;
}

const bensFriends = makeFriendsManager();
bensFriends.addFriend('zo')
bensFriends.addFriend('motun')
bensFriends.printFriends() // ['zo', 'motun']

console.log(bensFriends.friends) // undefined
console.log(friends); // reference error!
```

Here's how this works:
* We declare the `friends` array as its own variable, outside of `friendsManager`. It can no longer be accessed directly via the object.
* The methods `addFriend` and `printFriends` reference the `friends` array in the outer scope, forming a closure.
* Invoking `makeFriendsManager()` returns the `bensFriends` object whose methods `addFriend()` and `printFriends()` maintain their reference to the `friends` variable through the closure. 
* The internal `friends` array is now inaccessible except through the interface `addFriend()` and `printFriends()`.
* The validation guard clause in `addFriend()` now prevents invalid data to enter the system.

Because `addFriend` and `printFriends` were declared within the same scope as `friends`, they can continue using their reference to `friends` even after leaving that scope.

### Always Return Copies of Pass-By-Reference Values

Suppose we wanted to provide a way to access the list of `friends`, we would add a "getter" method like `getFriends`:

```js
const makeFriendsManager = (username) => {
  const friends = [];

  const friendsManager = {
    username,
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        console.log('new friends must be strings');
        return;
      }
      friends.push(newFriend);
    },
    printFriends() {
      friends.forEach((friend) => {
        console.log(`${this.username} is friends with ${friend}`);
      });
    },
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

Remember, each time we invoke this function, we will create a new `friends` array and a new object with methods that reference that specific **instance** of the friends array.

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
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        console.log('new friends must be strings');
        return;
      }
      friends.push(newFriend);
    },
    printFriends() {
      friends.forEach((friend) => {
        console.log(`I am friends with ${friend}`);
      });
    },
    getFriends() {
      return [...friends]; 
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
