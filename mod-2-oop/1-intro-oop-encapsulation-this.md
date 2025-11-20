# 1. Intro to OOP: Encapsulation and This

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

## Key Concepts

* **Encapsulation** refers to the bundling of data with the methods that operate that data into a single object. To achieve encapsulation, we create *interfaces* with *data hiding*
  * The *properties* and *methods* of an encapsulated object provide an **interface** (a "shared boundary") that allow other parts of the application to interact with the object's data.
* * A **Factory** is a type of function that creates and returns objects with a defined set of properties and methods.
* An **instance** is a single object returned from a factory that maintains its own set of data that is distinct from other instances made by the factory.
* The **`this`** keyword in JavaScript refers to the current execution context. In methods, `this` refers to the instance (object) invoking the method (i.e. "this instance").

## Functional Programming vs. Object-Oriented Programming

When designing software, we should seek to adhere to principles that will guide us towards creating **consistent** and **predictable** software. The two most common approaches (or "paradigms") are:
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

In this example, the `friendsManager` object contains both the list of `friends` as well as the actions that you can perform with that list: `addFriend` and `printFriend`. 

We can call the *properties* and *methods* the **"interface"** for this feature of managing friends.

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
