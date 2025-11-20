# Polymorphism

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-5-polymorphism)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Classes Review](#classes-review)
- [Polymorphism](#polymorphism-1)
  - [Why does this matter?](#why-does-this-matter)
  - [Inheritance Is Not Required For Polymorphism](#inheritance-is-not-required-for-polymorphism)
- [Challenge](#challenge)
- [Summary](#summary)

## Key Concepts

* **Polymorphism** is the OOP concept where different types of objects can be treated the same way because they share the same interface (the same method names) even though each type implements those methods differently.
  * Inheritance is not required to achieve polymorphism. However, inheritance guarantees polymorphism and improves the reusability of our code.

## Classes Review

So far in this Object-Oriented Programming unit we've learned:
* How to **encapsulate** data and methods into objects.
* How to use both **factory functions** and **classes** to create **interfaces** that provide well-defined and controlled access points for interacting with internal data.
* How to use **inheritance** to reuse and extend class interfaces.
* How to identify where methods are defined in a **prototype chain**.

We can see all of this in action in this prototype chain: `MarcyStudent` → `Student` → `Person`

```js
class Person {
  constructor(first, last, age) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
  }
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  introduce() {
    return `Hi, I'm ${this.firstName} and I'm ${this.age} years old.`;
  }
}

class Student extends Person {
  courses = [];

  constructor(first, last, age, subject, school) {
    super(first, last, age); 
    this.subject = subject;
    this.school = school;
  }

  introduce() {
    return `${super.introduce()}. I am studying ${this.subject} at ${this.school}.`
  }
  
  enrollInCourse(courseName) {
    this.courses.push(courseName);
  }
}

class MarcyStudent extends Student {
  static validCourses = ['Leadership & Development', 'Technical Interview Prep', 'Technical Lecture'];
  
  constructor(first, last, age) {
    super(first, last, age, "Software Engineering", "Marcy Lab School");
  }

  enrollInCourse(courseName) {
    if (!MarcyStudent.validCourses.includes(courseName)) {
      return 'Invalid Course Name';
    }
    super.enrollInCourse(courseName);
  }
}
```

Now, let's introduce the final pillar of object-oriented programming: **polymorphism**.

## Polymorphism

Recall that subclasses are considered instances of their superclasses. 

```js
const ada = new MarcyStudent('Ada', 'Lovelace', 24);
console.log(ada instanceof MarcyStudent); // true
console.log(ada instanceof Student); // true
console.log(ada instanceof Person); // true
```

**Polymorphism is the OOP concept where different types of objects can be treated the same way because they share the same interface (the same method names) even though each type implements those methods differently.**

> Polymorphism → poly ("many") + morph ("form") → "having many forms"
>
> For example: A `Person` can take "many forms" — it could be a `Person`, a `Student`, or a `MarcyStudent`, but they can all be treated as a `Person` because they share the same interface.

In this example, `ada`, `alan`, and `reuben` each have the method `introduce()` despite having different implementations. This means that we can write code like this:

```js
const ada = new MarcyStudent('Ada', 'Lovelace', 24);
const alan = new Student('Alan', 'Turing', 27, 'Mathematics', 'Cambridge University');
const reuben = new Person('Reuben', 'Ogbonna', 36);

const people = [ada, alan, reuben];

// This method works with any type of Person
const printIntroduction = (person) => {
  console.log(`Introducing ${person.fullName()}:`);
  console.log(person.introduce());
};

people.forEach(printIntroduction);
// Introducing Ada Lovelace:
// Hi, I'm Ada and I'm 24 years old. I am studying Software Engineering at Marcy Lab School.
// Introducing Alan Turing:
// Hi, I'm Alan and I'm 27 years old. I am studying Mathematics at Cambridge University.
// Introducing Reuben Ogbonna:
// Hi, I'm Reuben and I'm 36 years old.
```

Polymorphism works *because* of the prototype chain - when we call `person.fullName()` or `person.introduce()`, JavaScript walks up the prototype chain to find the most specific implementation.

**Question: Take a look at the example below. How would you explain how it demonstrates Polymorphism?**

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
  drive() {
    console.log("Vah... Vah...");
    super.drive();
    console.log("WHEEEEEEE!!!!");
  }
}

const car1 = new Car("Chevy", "Cobalt");
const car2 = new RaceCar("Ferrari", "Portofino");
const car3 = new Car("Tesla", "Model X");

const cars = [car1, car2, car3];
cars.forEach((car) => car.drive()); 
// since they are all Cars, they all have drive, even if they behave differently
```

**<details><summary>Answer</summary>**

Both `Car` and `RaceCar` implement the `drive()` method, but with different behaviors. The code `cars.forEach((car) => car.drive())` works on all cars regardless of their specific type - we can treat them uniformly because they share the same interface. This is **polymorphism**: same method name, different implementations, treated the same way in our code.

</details>

### Why does this matter?

Without polymorphism, a function like `printIntroduction` would need to confirm that incoming `person` objects have the `fullName()` and `introduce()` methods. Without this check, an error could be thrown for trying to invoke an undefined method.

```js
// Without polymorphism, you'd need to check if methods exist:
const printIntroduction = (person) => {
  if (person.fullName && person.introduce) {
    console.log(`Introducing ${person.fullName()}`);
    console.log(person.introduce());
  } else {
    console.log("This object can't introduce itself!");
  }
}

// With polymorphism: just trust the interface!
const printIntroduction = (person) => {
  console.log(`Introducing ${person.fullName()}`);
  console.log(person.introduce());
}
```

**When to use polymorphism:** Polymorphism shines when you're working with collections of related objects (like arrays of different users, or lists of different vehicles) and you want to treat them uniformly. If you find yourself writing lots of if `(type === 'X')` or `instanceof` checks, that's a sign polymorphism could simplify your code.

### Inheritance Is Not Required For Polymorphism

Look at the code below, does it use inheritance? Does it demonstrate polymorphism?

```js
class Dog() {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} said woof`);
  }
}

class Cat() {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} said meow`);
  }
}

const animals = [new Dog('snoopy'), new Cat('garfield')];
animals.forEach(animal => animal.speak());
// snoopy said woof
// garfield said meow
```

This code does NOT use inheritance but it DOES demonstrate polymorphism, though a looser form of polymorphism. Since `Cat` and `Dog` each define a `speak` method, instances of these classes can be treated the same as "things that speak". 

> This kind of loose polymorphism is often referred to as **"duck typing"** ("if it walks like a duck and it quacks like a duck, then it must be a duck").

Though not required, *inheritance guarantees polymorphic behavior* through inherited methods. It also reduces the amount of code we have to rewrite. 

In this example, if `Cat` and `Dog` had a shared `Animal` parent class, they could inherit its `constructor()`:

```js
class Animal() {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} said something`);
  }
}

class Dog() {
  speak() {
    console.log(`${this.name} said woof`);
  }
}
class Cat() {
  speak() {
    console.log(`${this.name} said meow`);
  }
}
```

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

Finally, create an array containing both the user and admin, and loop through them calling `login()` on each. Notice how polymorphism lets you treat them uniformly even though one is a User and one is an Admin."

```js
const users = [user, admin];
users.forEach(u => u.login()); // polymorphism in action!
```

## Summary

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
cars.forEach((car) => car.drive()); 
// since they are all Cars, they all have drive, even if they behave differently
```