# 3. Private & Static

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-3-private-properties-static-methods)!
{% endhint %}

**Table of Contents:**

- [Key Concepts](#key-concepts)
- [Private Properties](#private-properties)
  - [`#privateField` Notation](#privatefield-notation)
  - [Private Methods](#private-methods)
- [Static Properties and Methods](#static-properties-and-methods)
  - [Tracking All Instances](#tracking-all-instances)
- [Quiz!](#quiz)
  - [What is the purpose of using the # notation for a property in a class?](#what-is-the-purpose-of-using-the--notation-for-a-property-in-a-class)
  - [How do you call a static method on a class?](#how-do-you-call-a-static-method-on-a-class)
- [Challenge: Car Class](#challenge-car-class)


## Key Concepts

* The `#` symbol can be placed in front of any property or method to make it private.
* Properties and methods should be made private when...
  * **Encapsulation and Data Protection:** If we want to ensure that the property is managed only by the class methods and not by any external code, the property should be private. This will protect against unexpected changes to the object's state. 
  * **Internal Implementation Details:** If a property/method is only used within the class itself (like a helper function), it should be private.
* The `static` keyword can be placed in front of any property or method to make it belong to the class itself rather than the instances of the class.

## Private Properties

In the previous lectures, we've been creating classes and instances, but we've left some of our properties open to direct manipulation. For example, in this `Person` class, we have a `friends` array that can be directly mutated without using the provided `addFriend` method:

```js
class Person {
  friends = [];

  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  addFriend(newFriend) {
    if (typeof newFriend !== 'string') {
      return;
    }
    this.friends.push(newFriend);
  }

  greet() {
    console.log(`Hi, I'm ${this.name}. I am ${this.age} years old and I have ${this.friends.length} friends.`);
  }
}

const ada = new Person('Ada Lovelace', 36)
ada.addFriend('Alan');
ada.addFriend('Nikola');

ada.friends.push(null);
ada.friends.push(false);
ada.greet(); // Hi, I'm Ada Lovelace. I am 36 years old and I have 4 friends.

// But do you have 4 friends Ada?
```

Ideally, we wouldn't want the `friends` property to be directly accessible and modifiable. This is where the concept of **private properties** comes in.

<details>

<summary><strong>Q: What are the downside of making the `friends` property directly accessible?</strong></summary>

It is possible to improperly modify the `friends` array and produce unexpected results.

</details>

### `#privateField` Notation

We can make any property private by declaring it as a **private field** using the `#privateField` syntax. We can reference that property anywhere in the class using `this.#privateField`:

```js
class Person {
  #friends = [];

  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  
  addFriend(newFriend) {
    if (typeof newFriend !== 'string') {
      return;
    }
    this.#friends.push(newFriend);
  }

  greet() {
    console.log(`Hi, I'm ${this.name}. I am ${this.age} years old and I have ${this.#friends.length} friends.`);
  }

  getFriends() {
    // return a copy to avoid exposing the private array
    return [...this.#friends];
  }
}

const ada = new Person('Ada Lovelace', 36)
ada.addFriend('Alan');
ada.addFriend('Nikola');

// getFriends returns a copy that keeps the private array protected
const friends = ada.getFriends();
friends.push(null);
friends.push(false);
console.log(ada.getFriends()); // [ 'Alan', 'Nikola' ]

ada.greet(); // Hi, I'm Ada Lovelace. I am 36 years old and I have 2 friends.

console.log(ada.friends); // undefined
console.log(ada.#friends); // Error
```

Notice that the new `getFriends()` method returns a copy of the private `#friends` array, making it possible to *see* its contents but ensuring that the original array is not able to be mutated.

**Make properties private for encapsulation and data protection:** If we want to ensure that the property is managed only by the class methods and not by any external code, the property should be private. This will protect against unexpected changes to the object's "state" (the data held by an object). 

**<details><summary>Q: If you were designing a `User` class, what properties might be private?</summary>**

Examples could include:
- password
- social security number
- bank account numbers
- home address

</details>

### Private Methods

Methods can also be marked as private by putting a `#` in front of the method name. 

For example, here we have a `Die` class with a public `roll()` method that lets the caller roll a die any number of times. It uses the private method `#getSingleRoll()` as a "helper" method:

```js
class Die {
  constructor(sides) {
    this.sides = sides;
  }

  roll(times) {
    const rolls = [];
    for (let i = 0; i < times; i++) {
      rolls.push(this.#getSingleRoll());
    }
    return rolls;
  }
  
  // a private "helper" method
  #getSingleRoll() {
    return Math.ceil(Math.random() * this.sides);
  }
}

const sixSided = new Die(6);
console.log(sixSided.roll(3)); // Prints a random set of three rolls such as [ 2, 3, 6 ]
```

**Make methods private to hide internal implementation details:** If a property/method is only used within the class itself (like a helper function), it should be private.

**<details><summary>Q: If you were designing a `User` class, what internal helper methods might exist that you would make private?</summary>**

Examples could include:
- `#isValidPassword()` could be used internally to verify that a given password satisfies all requirements (e.g. is at least 8 characters long, uses 3 symbols, etc...)
- `#formatDisplayName()` could be used to standardize how usernames are displayed so that they are all lowercase and replace spaces with underscores 

</details>


## Static Properties and Methods

There are cases where it's more appropriate to define methods/properties that are associated with the class itself rather than its instances. These are called **static methods/properties**.

Static properties and methods are defined using the `static` keyword before the property/method name. Check out the `Circle` class below:

```js
class Circle {
  // this property is owned by the Circle class: Circle.PI
  static PI = 3.14159;

  constructor(radius) {
    // this property is owned by each Circle instance: instance.radius
    this.radius = radius;
  }

  // An "instance method": instance.getArea()
  getArea() {
    // notice how we can reference Circle.PI inside this instance method
    return Circle.PI * this.radius * this.radius;
  }

  // A "static method": Circle.isCircle(shape)
  static isCircle(shape) {
    return shape instanceof Circle;
  }
}

// The PI value is shared by ALL circles and doesn't depend on any circle instance
console.log(Circle.PI); // 3.14159

// Only the Circle class can verify if an object is a Circle
const myCircle = new Circle(10);
console.log(Circle.isCircle(myCircle)); // true

const randomObject = {};
console.log(Circle.isCircle(randomObject)); // false

// each circle instance has `getArea` which internally references the Circle.PI property
console.log(myCircle.getArea()); // 314.159
```

Since the value of `PI` is the same for all circles, it can be defined once as a static property of the `Circle` class rather than having it be defined for each circle instance.

**Make properties and methods static when they are independent from instance state or have shared behavior across instances**: If a property or method does not depend on the state of a specific instance of the class, it can be `static`. Similarly, if a property or method represents data or functionality that is common to all instances of a class, it can be `static`.

### Tracking All Instances

It is common to track all instances of a class using a combination of static and private properties and methods.

```js
class Person {
  #friends = [];
  static #allPeople = []; // a private AND static array to track all Person instances

  constructor(name, age) {
    this.name = name;
    this.age = age;

    // Add the instance being created (this) into the static allPeople array
    Person.#allPeople.push(this);
  }
  
  addFriend(newFriend) {
    if (typeof newFriend !== 'string') {
      return;
    }
    this.#friends.push(newFriend);
  }

  greet() {
    console.log(`Hi, I'm ${this.name}. I am ${this.age} years old and I have ${this.#friends.length} friends.`);
  }

  getFriends() {
    // return a copy to avoid exposing the private array
    return [...this.#friends];
  }

  static getPeopleCount() {
    return Person.#allPeople.length;
  }

  static findPersonByName(name) {
    return Person.#allPeople.find((person) => person.name === name);
  }
}

const ada = new Person('Ada Lovelace', 36);
const alan = new Person('Alan Turing', 30);
const reuben = new Person('Reuben Ogbonna', 37);

console.log(Person.getPeopleCount()); // 3
console.log(Person.findPersonByName('Alan Turing')); 
// Person { name: 'Alan Turing', age: 30 }
```

**<details><summary>Q: If you were designing a `User` class, what properties and methods might be static and belong to the `User` class?</summary>**

Here are some static properties that a `User` class might have:
- `static allUsers = []`
- `static minimumAge = 13`
- `static usernameMaximumLength = 20`
- `static allowablePasswordSpecialCharacters = ['?', '!', '@']`

Here are some static methods that a `User` class might have:
- `static getAllUsers()`
- `static findUserByUsername(username)`
- `static isValidPassword(password)`

The decision to make a method static or private is a tricky one and there may not always be a clear answer! You may notice that the `isValidPassword` method could be either private or static depending on how it is implemented.

The key is to be consistent with your decisions.

</details>

## Quiz!

The following code snippet has some errors! What is it?

```js
class Counter {
  #value = 0;

  increment() {
    this.value++;
  }

  getValue() {
    return this.value;
  }

  reset() {
    this.value = 0;
  }
}
```

<details>

<summary>Answer</summary>

The `value` field is a private field so it should be referenced with `this.#value` instead of `this.value` in each of the instance methods.

</details>

### What is the purpose of using the # notation for a property in a class?

<details>

<summary>Answer</summary>

It indicates a private property.

The `#` notation in JavaScript is used to indicate private fields within a class. It means that the property is intended to be private and not directly accessible from outside the class. This helps in encapsulation, ensuring that the property is not accidentally or intentionally modified from external code.

</details>

### How do you call a static method on a class?

<details>

<summary>Answer</summary>

`ClassName.staticMethod()`

To call a static method in JavaScript, you use the class name followed by the method name. Static methods are associated with the class itself, not with instances of the class. Therefore, you don't need to create an instance to call a static method. The correct syntax is `ClassName.staticMethod()`.

</details>

## Challenge: Car Class

Lets create a class called `Car` that has the following:

* Utilize a private property #licensePlate for encapsulation.
* Include public properties: `make`, `model`, and `year`.
* Implement an instance method `displayInfo()` that logs information about the car.
* Implement another instance method `honkHorn()` to simulate honking the car's horn in the console.
* Use a static method called `generateLicensePlate()` that a license plate `'ABC123'`.
