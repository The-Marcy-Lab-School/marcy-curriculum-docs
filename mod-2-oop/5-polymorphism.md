# Polymorphism

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-5-polymorphism)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Classes Review](#classes-review)
- [Polymorphism](#polymorphism-1)
- [Challenge](#challenge)
- [Summary](#summary)

## Key Concepts

* **Polymorphism** refers to multiple types of objects sharing the same interface (they have the same property and method names) even if their underlying implementations are different.

## Classes Review

So far in this Object-Oriented Programming unit we've learned:
* How to **encapsulate** data and methods into objects.
* How to use both **factory functions** and **classes** to create **interfaces** that provide well-defined and controlled access points for interacting with internal data.
* How to create long **prototype chains** through **inheritance**.

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
    super(first, last, age, "Software Engineering", "Marcy Lab School")
  }

  enrollInCourse(courseName) {
    if (!MarcyStudent.validCourses.includes(courseName)) {
      return 'Invalid Course Name';
    }
    super(courseName);
  }
}
```

## Polymorphism

Recall that subclasses are considered instances of their superclasses. 

```js
const ada = new MarcyStudent('Ada', 'Lovelace', 24);
console.log(ada instanceof MarcyStudent); // true
console.log(ada instanceof Student); // true
console.log(ada instanceof Person); // true
```

Because of this, we can treat instances of each class as if they are a `Person`, knowing that they will each have at the very least the properties `firstName`, `lastName`, `age`, and the methods `fullName()` and `introduce()`.

```js
const ada = new MarcyStudent('Ada', 'Lovelace', 24);
const alan = new Student('Alan', 'Turing', 27, 'Mathematics', 'Cambridge University');
const reuben = new Person('Reuben', 'Ogbonna', 36);

const people = [ada, alan, reuben];

people.forEach((person) => {
  if (person instanceof Person) {
    console.log(person.introduce());
  }
})
/* 
Hi, I'm Ada and I'm 24 years old. I am studying Software Engineering at Marcy Lab School.
Hi, I'm Alan and I'm 27 years old. I am studying Mathematics at Cambridge University.
Hi, I'm Reuben and I'm 36 years old.
*/
```

This ability to use these objects in a similar manner, knowing they all descend from the `Person` class, is called **Polymorphism**.

> Polymorphism → poly ("many") + morph ("form") → "having many forms"
>
> For example: A `Person` can come in "many forms".

**Polymorphism is the OOP concept where many different objects can be treated similarly because they share the same interface despite having different implementations.**

The impact of polymorphism is that **our program can reliably use different types of objects in the same way** if they all descend from the same parent class.

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
cars.forEach((car) => car.makeSound()); 
// since they are all Cars, they all have drive, even if they behave differently
```

**<details><summary>Answer</summary>**

Both classes implement a method called `drive` but they have their own implementations. The code that calls these methods doesn't care how each class implements `drive()` — as long as instances of `Car` and `RaceCar` have a `makeSound` method at all, the code will work.

The subclass `RaceCar` uses the `Car` `drive` method sandwiched between two of its own `console.log` statements.

`Car` objects can come in many forms (they look the same, but they may behave differently).

</details>

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