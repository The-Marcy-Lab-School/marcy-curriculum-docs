# Inheritance

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-4-inheritance)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Inheritance](#inheritance-1)
  - [A Person and a Student](#a-person-and-a-student)
  - [`extends`](#extends)
  - [`super`](#super)
- [Prototype Chain: Subclasses of Subclasses](#prototype-chain-subclasses-of-subclasses)
  - [Array is a Subclass of Object](#array-is-a-subclass-of-object)
- [Practice \& Review](#practice--review)
  - [Coding Challenge](#coding-challenge)
  - [Study Questions](#study-questions)

## Key Concepts

* **Inheritance** describes a relationship between two classes: a **subclass** that inherits methods from a **superclass**. Instances of the subclass can reuse and add to the methods defined in a superclass. 
* The **`Subclass extends Superclass`** syntax defines an inheritance relationship between two classes
* **The `super` keyword references the superclass of a given subclass:**
  1. We can invoke `super()` in the subclass constructor to invoke the superclass's `constructor()`.
  2. We can invoke `super.method()` in an **overridden method** to use the behavior of that same method from the superclass.
* The **prototype chain** describes the linked structure where each object inherits methods from a prototype, which may inherit methods from another prototype, forming a chain.
  * When you access a property or method, JavaScript walks up this chain until it finds what you're looking for (or reaches the end).

## Inheritance

### A Person and a Student

Imagine we have this `Person` class. 

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

const ada = new Person('Ada', 'Lovelace', 30);
console.log(ada.fullName()); // Ada Lovelace
console.log(ada.introduce()); // Hi, I'm Ada and I'm 30 years old.
```

I want to make another class called `Student` that can do everything a `Person` can, with some additional properties and behaviors that only instances of `Student` will have.

```js
class Student {
  courses = [];

  constructor(first, last, age, subject, school) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.subject = subject;
    this.school = school;
  }
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  introduce() {
    return `Hi, I'm ${this.firstName} and I'm ${this.age} years old. I am studying ${this.subject} at ${this.school}.`;
  }
  enrollInCourse(courseName) {
    this.courses.push(courseName);
  }
}

const alan = new Student('Alan', 'Turing', 24, 'Computer Science', 'Marcy Lab School');
console.log(alan.fullName()); // Alan Turing
console.log(alan.introduce()); // Hi, I'm Alan and I'm 24 years old. I am studying Computer Science at Marcy Lab School.

ada.enrollInCourse('Leadership & Development');
ada.enrollInCourse('Technical Interview Prep');
console.log(ada.courses); // [ 'Leadership & Development', 'Technical Interview Prep' ]
```

**<details><summary>Question: What best practice is NOT demonstrated by this code?</summary>**

This code breaks the DRY principle (Don't Repeat Yourself). In the `Student` class we duplicate the code for the `fullname` method and much of the `constructor` and `introduce` methods stay the same.

</details> 

### `extends`

A `Student` is essentially a specific kind of `Person`. In computer science, we would say that **"A Student is a Person"** to explain this relationship. 

In JavaScript, we formalize this relationship using the `Subclass extends Superclass` syntax:

```js
const Person = require('./Person');

class Student extends Person {

}

const ada = new Student('Ada', 'Lovelace', 30);
console.log(ada.fullName()); // Ada Lovelace
console.log(ada.introduce()); // Hi, I'm Ada and I'm 30 years old.
```

With just this `extends` keyword, **every instance of `Student` "inherits" the properties and methods from the `Person` class.**

Furthermore, instances of `Student` are *also* instances of `Person`.

```js
console.log(ada instanceof Student);  // true
console.log(ada instanceof Person);   // true
```

### `super`

We don't want our `Student` class to just be a carbon-copy of the `Person` class. When we want to add new functionality to a subclass and still leverage the functionality of the superclass, we use the `super` keyword.

**The `super` keyword references the superclass of a given subclass:**

{% tabs %}

{% tab title="Student.js" %} 

```js
const Person = require('./Person');

class Student extends Person {
  // A field unique to Students
  courses = [];

  constructor(first, last, age, subject, school) {
    // Invoke the superclass constructor
    super(first, last, age); 

    // Assign instance properties unique to Students
    this.subject = subject;
    this.school = school;
  }
  
  // fullName is inherited from Person

  // Overriding the introduce method
  introduce() {
    // Invoke the Person's version of the introduce method
    return `${super.introduce()}. I am studying ${this.subject} at ${this.school}.`
  }
  
  // A method unique to Student
  enrollInCourse(courseName) {
    this.courses.push(courseName);
  }
}

const ada = new Student('Ada', 'Lovelace', 30, 'Computer Science', 'Marcy Lab School');
console.log(ada.fullName()); // Ada Lovelace
console.log(ada.introduce()); // Hi, I'm Ada and I'm 30 years old. I am studying Computer Science at Marcy Lab School.

ada.enrollInCourse('Leadership & Development');
ada.enrollInCourse('Technical Interview Prep');
console.log(ada.courses); // [ 'Leadership & Development', 'Technical Interview Prep' ]
```

{% endtab %}

{% tab title="Person.js" %} 

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

module.exports = Person;
```

{% endtab %}

{% endtabs %} 

The `super` keyword is most often used in one of two ways:

1. We can invoke `super()` in the subclass constructor to invoke the superclass's `constructor()`. 
     - In the `Student` constructor, `super()` invokes the `Person`'s constructor which assigns the properties `firstName`, `lastName`, and `age` to the new `Student` instance.
2. We can invoke `super.method()` in an **overridden method** to use the behavior of that same method from the superclass.
    - The `introduce()` method is overridden in the `Student` class but uses `super.introduce()` to invoke the `introduce` method defined in the `Person` class.

Notice that the `fullName` method is inherited from the `Person` class and is NOT overridden. It will behave the same way for student instances and person instances.

## Prototype Chain: Subclasses of Subclasses

Suppose that a `GraduateStudent` class simply extends `Student` without overriding any methods. We create a chain of inheritance: `GraduateStudent` → `Student` → `Person`. 

```js
class GraduateStudent extends Student {
  // inherits all methods from Student including the constructor
}
const ada = new GraduateStudent('Ada', 'Lovelace', 30, 'Computer Science', 'Marcy Lab School');

console.log(ada.fullName()); // where is this method defined?
```

We can refer to this series of connected classes as a **prototype chain**: The linked structure where each object inherits methods from a prototype, which may inherit methods from another prototype, forming a chain:
* The `ada` object inherits methods from the `GraduateStudent` prototype
  * The `GraduateStudent` prototype inherits methods from the `Student` prototype
    * The `Student` prototype inherits methods from the `Person` prototype
      * The `Person` prototype inherits methods from the `Object` prototype
      * The `Object` prototype is the end of the chain.

When you access a property or method, JavaScript walks up this chain until it finds what you're looking for (or reaches the end).

For example, when we invoke `ada.fullName()`, JavaScript will look for the `fullName()` definition in the following order:

- In the object itself (the `ada` instance properties)
- In the class prototype (the shared methods in the `GraduateStudent` class)
- In the parent class prototype (the inherited methods in the `Student` class)
- In the parent class's parent class prototype (the inherited methods in the `Person` class)

We can see this if we look at the "own properties" of each class's prototype in the chain:

```js
Object.getOwnPropertyNames(ada);
// [ 'firstName', 'lastName', 'age', 'courses', 'subject', 'school' ]
Object.getOwnPropertyNames(GraduateStudent.prototype);
// [ 'constructor' ]
Object.getOwnPropertyNames(Student.prototype);
// [ 'constructor', 'introduce', 'enrollInCourse' ]
Object.getOwnPropertyNames(Person.prototype);
// [ 'constructor', 'fullName', 'introduce' ] <--- here it is
```

This is somewhat easier to see when we use the Node Debugger:

![](img/prototype-chain-debugger.png)

### Array is a Subclass of Object

This is all precisely why `typeof []` gives us `"object"` — every array is an instance of the the `Array` class which is a subclass of the `Object` class!

Every Array instance gets methods from the `Array.prototype` which inherits methods from the `Object.prototype`. Therefore, all arrays can use `Object.prototype` methods like `toString()`.

Try running the following code:

```js
debugger;
const arr = [1,2,3];

console.log(arr.toString());
console.log(arr); // expand the prototype chain to find the .toString() method

console.log(typeof arr); // "object"
console.log(arr instanceof Array);  // True
console.log(arr instanceof Object); // True
```

## Practice & Review 

### Coding Challenge

Create a class called `Professor` that is a subclass of `Person` with the following behavior:

```js
const reuben = new Professor('Reuben', 'Ogbonna', 36, 'Software Engineering', 'Marcy Lab School');

console.log(reuben.fullName()); // Reuben Ogbonna
console.log(reuben.introduce()); // Hi, I'm Reuben and I'm 36 years old. I each Software Engineering at Marcy Lab School.
console.log(reuben.teach('classes')); // Hello class, today we will be learning about classes.
```

Then, create class called `MarcyStudent` that is a subclass of `Student`.
* All instances of `MarcyStudent` should have `subject` be set to `"Software Engineering"` and `school` be set to `"Marcy Lab School"`. 
* It should have a static `validCourses` array 
* `enrollInCourse` should be overridden to first check if the provided course is in the `validCourses` array before adding the course to the `courses` array.

### Study Questions

Then, with a partner, discuss these questions:

**<details><summary>Question 1: What does `extends` do?</summary>**

`extends` makes the `WebDeveloper` inherit methods from `Programmer`. It sets `Programmer.prototype` as the prototype for `WebDeveloper`

</details>

**<details><summary>Question 2: What does `super` do?</summary>**

`super()` invokes the `Programmer` constructor function using its own value of `this`. Any properties that the `Programmer` constructor sets on `this` will be set on `WebDeveloper`.

</details>

**<details><summary>Question 3: What do we know about the relationship between a `Programmer` and a `Person`?</summary>**

* `WebDeveloper` is said to be a **subclass** of `Programmer`. 
* `Programmer` is said to be a **superclass** of `WebDeveloper`.
* `WebDeveloper` will inherit properties and methods from `Programmer` and `Person`.
* Instances of `WebDeveloper` are also instances of `Programmer` and of `Person`, but not all instances of `Person` or `Programmer` are instances of `WebDeveloper`.

</details>

**<details><summary>Question 4: How does the `code` method work?</summary>**

`code` invokes the `doActivity` method inherited from `Person.prototype`

</details>
