# 1. Encapsulation, Closures, and Factory Functions

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-1-encapsulation-factories-closure)!
{% endhint %}

**Table of Contents:**

- [Key Concepts](#key-concepts)
- [Functional Programming vs. Object-Oriented Programming](#functional-programming-vs-object-oriented-programming)
  - [OOP: Separation of Concerns through Encapsulation](#oop-separation-of-concerns-through-encapsulation)
- [What is `this`?](#what-is-this)
  - [Factory Functions and `this`](#factory-functions-and-this)
  - [A broader definition of `this`](#a-broader-definition-of-this)
    - [Global Scope](#global-scope)
    - [Arrow Functions](#arrow-functions)
    - [Function Declarations](#function-declarations)
- [Quick Aside: Closures](#quick-aside-closures)
  - [A Closure Counter](#a-closure-counter)
- [Restricting Access to Data Supports Encapsulation](#restricting-access-to-data-supports-encapsulation)
  - [Factory Functions and Closures Let Us Hide Data](#factory-functions-and-closures-let-us-hide-data)
  - [Always Return Copies of Pass-By-Reference Values](#always-return-copies-of-pass-by-reference-values)
  - [Every Instance Creates a New Closure](#every-instance-creates-a-new-closure)
- [Quiz!](#quiz)
- [Challenge](#challenge)
- [Summary](#summary)

## Key Concepts

* **Encapsulation** refers to the bundling of data with the methods that operate that data into a single object.
* An **interface** is a "shared boundary" where two or more components of a system can interact and exchange information.
* The **`this`** keyword in JavaScript refers to the current execution context. In methods, `this` refers to the object invoking the method.
* A **Factory** is a type of function that creates and returns objects with a defined set of properties and methods.
* An **instance** is a single object returned from a factory that maintains its own set of data that is distinct from other instances made by the factory.
* A **closure** is created when an "inner function" references a variable declared in the surround scope.
* **Data Hiding** is the act of restricting access to data in object such that they can only be accessed in a controlled manner through public methods.

## Functional Programming vs. Object-Oriented Programming

When designing software, we should seek to adhere to principles that will guide us towards creating **consistent** and **predictable** software. The two most common approaches (or "paradigms") are
1. Functional Programming
2. Object-Oriented Programming

Imagine we want to create a program that manages a list of friends. We want to be able to
1. add a friend to the list
2. print out all of the people we're friends with.

With a more **functional programming** approach, we might write pure functions like this:

```js
const addFriendToList = (friends, newFriend) => {
  const friendsCopy = [...friends]
  friendsCopy.push(newFriend);
  return friendsCopy;
};

const printFriends = (username, friends) => {
  friends.forEach((friend) => {
    console.log(`${username} is friends with ${friend}`)
  });
};

const friends = ['ada', 'bart'];
const newFriends = addFriendToList(friends, 'cleo');

printFriends('ben', friends);
// ben is friends with ada
// ben is friends with bart

printFriends('ben', newFriends);
// ben is friends with ada
// ben is friends with bart
// ben is friends with cleo
```

Note how the functions are pure: they do not reference global variables and aim to avoid modifying variables passed into them. As a result, the functions behave consistently and predictably.

In functional programming, separation of concerns is achieved by separating data from methods.

### OOP: Separation of Concerns through Encapsulation

In object-oriented programming, rather than separating data from functions, we take the opposite approach: **encapsulation**. 

**Encapsulation refers to the bundling of data and the methods that operate on that data into one object**, like the `friendsManager` object below.

```js
// Object Oriented Programming encapsulates data with functionality
const friendsManager = {
  username: 'ben',
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
    this.friends.push(newFriend);
  },
  printFriends() {
    this.friends.forEach((friend) => {
      console.log(`${this.username} is friends with ${friend}`);
    });
  }
}

friendsManager.printFriends();
// ben is friends with ada
// ben is friends with bart

friendsManager.addFriend('cleo');
friendsManager.printFriends();
// ben is friends with ada
// ben is friends with bart
// ben is friends with cleo
```

In this example, the `friendsManager` object contains both the list of `friends` as well as the actions that you can perform with that list: `addFriend` and `printFriend`. We can call this object the **"interface"** for this feature of managing friends.

{% hint style="info" %}
An **interface** is a "shared boundary" where two or more components of a system can interact and exchange information. For example, your keyboard, mouse/trackpad, and screen together are the interface that you use to interact with your computer. Similarly, the methods of an encapsulated object are the interface that the caller of those methods can use to interact with the object's data.

Interfaces do not expose the inner details of the tool/machine/program that the user is operating — they instead provide well-defined and controlled access points for the user to operate it.
{% endhint %}

In summary, separation of concerns is achieved in OOP by identifying the *features* of an application and using objects to create an **interface** for each.

**<details><summary>Q: If you were building a social media application, what are some other features that we could encapsulate with objects?</summary>**

For example, in this "social network" application, while the `friendsManager` object manages the `friends` data, we could build another object that manages data related to messages sent between friends or one that manages an individual's account data.

</details>


## What is `this`?

Object-oriented programming and encapsulation gives us a new, incredibly powerful, and often misunderstood tool: the `this` keyword.

**When a method is invoked on an object, the `this` keyword refers to the object that is invoking the method**. As a result, `this.friends` will refer to the `friendsManager.friends` array.

```js
const friendsManager = {
  username: 'ben',
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
    console.log(this); // <-- printing this will print the execution context
    this.friends.push(newFriend);
  },
  printFriends() {
    this.friends.forEach((friend) => {
      console.log(`${this.username} is friends with ${friend}`);
    });
  }
}

// When invoking a method on friendsManager, the execution context becomes the friendsManager object
friendsManager.addFriend('cleo');
friendsManager.printFriends();
```

The `this` keyword allows the methods `addFriend` and `printFriends` to avoid needing to have the `friends` array explicitly passed into them — they just refer to `this.friends`!

<details>

<summary><strong>Q: How would you add a <code>friendsManager.removeLast()</code> method that removes the last friend that was added?</strong></summary>

```js
const friendsManager = {
  username: 'ben',
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
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
```

</details>

### Factory Functions and `this`

One major benefit of `this` is when we want to have multiple objects share the same functionality. Imagine you were to create a second object to manager a different user's friends:

```js
const bensFriends = {
  username: 'ben',
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
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

const adasFriends = {
  username: 'ada',
  friends: ['ben', 'cleo'],
  addFriend(newFriend) {
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
```

This is obviously repetitive. To simplify the logic, we can create a **factory function** — a function that creates and returns objects with the same set of properties and methods.

```js
const createFriendsManager = (username) => {
  return {
    username,
    friends: [],
    addFriend(newFriend) {
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
const adasFriends = createFriendsManager('ada');

bensFriends.addFriend('ada');
bensFriends.addFriend('bart');

adasFriends.addFriend('ben');
adasFriends.addFriend('cleo');

bensFriends.printFriends();
// ben is friends with ada
// ben is friends with bart

adasFriends.printFriends();
// ada is friends with ben
// ada is friends with cleo
```


A factory function further demonstrates the power of `this`. Both objects `bensFriends` and `adasFriends` can make use of the `addFriend` and `printFriends` methods without us needing to define that method twice in our code. When the method is invoked, the value of `this` will change according to the execution context: it will be whichever object is invoking the method.

{% hint style="info" %}
We refer to `bensFriends` and `adasFriends` as **instances of the factory**.
{% endhint %}

### A broader definition of `this`

More broadly, **the `this` keyword refers to the current "context" where it is being used**.

#### Global Scope

In the global scope, `this` will refer to an empty object:

```js
console.log(this); // prints {}
```

#### Arrow Functions

Arrow functions, no matter how they are invoked, will always inherit the value of `this` from their parent's scope.

```js
// Arrow function in the global scope
const arrow = () => {
  console.log(this);
}

arrow(); // prints {}

// Arrow Function as a method
const obj1 = {
  description: 'obj1',
  arrow: () => {
    // the object doesn't create a new scope so the parent scope is still the global scope
    console.log(this);
  }
}
obj1.arrow(); // prints {}

// Arrow function nested inside another function
const obj2 = {
  description: 'obj2',
  method() {
    const nested = () => {
      // the parent scope is now the `obj2.method` function whose `this` value is `obj2`
      console.log(this);
    }
    nested(); // prints obj2
  }
}
obj2.method();
```

#### Function Declarations

All functions made with the `function` keyword behave in the same manner.

In global function declarations and in global function expressions, `this` refers to the `global` object.

```js
// Function declaration in the global scope
function functionDeclaration() {
  console.log(this);
}
functionDeclaration(); // prints the global object

// Function expression in the global scope
const functionExpression = function() {
  console.log(this);
}
functionExpression(); // prints the global object
```

In methods of objects declared with either the method definition shorthand or defined as a function expression, `this` refers to the object invoking the method.

```js
const obj3 = {
  description: 'obj3',
  methodDefinition() {
    // method definition shorthand (equivalent to a function expression below)
    console.log(this);
  },
  methodExpression: function() {
    // method as a function expression
    console.log(this);
  },
};

obj3.methodDefinition(); // prints obj3
obj3.methodExpression(); // prints obj3
```


**Challenge: So, what is printed in each of these scenarios?**

```js
// a globally scoped variable
description = 'global scope';
console.log(globalThis);

// a globally scoped "function declaration"
function printDescription() {
  console.log(`global function declaration — ${this.description}`);
}
printDescription();


const obj = {
  description: 'an object',
  printDescription() {
    // method definition shorthand (use this!)
    console.log(`method definition — ${this.description}`);
  },
  printDescriptionFunction: function() {
    // method declared as a function expression
    console.log(`function expression method — ${this.description}`);
    const nested = () => {
      console.log(`nested arrow — ${this.description}`);
    }
    nested();
  },
  printDescriptionArrow: () => {
    // method declared as an arrow function
    console.log(`arrow method — ${this.description}`);
  },
}

obj.printDescription();
obj.printDescriptionFunction();
obj.printDescriptionArrow();
```

**<details><summary>Answer</summary>**

```
global function declaration — global scope
method definition — an object
function expression method — an object
nested arrow — an object
arrow method — global scope
```

</details>

Check out this video for a different explanation of the [this Keyword](https://www.youtube.com/watch?v=gvicrj31JOM\&ab_channel=ProgrammingwithMosh)!

## Quick Aside: Closures

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

## Restricting Access to Data Supports Encapsulation

As mentioned earlier, the goal of any programming paradigm is to write code that will behave *consistently* and *predictably*.

Consider the `friendsManager` example again. Notice that we've added a guard clause to ensure that only strings are added as friends.

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

If we are unable to ensure `friendsManager.friends` is only interacted with through the methods `addFriend` and `printFriends`, we cannot guarantee that our rules for adding new friends are enforced.

### Factory Functions and Closures Let Us Hide Data

**Encapsulation encourages consistency and predictability by restricting access to the data in objects.** 

In JavaScript, we can leverage closures and factory functions to restrict access to the `friends` array by doing the following:
* We declare the `friends` array as its own variable, outside of `friendsManager`. It can no longer be accessed directly via the object but can be referenced by the methods `addFriend` and `printFriends`

```js
// First, we make a function that returns our friendsManager. 
// This is an "outer" function
const makeFriendsManager = (username) => {
  // This variable is in the "outer" function's scope. 
  // friends is referenced by addFriend and printFriends but is not in the friendsManager itself.
  const friends = [];

  const friendsManager = {
    username, // we can leave username "public" if we don't care about how it is used or reassigned.
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

The full effect of the closure is seen when we invoke `makeFriendsManager`. After we return from `makeFriendsManager`, we can't reference `friends` anymore. However, the methods `addFriend` and `printFriends` keep their references to `friends`, even after they've been returned.

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
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') {
        throw Error('new friends must be strings');
      };
      friends.push(newFriend);
    },
    printFriends() {
      friends.forEach((friend) => {
        console.log(`I am friends with ${friend}`);
      });
    },
    getFriends() {
      return [...friends]; 
    },
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
