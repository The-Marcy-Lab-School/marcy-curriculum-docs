# Inheritance & Polymorphism

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-1-3-inheritance-polymorphism-f23)!
{% endhint %}

**Table of Contents:**
- [Inheritance \& "Is A" Relationships](#inheritance--is-a-relationships)
- [Array is a Subclass of Object](#array-is-a-subclass-of-object)
- [Establishing Inheritance Between Custom Classes](#establishing-inheritance-between-custom-classes)
- [Extends and Super](#extends-and-super)
- [Refactor Challenge](#refactor-challenge)
- [Polymorphism](#polymorphism)
- [Using Classes with Other Classes](#using-classes-with-other-classes)
- [Challenge: User \& Admin](#challenge-user--admin)
- [Challenge](#challenge)
- [Summary](#summary)



## Inheritance & "Is A" Relationships

**Inheritance** is a pillar of object-oriented programming. It describes a relationship between two classes: a **subclass** that inherits methods from a **superclass**. As a result, instances of the sub-class can use methods defined in a super-class. 

We call this an "Is A" relationship

![A Person class sits at the top of the "tree". A Doctor, a Professor, and a Student class sit below and all inherit from the Person class. A GraduateStudent class inherits from the Student class.](./img/inheritance.png)

**Question: What is the inheritance relationship between the `Professor` class and the `Person` class? What about the `GraduateStudent` class and the `Person` class?**

## Array is a Subclass of Object

The `Array` class is a sub-class of the `Object` class which is the super-class.

Every Array instance gets methods from the `Array.prototype` which inherits methods from the `Object.prototype`. Therefore, all arrays can use `Object.prototype` methods like `toString()`.

Try running the following code:

```js
const arr = [1,2,3];

console.log(arr.toString());
console.log(arr); // expand the prototype chain to find the .toString() method

console.log(typeof arr); 
console.log(arr instanceof Array);
console.log(arr instanceof Object);
```

## Establishing Inheritance Between Custom Classes

Imagine we have a `Person` class. I want to make another class called `Programmer` that can do everything a `Person` can, with some additional properties and behaviors that only instances of `Programmer` will have.

**How NOT to do it**:
```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
    this.friends = [];
  }
  makeFriend(friend) {
    this.friends.push(friend)
    console.log(`Hi ${friend}, my name is ${this.name}, nice to meet you!`);
  }
  doActivity(activity) {
    console.log(`${this.name} is ${activity}`);
  }
}

class Programmer {
  constructor(name, age, language) {
    this.name = name;
    this.age = age;
    this.friends = [];
    this.favoriteLanguage = language;
  }
  makeFriend(friend) {
    this.friends.push(friend)
    console.log(`Hi ${friend}, my name is ${this.name}, nice to meet you!`);
  }
  doActivity(activity) {
    console.log(`${this.name} is ${activity}`);
  }
  code() {
    this.doActivity(`writing some ${this.favoriteLanguage} code.`);
  }
}
```

Q: What bad practice exists this code?

## Extends and Super
To remove the repetitive code AND to establish a relationship between `Programmer` and `Person`, we use the `extends` and `super` keywords to define our `Programmer` class:

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
    this.friends = [];
  }
  makeFriend(friend) {
    this.friends.push(friend)
    console.log(`Hi ${friend}, my name is ${this.name}, nice to meet you!`);
  }
  doActivity(activity) {
    console.log(`${this.name} is ${activity}`);
  }
}

class Programmer extends Person {
  constructor(name, age, language) {
    super(name, age);                 // invoke the Person constructor, setting the name, age, and friends properties on `this`
    this.favoriteLanguage = language; // add a favoriteLanguage property only for Programmers
  }
  
  // makeFriend is inherited
  // doActivity is inherited
  
  code() { // a new method only Programmer instances can use
    this.doActivity(`writing some ${this.favoriteLanguage} code.`);
  }
}
```

## Refactor Challenge

Take the first code snippet and refactor it such that the `Programmer` class extends the `Person` class.

Using these classes, do the following:

* Create an instance of `Programmer`
* Invoke all of the methods that the instance inherits
* Use the `instanceof` operator to confirm that your instance is a `Programmer` AND a `Person`

Then, with a partner, discuss these questions:

**Question 1: What does `extends` do?**

**Question 2: What does `super` do?**

**Question 3: What do we know about the relationship between a `Programmer` and a `Person`?**

**Question 4: How does the `code` method work?**

<details><summary>Ben's Answer</summary>

* `extends` makes the `Programmer` inherit methods from `Person`. It sets `Person.prototype` as the prototype for `Programmer.prototype`
* `super()` invokes the `Person` constructor function using its own value of `this`. Any properties that the `Person` constructor sets on `this` will  be set on `Programmer`.
* `Programmer` is said to be a **subclass** of `Person`. 
* `Person` is said to be a **superclass** of `Programmer`.
* `Programmer` will inherit properties and methods 
from `Person`
* Instances of `Programmer` are also instances of `Person`
* `code` invokes the `doActivity` method inherited from `Person.prototype`

</details>

## Polymorphism

Polymorphism means "many forms".

Polymorphism is a concept in object-oriented programming where multiple types of objects share "signatures" (they have the same property and method names even if their values/implementations are different).

The impact of polymorphism is that **our program can reliably use different types of objects in the same way** if they all descend from the same parent class.

```js
class Person {
  constructor(name) {
    this.name = name;
    this.friends = [];
  }
  makeFriend(friend) {
    this.friends.push(friend)
    console.log(`Hi ${friend}, my name is ${this.name}, nice to meet you!`);
  }
}

class Programmer extends Person {
  // the constructor can be inherited too as long as the signature doesn't need changing

  // here we totally override the parent method
  makeFriend(friend) {
    this.friends.push(friend);
    console.log('tap tap tap');
  }
}

class Musician extends Person {
  constructor(name, instrument) {
    super(name);
    this.instrument = instrument;
  }
  makeFriend(friend) { // Method Override
    super.makeFriend(friend)
    console.log(`I can play the ${this.instrument}. Do you know any instruments?`);
  }
}

const carmen = new Person("Carmen");
const reuben = new Programmer("Reuben");
const ben = new Musician("Ben", "Piano");
const people = [carmen, reuben, ben];

// Ben, reuben, and carmen are all hanging out. 
// Maya enters the room and wants to be friends with everyone!
// Because everyone is a Person, we can do this:
people.forEach(person => person.makeFriend("Maya"));

// Output:
// "Hi Maya, my name is Carmen, nice to meet you!"
// "Hi Maya, my name is Reuben, nice to meet you!"
// "Hi Maya, my name is Ben, nice to meet you!"
// "I can play the Piano. Do you know any instruments?"
```

This demonstrates polymorphism because `ben`, `reuben`, and `carmen` are all descendants of `Person` which we know defines a `makeFriend` method. Even though `reuben` and `carmen` are different subtypes, we can treat them as `Person` objects as well. 

A `Person` can come in "many forms".

**Q: What does `super.makeFriend(friend)` do?**

<details><summary>Ben's Answer</summary>

  `super.makeFriend(friend)` will call the superclass's `makeFriend` method, adding the `friend` argument to the `this.friends` array and printing out the greeting message. It is common when method overriding to invoke the superclass's version of the method and then adding on additional statements to execute.
  
</details>

Let's look at another example of polymorphism. In this example, we have a `Car` class and a `RaceCar` subclass. 

```js
class Car {
  constructor(make, model) {
    this.make = make;
    this.model = model;
  }
  
  drive() {
    console.log("Vrooom");
  }
}

class RaceCar extends Car {

  // the constructor can be inherited too as long as the signature doesn't need changing
  
  drive() { // Method Override
    console.log("Vah... Vah...");
    super.drive(); // invoke the parent class method
    console.log("WHEEEEEEE!!!!");
  }
}

const car1 = new Car("Chevy", "Cobalt");
const car2 = new RaceCar("Ferrari", "Portofino");
const car3 = new Car("Tesla", "Model X");

const cars = [car1, car2, car3];
cars.forEach((car) => car.makeSound()); 
// since they are all Cars, they all have drive, even if they behave differently
```

Both classes implement a method called `drive` but they have their own implementations. The code that calls these methods doesn't care how each class implements `drive()` — as long as instances of `Car` and `RaceCar` have a `makeSound` method at all, the code will work.

The subclass `RaceCar` uses the `Car` `drive` method sandwiched between two of its own `console.log` statements.

`Car` objects can come in many forms (they look the same, but they may behave differently).

## Using Classes with Other Classes

**Challenge: Refactor the `makeFriend` method so that instead of adding a friend's name, it takes in a Person object. When a person is added as a friend, both person objects should have each other as friends.**

<details><summary>Ben's Solution</summary>

We only have to modify the `Person` class and all subclasses will inherit the new behavior. Instead of passing in a friend's name, pass in the entire Person object and have both friends add each other to the friend list.

We have to be careful to not create an infinite recursion. We will end up with a **circular reference** though.

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
    this.friends = [];
  }
  makeFriend(friend) {
    if (this.friends.includes(friend)) {
      return;
    }
    
    this.friends.push(friend)
    console.log(`Hi ${friend.name}, my name is ${this.name}, nice to meet you!`);
    
    friend.makeFriend(this);
  }
  doActivity(activity) {
    console.log(`${this.name} is ${activity}`);
  }
}

const ben = new Person("Ben", 28);
const carmen = new Person("Carmen", 22);
const reuben = new Person("Reuben", 35);

ben.makeFriend(carmen);
ben.makeFriend(reuben);

console.log(ben, reuben, carmen)
```

</details>

## Challenge: User & Admin

## Challenge

Create two classes, `User` and `Admin`.

A `User` should have the following properties:
* `username` a string provided to the constructor
* `isOnline` with a default value `false`

A `User` should have the following methods:
* `login` sets `isOnline` to `true` and prints `<username> has logged in!`
* `logout` sets `isOnline` to `false` and prints `<username> has logged out!`

An `Admin` should be a subclass of `User`. It should also have:
* A property `isAdmin` set to `true`
* A method called `doSecretAdminStuff` that just prints a message `"Doing secret admin stuff"`.

Then, create a user instance and an admin instance and demonstrate how to use all of their methods.

## Summary

* **Inheritance** occurs when a **child class** inherits properties and methods from a **parent class**
  * The `extends` keyword creates this relationship
  * The `super` keyword references the parent class
  * You can invoke `super()` to invoke the parent class constructor.

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
    this.friends = [];
  }
  makeFriend(friend) {
    this.friends.push(friend)
    console.log(`Hi ${friend}, my name is ${this.name}, nice to meet you!`);
  }
  doActivity(activity) {
    console.log(`${this.name} is ${activity}`);
  }
}

class Programmer extends Person {
  constructor(name, age, language) {
    super(name, age);                 // invoke the Person constructor, setting the name, age, and friends properties on `this`
    this.favoriteLanguage = language; // add a favoriteLanguage property only for Programmers
  }
  
  // makeFriend is inherited
  // doActivity is inherited
  
  code() { // a new method only Programmer instances can use
    this.doActivity(`writing some ${this.favoriteLanguage} code.`);
  }
}
```

* **Polymorphism** ("many forms") occurs when multiple types of objects share "signatures" (they have the same property and method names).
  * The impact of polymorphism is that **our program can reliably use different types of objects in the same way** if they all descend from the same parent class.
  * Method Overriding means that method signatures are the same even if their implementations are different

```js
class Car {
  constructor(make, model) {
    this.make = make;
    this.model = model;
  }
  
  drive() {
    console.log("Vrooom");
  }
}

class RaceCar extends Car {

  // the constructor can be inherited too as long as the signature doesn't need changing
  
  drive() { // Method Override
    console.log("Vah... Vah...");
    super.drive(); // invoke the parent class method
    console.log("WHEEEEEEE!!!!");
  }
}

const car1 = new Car("Chevy", "Cobalt");
const car2 = new RaceCar("Ferrari", "Portofino");
const car3 = new Car("Tesla", "Model X");

const cars = [car1, car2, car3];
cars.forEach((car) => car.makeSound()); 
// since they are all Cars, they all have drive, even if they behave differently
```