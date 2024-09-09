# 5-0-3-oop-review

OOP can be defined by its 4 pillars:
* **Encapsulation** - every object should control its own state. information hiding.
* **Abstraction** - hiding complexity through functions and prototypes
* **Inheritance** - sharing behavior between objects
* **Polymorphism** - similar objects can be used interchangeably

## Closure

A **closure** is created when an inner function uses a variable from an outer function's scope. Even after the outer function returns, the inner function maintains a reference in memory to the outer function's variable.

```js
const getIdMaker = (id = 0) => {
  return () => ++id; // add 1 then return
};

const getId = getIdMaker();
console.log(getId()); // 1
console.log(getId()); // 2
console.log(getId()); // 3
```

In this example, the parameter `id` is referenced by the arrow function returned by `getIdMaker`. A closure is formed around `id`.

## Encapsulation 

In OOP, we store data in objects and give those objects methods to manipulate their own data. This is called **encapsulation**.

```js
// Object Oriented Programming encapsulates data with functionality
const friendsManager = {
  friends: [],
  addFriend(newFriend) {
    this.friends.push(newFriend);
    // `this` refers to the "owner" of the method. 
  }
}
friendsManager.addFriend('gonzalo');
friendsManager.friends.push(42); // oops!
```

Encapsulation also values **information hiding**. In the example above, the `friends` property can be directly accessed in ways that we may not want.

## Factory Functions & Closure

A simple way of implementing encapsulation and information hiding is through **factory functions** and **closure**.

With a factory function, we can make many **instances** of objects that have the same properties

```js
const makeVehicle = (name, seats) => {
  const passengers = []; // The methods below form a closure around this variable

  return {
    name: name,
    seats,            // shorthand notation
    sound: 'vroom',   // a property with a default value
    addPassenger(passenger) {
      if (passengers.length >= this.seats) {
        return console.log('not enough room');
      };
      passengers.push(passenger);
    },
    getPassengers(){
      return [...passengers] // return a copy to keep passengers private
    },
    makeSound() {
      console.log(`${this.name} goes ${this.sound}`);
    }
  }
}

const myCar = makeVehicle('benmobile', 5);
myCar.addPassenger('ben');
myCar.addPassenger('gonzalo');
const myPassengers = myCar.getPassengers(); // ['ben', 'gonzalo']

const batmansCar = makeVehicle('batmobile', 1);
batmansCar.addPassenger('batman');
batmansCar.addPassenger('catwoman'); // not enough room
batmansCar.sound = 'ZOOOM'; // instance properties can be accessed/modified
batmansCar.makeSound(); // batmobile goes ZOOOM
```

This has some issues...

**<details><summary style="color: purple">Q: What issues do factory functions have?</summary>**

* No inheritance
* No constructors
* Memory consumption (methods are not shared)
* No private instance properties

</details><br>

## Classes

To achieve **inheritance**, where objects share a set of methods in memory, we define a **class**.

A **class** defines a type of object. 
* It has a **constructor function** for creating an **instance** of that type of object
* All instances of that class have access to the class' methods.
* Properties and methods can be any combination of:
  * public or private
  * `static` or instance

```js
class User {
  #password;            // private instance property without starting value
  static allUsers = []  // public class property with a starting value

  constructor(name, email, password) {
    this.name = name;   // set the public instance property
    this.email = email;

    this.#password = password; // set the private instance property
    User.allUsers.push(this);
  }

  static getAllUsers() { // public class method
    return [...User.allUsers]
  }

  validatePassword(passwordToCheck) { // public instance method
    return passwordToCheck === this.#password;
  }

  setPassword(newPassword) {
    this.#password = newPassword;
  }
}

const ben = new User('ben', 'ben@mail.com', 123);
console.log(ben); // { name: 'ben', email: 'ben@mail.com' }

const carmen = new User('carmen', 'carmen@mail.com', 456);
const zo = new User('zo', 'zo@mail.com', 789);

console.log(User.getAllUsers()); // [ User { }, User {}, User {} ]

ben.password = 'blah';    // this won't affect the private #password property
console.log(ben.validatePassword('blah')); // false
ben.setPassword = 'blah'; // this will
console.log(ben.validatePassword('blah')); // true
```

> **Note:** The Chrome DevTools console will display private instance properties when you print them. This does NOT mean that those properties are now public. It is similar to how logged promises will show the fulfilled value.

## Challenge: Make a Library and Book Class

```js
const cityLibrary = new Library('City Library', '123 Main St');

// Adding books to the library
cityLibrary.addBook('The Great Gatsby', 'F. Scott Fitzgerald', 'Classic');
cityLibrary.addBook('To Kill a Mockingbird', 'Harper Lee', 'Fiction');
cityLibrary.addBook('1984', 'George Orwell', 'Dystopian');

// Listing all books
cityLibrary.listBooks();

// Removing a book by ID
cityLibrary.removeBook(2);

// Listing books after removal
cityLibrary.listBooks();

```
Notes:
* `Book` should be a class with a private `#id` instance property and public `title`, `author`, and `genre` instance properties.
* `Book` instances should have a `getId` method that returns the private `#id` instance property.
* `Library` should be a class with a private `#books` instance property. It should be an empty array to start.
* `Library` instances should have an `addBook`, `listBooks`, and `removeBook` method.
* Bonus: The `Library` class should have a `static #allLibraries` private class property. It should be an empty array to start.
* Bonus: The `Library` class should have a `static list()` method that returns all `Library` instances.