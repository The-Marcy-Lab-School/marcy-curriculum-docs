# Object-Oriented Programming Cheat Sheet

Use this as a reference while working.

**Table of Contents:**
- [Encapsulation and `this`](#encapsulation-and-this)
  - [Functional vs. OOP Approach](#functional-vs-oop-approach)
  - [Factory Functions](#factory-functions)
  - [The `this` Keyword](#the-this-keyword)
- [Closures and Data Hiding](#closures-and-data-hiding)
  - [Closures](#closures)
  - [Data Hiding with Closures](#data-hiding-with-closures)
- [Classes](#classes)
  - [Class Basics](#class-basics)
  - [Memory Efficiency](#memory-efficiency)
- [Private Properties and Static Methods](#private-properties-and-static-methods)
  - [Private Fields](#private-fields)
  - [Static Properties and Methods](#static-properties-and-methods)
- [Inheritance](#inheritance)
- [Polymorphism](#polymorphism)
- [System Design and UML Diagrams](#system-design-and-uml-diagrams)
- [System Design Patterns](#system-design-patterns)
  - [The Coordinator Pattern](#the-coordinator-pattern)
  - [Status-Driven Behavior](#status-driven-behavior)
  - [The Intermediary Object Pattern](#the-intermediary-object-pattern)
  - [Common Design Decisions](#common-design-decisions)

The four pillars of OOP:
1. **Encapsulation** — bundling data with the methods that operate on it
2. **Abstraction** — creating interfaces that hide complexity
3. **Inheritance** — sharing behavior between classes via `extends`
4. **Polymorphism** — different objects responding to the same method in different ways

## Encapsulation and `this`

### Functional vs. OOP Approach

In a **functional** approach, data and functions are separate:

```js
const addFriend = (friends, newFriend) => {
  friends.push(newFriend);
};
const printFriends = (username, friends) => {
  console.log(`${username}'s friends: ${friends.join(', ')}`);
};
```

With **encapsulation**, data and the methods that operate on it are bundled together:

```js
const friendsManager = {
  username: 'ben',
  friends: ['ada', 'bart'],
  addFriend(newFriend) {
    this.friends.push(newFriend);
  },
  printFriends() {
    console.log(`${this.username}'s friends: ${this.friends.join(', ')}`);
  }
};
```

### Factory Functions

A **factory function** creates and returns objects with a shared interface:

```js
const createFriendsManager = (username) => {
  return {
    username,
    friends: [],
    addFriend(newFriend) {
      this.friends.push(newFriend);
    },
    printFriends() {
      console.log(`${this.username}'s friends: ${this.friends.join(', ')}`);
    }
  };
};

const ben = createFriendsManager('ben');
const ada = createFriendsManager('ada');
```

### The `this` Keyword

`this` refers to the object that is calling the method:

```js
const user = {
  name: 'ben',
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
};
user.greet(); // "Hi, I'm ben" — `this` is `user`
```

**Arrow functions do NOT bind their own `this`** — they inherit `this` from their parent scope. Use function shorthand (`method() {}`) for object methods, not arrow functions.

## Closures and Data Hiding

### Closures

A **closure** is created when an inner function maintains a reference to variables from an outer function's scope, even after the outer function has returned:

```js
const makeCounter = () => {
  let count = 0;              // private variable
  return {
    increment() { count++; },
    getCount() { return count; }
  };
};

const counter = makeCounter();
counter.increment();
counter.increment();
counter.getCount(); // 2
// count is NOT accessible directly
```

### Data Hiding with Closures

**Data hiding** limits access to data to prevent invalid modifications. Use closures to make data private and expose controlled access through methods:

```js
const makeFriendsManager = (username) => {
  const friends = []; // private — not on the returned object

  return {
    username,
    addFriend(newFriend) {
      if (typeof newFriend !== 'string') return;  // validation
      friends.push(newFriend);
    },
    getFriends() {
      return [...friends]; // return a copy, not the original!
    }
  };
};
```

**Always return copies** of arrays and objects to prevent external mutation.

## Classes

### Class Basics

A **class** is a blueprint for creating objects with shared interfaces. Use the `new` keyword to create instances.

```js
class Person {
  friends = []; // public field (each instance gets its own)

  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  addFriend(newFriend) {
    this.friends.push(newFriend);
  }

  greet() {
    console.log(`Hi, I'm ${this.name}.`);
  }
}

const ben = new Person('ben', 30);
ben.greet();                    // "Hi, I'm ben."
console.log(ben instanceof Person); // true
```

- `constructor()` runs when `new` is called
- Properties set with `this.x = y` in the constructor are **own properties** (unique to each instance)
- Methods defined in the class body are shared via the **prototype**

### Memory Efficiency

Classes are more memory-efficient than factory functions because methods are shared on the prototype, not duplicated per instance:

```js
const ben = new Person('ben', 30);
const ada = new Person('ada', 25);

// Factory functions: methods are recreated per instance
// ben.addFriend === ada.addFriend → false

// Classes: methods are shared via prototype
// ben.addFriend === ada.addFriend → true
```

## Private Properties and Static Methods

### Private Fields

Use `#` to make properties and methods private (accessible only inside the class):

```js
class Person {
  #friends = [];  // private field

  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  addFriend(newFriend) {
    if (typeof newFriend !== 'string') return; // validation
    this.#friends.push(newFriend);
  }

  getFriends() {
    return [...this.#friends]; // return a copy!
  }
}

const ben = new Person('ben', 30);
ben.addFriend('ada');
ben.getFriends();  // ['ada']
ben.#friends;      // SyntaxError! Cannot access private field
```

**When to use private:** passwords, sensitive data, data requiring validation, internal helper methods.

### Static Properties and Methods

`static` properties and methods belong to the **class itself**, not to instances:

```js
class Person {
  static #allPeople = [];

  constructor(name, age) {
    this.name = name;
    this.age = age;
    Person.#allPeople.push(this);
  }

  static getPeopleCount() {
    return Person.#allPeople.length;
  }

  static findByName(name) {
    return Person.#allPeople.find((p) => p.name === name);
  }
}

new Person('ben', 30);
new Person('ada', 25);

Person.getPeopleCount();      // 2
Person.findByName('ada');     // Person { name: 'ada', age: 25 }
```

**When to use static:** constants shared across instances, tracking all instances, factory/utility methods.

## Inheritance

**Inheritance** lets a subclass extend a superclass, inheriting its methods and properties. Use `extends` and `super`.

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
    return `Hi, I'm ${this.firstName}.`;
  }
}

class Student extends Person {
  courses = [];

  constructor(first, last, age, subject, school) {
    super(first, last, age);  // call the parent constructor
    this.subject = subject;
    this.school = school;
  }

  introduce() {
    return `${super.introduce()} I study ${this.subject}.`;
    // super.introduce() calls the parent's introduce()
  }

  enrollInCourse(courseName) {
    this.courses.push(courseName);
  }
}
```

**Prototype chain:** `student` → `Student.prototype` → `Person.prototype` → `Object.prototype` → `null`

- `super()` in the constructor calls the parent's `constructor()`
- `super.method()` calls the parent's version of an overridden method

## Polymorphism

**Polymorphism** means different objects can be treated the same way when they share the same method names, even if the implementations differ:

```js
class Person {
  introduce() { return `Hi, I'm ${this.firstName}.`; }
}

class Student extends Person {
  introduce() { return `${super.introduce()} I study ${this.subject}.`; }
}

class MarcyStudent extends Student {
  introduce() { return `${super.introduce()} I go to Marcy Lab!`; }
}

// All types can be used interchangeably:
const people = [
  new MarcyStudent('Ada', 'Lovelace', 24, 'CS', 'Marcy'),
  new Student('Alan', 'Turing', 27, 'Math', 'Cambridge'),
  new Person('Reuben', 'Ogbonna', 36)
];

people.forEach((person) => {
  console.log(person.introduce()); // each calls its own version
});
```

**Duck typing** — objects don't need to share a parent class, they just need to have the same method name:

```js
class Dog {
  speak() { console.log('woof'); }
}

class Cat {
  speak() { console.log('meow'); }
}

[new Dog(), new Cat()].forEach((animal) => animal.speak());
```

## System Design and UML Diagrams

**System design** is the process of identifying the entities (classes), their responsibilities, and their relationships.

**Key questions to ask:**
1. What are the **entities** (classes)?
2. What are their **responsibilities** (properties and methods)?
3. What are the **relationships** between entities?

**UML Class Diagram Notation:**

```
┌─────────────────────┐
│       Person        │
├─────────────────────┤
│ +name: String       │   + public
│ -friends: Array     │   - private
├─────────────────────┤
│ +greet()            │
│ +addFriend(name)    │
│ -validateFriend()   │
└─────────────────────┘
```

**Relationship multiplicity:**
- `1` — exactly one
- `0..*` — zero or more
- `1..*` — one or more

## System Design Patterns

### The Coordinator Pattern

One class manages interactions between multiple entities:

```js
class Shelter {
  #pets = [];
  #applications = [];

  addPet(pet) { this.#pets.push(pet); }

  submitApplication(application) {
    this.#applications.push(application);
  }

  approveApplication(applicationId) {
    const app = this.#applications.find((a) => a.id === applicationId);
    app.approve();
  }
}
```

Use when one entity coordinates others, stores collections, validates interactions, and enforces business rules.

### Status-Driven Behavior

Objects maintain internal state that controls what actions they can perform:

```js
class Application {
  #status = 'pending';

  approve() {
    if (this.#status !== 'pending') return;
    this.#status = 'approved';
  }

  reject() {
    if (this.#status !== 'pending') return;
    this.#status = 'rejected';
  }

  getStatus() { return this.#status; }
}
```

Use when objects go through stages (pending → approved/rejected) and certain actions are only valid in certain states.

### The Intermediary Object Pattern

A new class represents the **relationship** between two entities, enabling many-to-many relationships:

```js
class Enrollment {
  constructor(student, course) {
    this.student = student;
    this.course = course;
    this.grade = null;
    this.enrollmentDate = new Date();
  }

  setGrade(grade) { this.grade = grade; }
}
```

Use when two entities have a many-to-many relationship and the relationship itself has data (grade, date, status).

### Common Design Decisions

| Decision                    | Guideline                                                                |
| --------------------------- | ------------------------------------------------------------------------ |
| **Private vs. Public**      | Default to private for data with business logic; public for simple data  |
| **Uni- vs. Bi-directional** | One-way when only one direction is queried; two-way when both are needed |
| **Creator Responsibility**  | The entity that initiates an action should create the object             |
