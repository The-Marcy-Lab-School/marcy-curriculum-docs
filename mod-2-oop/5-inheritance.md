# 5. Inheritance

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-4-inheritance)!
{% endhint %}

**Table of Contents:**

* [Key Concepts](5-inheritance.md#key-concepts)
* [Inheritance](5-inheritance.md#inheritance-1)
  * [A Person and a Student](5-inheritance.md#a-person-and-a-student)
  * [`extends`](5-inheritance.md#extends)
  * [`super`](5-inheritance.md#super)
  * [Challenge: Professor Class](5-inheritance.md#challenge-professor-class)
* [Prototype Chain: Subclasses of Subclasses](5-inheritance.md#prototype-chain-subclasses-of-subclasses)
  * [Array.prototype](5-inheritance.md#arrayprototype)
  * [Object.prototype and the Prototype Chain](5-inheritance.md#objectprototype-and-the-prototype-chain)
  * [Quiz!](5-inheritance.md#quiz)
* [Practice & Review](5-inheritance.md#practice--review)
  * [Study Questions](5-inheritance.md#study-questions)

## Key Concepts

* **Inheritance** describes a relationship between two classes: a **subclass** that inherits methods from a **superclass**. Instances of the subclass can reuse and add to the methods defined in a superclass.
* The **`Subclass extends Superclass`** syntax defines an inheritance relationship between two classes
* **The `super` keyword references the superclass of a given subclass:**
  1. We can invoke `super()` in the subclass constructor to invoke the superclass's `constructor()`.
  2. We can invoke `super.method()` in an **overridden method** to invoke the superclass's version of the method on the subclass instance.
* The **prototype chain** describes the linked structure where each object inherits methods from a prototype, which may inherit methods from another prototype, forming a chain.
  * When you access a property or method, JavaScript walks up this chain until it finds what you're looking for (or reaches the end).

## Inheritance

### A Person and a Student

Consider this `Person` class.

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

I want to make another class called `Student` that can do everything a `Person` can. In addition, `Student` will have some additional properties and methods.

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

<details>

<summary><strong>Question: What similarities and differences do you see between these two classes? What coding style rule is this breaking?</strong></summary>

A `Student` has every property and method that a `Person` has but it also has the public field `courses` as well as the properties `subject` and `sschool`. The `introduce()` method is implemented slightly differently and the `enrollInCourse` method is new.

This code breaks the DRY principle (Don't Repeat Yourself). In the `Student` class we duplicate the code for the `fullname` method and most of the code in the `constructor` and `introduce` methods stays the same.

</details>

### `extends`

A `Student` is essentially a specific kind of `Person`. In computer science, we would say that **"a Student&#x20;**_**is a**_**&#x20;Person"** to describe this relationship.

In JavaScript, we formalize this relationship using the `Subclass extends Superclass` syntax:

```js
const Person = require('./Person');

// Student is the "Subclass", Person is the "Superclass"
class Student extends Person {

}

const ada = new Student('Ada', 'Lovelace', 30);
console.log(ada.fullName()); // Ada Lovelace
console.log(ada.introduce()); // Hi, I'm Ada and I'm 30 years old.
```

With just this `extends` keyword, **every instance of `Student` "inherits" the properties and methods from the `Person` class.**

Furthermore, instances of `Student` are _also_ instances of `Person`.

```js
console.log(ada instanceof Student);  // true
console.log(ada instanceof Person);   // true
```

### `super`

We don't want our `Student` class to just be a carbon-copy of the `Person` class. When we want to add new functionality to a subclass and still leverage the functionality of the superclass, we use the `super` keyword.

**The `super` keyword references the superclass of a given subclass. `super` allows us to extend the functionality of the superclass's methods:**

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

This example shows the two common usages of the `super` keyword:

1. `super()` — invokes the superclass constructor
   * In the `Student` constructor, `super()` invokes the `Person`'s constructor which assigns the properties `firstName`, `lastName`, and `age` to the new `Student` instance.
2. `super.method()` — invokes a method defined in the superclass. Often used to **override** the behavior of that method while still using some of the behavior from the superclass.
   * The `introduce()` method is overridden in the `Student` class but uses `super.introduce()` to invoke the `introduce` method defined in the `Person` class.

Notice that the `fullName` method is inherited from the `Person` class and is NOT overridden. It will behave the same way for student instances and person instances.

### Challenge: Professor Class

Create a class called `Professor` that is a subclass of `Person` with the following behavior:

```js
const reuben = new Professor('Reuben', 'Ogbonna', 36, 'Software Engineering', 'Marcy Lab School');

console.log(reuben.fullName()); // Reuben Ogbonna
console.log(reuben.introduce()); // Hi, I'm Reuben and I'm 36 years old. I teach Software Engineering at Marcy Lab School.
console.log(reuben.teach('object-oriented programming')); // Hello class, today we will be learning about object-oriented programming.
```

Then, create class called `MarcyStudent` that is a subclass of `Student`.

* All instances of `MarcyStudent` should have `subject` be set to `"Software Engineering"` and `school` be set to `"Marcy Lab School"`.
* It should have a static `validCourses` array: `['Leadership & Development', 'Technical Interview Prep', 'Technical Lecture']`
* `enrollInCourse` should be overridden to first check if the provided course is in the `validCourses` array before adding the course to the `courses` array.

<details>

<summary><strong>Solution</strong></summary>

**Professor**

```js
class Professor extends Person {
  constructor(first, last, age, subject, school) {
    super(first, last, age);
    this.subject = subject;
    this.school = school;
  }
  introduce() {
    return `${super.introduce()}. I teach ${this.subject} at ${this.school}.`;
  }
  teach(topic) {
    return `Hello class, today we will be learning about ${topic}.`;
  }
}
```

**MarcyStudent**

```js
class MarcyStudent extends Student {
  static validCourses = ['Leadership & Development', 'Technical Interview Prep', 'Technical Lecture'];
  constructor(first, last, age) {
    super(first, last, age, "Software Engineering", "Marcy Lab School");
  }
  enrollInCourse(course) {
    if (validCourses.includes(course)) {
      super.enrollInCourse();
    }
  }
}
```

</details>

## Prototype Chain: Subclasses of Subclasses

### Array.prototype

Have you considered how arrays are able to use methods like `push` and `forEach`?

If we look at the "own properties" of an array (the properties defined on the array), we don't see any of its methods:

```js
const arr = ['a', 'b', 'c'];
const arrayProperties = Object.getOwnPropertyNames(arr);
console.log(arrayProperties); // ['0', '1', '2', 'length']
```

As we can see, the properties of an array are just the indexes for each value and the `length` property. So, where are those methods defined?

We get a hint if we look at the [documentation for the array method `push()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push). Notice how it names the method as `Array.prototype.push`?

Arrays are able to use array methods because:

1.  Methods like `push` and `forEach` are defined as methods of the `Array` class and are therefore inherited by all `Array` instances. We can see those methods by looking at the properties of the `Array.prototype` object.

    ```js
    const arrayMethods = Object.getOwnPropertyNames(Array.prototype);
    console.log(arrayMethods);
    /* 
    [
      'length',        'constructor',    'at',
      'concat',        'copyWithin',     'fill',
      'find',          'findIndex',      'findLast',
      'findLastIndex', 'lastIndexOf',    'pop',
      'push',  <---    'reverse',        'shift',
      'unshift',       'slice',          'sort',
      'splice',        'includes',       'indexOf',
      'join',          'keys',           'entries',
      'values',        'forEach',  <---  'filter',
      'flat',          'flatMap',        'map',
      'every',         'some',           'reduce',
      'reduceRight',   'toLocaleString', 'toString',
      'toReversed',    'toSorted',       'toSpliced',
      'with'
    ] 
    */
    ```
2.  Whenever we create an **"array literal"** (using square brackets `[]`), we are actually creating a new instance of the `Array` class!

    ```js
    // these do the same thing
    let arr2 = ['dee', 'too']; // using "array literal" syntax
    let arr3 = new Array('dee', 'too');  // using the Array class constructor

    console.log(arr2 instanceof Array); // true
    console.log(arr3 instanceof Array); // true
    ```

### Object.prototype and the Prototype Chain

Now, in that list of `Array.prototype` methods, can you find a method called `hasOwnProperty`?

It's not there, and yet we can invoke that method on an `Array` instance:

```js
const letters = ['a', 'b', 'c'];

// .hasOwnProperty returns true if a given property name is an "own property".
console.log(letters.hasOwnProperty(1)); // true 
console.log(letters.hasOwnProperty('push')); // false
```

The `Object.prototype.hasOwnProperty` method is defined as a part of the `Object` class from which _all_ classes are extended, including the `Array` class and the classes we've made here like `Person` and `Student`.

```js
const ada = new Student('Ada', 'Lovelace', 30, 'Computer Science', 'Marcy Lab School');
const letters = ['a', 'b', 'c'];

console.log(ada instanceof Object);
console.log(letters instanceof Object);
```

Since arrays are instances of the `Array` class which extends the `Object` class, we end up with what we call a **prototype chain: a linked structure of objects inheriting methods from class prototypes, which can inherit methods from other class prototypes, and so on...**

`letters` → `Array.prototype` → `Object.prototype` → `null`

**The prototype chain is used by JavaScript to find method definitions when the method can't be found the object invoking the method.**

For example, when `letters.push()` is invoked, JavaScript looks at `letters` to see if it has the definition of `push`. When it doesn't find it, it looks at inside of `Array.prototype` where it finds the definition of `push`.

When `letters.hasOwnProperty()` is invoked, JavaScript looks at `letters`, then `Array.prototype`, and then finally at `Object.prototype` to find the definition.

If we were to invoke a non-existent method like `letters.foo()`, JavaScript would look at `letters`, then `Array.prototype`, then `Object.prototype` and then would run out of prototypes to look at. At this point, JavaScript would determine that `foo` is not a function and throw a `TypeError`.

### Quiz!

Consider the `ada` instance of the `Student` class below.

```js
const ada = new Student('Ada', 'Lovelace', 30, 'Computer Science', 'Marcy Lab School');
```

<details>

<summary><strong>Q: Using arrows, draw out the prototype chain for <code>ada</code>.</strong></summary>

`ada` → `Student.prototype` → `Person.prototype` → `Object.prototype` → `null`

</details>

Consider the code below:

```js
ada.fullName()
ada.toString()
ada.name
ada.introduce()
ada.blah()
```

<details>

<summary><strong>Q: For each line of code, describe how JavaScript walks up the prototype chain and where it finds the definition.</strong></summary>

`.fullName()` is not in `ada` so JavaScript looks at `Student.prototype`, then `Person.prototype` where it finds the definition. `.toString()` is not in `ada` so JavaScript looks at `Student.prototype`, then `Person.prototype`, then `Object.prototype` where it finds the definition. `.fullName()` is found in `ada` so JavaScript doesn't do anything. `.introduce()` is not in `ada` so JavaScript looks at `Student.prototype` where it finds the definition. It ignores the definition in `Person.prototype` because it has found a "closer" definition. `.blah()` is not in `ada` so JavaScript looks at `Student.prototype`, then `Person.prototype`, then `Object.prototype`. It doesn't find it there so JavaScript throws a `TypeError`.

</details>

If you run the Node debugger, you can see the prototype chain by looking at the `[[Prototype]]` field of any object:z

![](../.gitbook/assets/prototype-chain-debugger.png)

## Practice & Review

### Study Questions

Then, with a partner, discuss these questions:

<details>

<summary><strong>Question 1: What does <code>extends</code> do?</strong></summary>

`extends` makes the `WebDeveloper` inherit methods from `Programmer`. It sets `Programmer.prototype` as the prototype for `WebDeveloper`

</details>

<details>

<summary><strong>Question 2: What does <code>super</code> do?</strong></summary>

`super()` invokes the `Programmer` constructor function using its own value of `this`. Any properties that the `Programmer` constructor sets on `this` will be set on `WebDeveloper`.

</details>

<details>

<summary><strong>Question 3: What do we know about the relationship between a <code>Programmer</code> and a <code>Person</code>?</strong></summary>

* `WebDeveloper` is said to be a **subclass** of `Programmer`.
* `Programmer` is said to be a **superclass** of `WebDeveloper`.
* `WebDeveloper` will inherit properties and methods from `Programmer` and `Person`.
* Instances of `WebDeveloper` are also instances of `Programmer` and of `Person`, but not all instances of `Person` or `Programmer` are instances of `WebDeveloper`.

</details>

<details>

<summary><strong>Question 4: How does the <code>code</code> method work?</strong></summary>

`code` invokes the `doActivity` method inherited from `Person.prototype`

</details>
