# 1. Promises

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-3-promises)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Why Promises?](#why-promises)
- [Synchronous vs. Asynchronous Functions and `setTimeout`](#synchronous-vs-asynchronous-functions-and-settimeout)
  - [Executing Asynchronous Code With Callbacks](#executing-asynchronous-code-with-callbacks)
  - [Sequential Asynchronous Callbacks Leads to Callback Hell](#sequential-asynchronous-callbacks-leads-to-callback-hell)
- [Promises!](#promises)
  - [Sequential Asynchronous Functions With Promises](#sequential-asynchronous-functions-with-promises)
  - [Promise.all()](#promiseall)
- [Making Promises](#making-promises)
  - [A Function that Makes and Returns a Promise](#a-function-that-makes-and-returns-a-promise)
- [Summary](#summary)
  - [Coming up...](#coming-up)

## Key Concepts

* **Synchronous code** — runs in order, one statement at a time; each must finish before the next starts.
* **Asynchronous code** — starts a process (e.g., a timer or file read) and does not block; later code can run while waiting. Often uses callbacks or Promises to handle completion.
* **Callback hell** — nested callbacks used to run async operations one after another; hard to read and maintain.
* **Promise** — an object representing a pending async operation. It can be **pending**, **resolved** (fulfilled), or **rejected**.

**Key Syntax**
* **`new Promise((resolve, reject) => { ... })`** — creates a new Promise. 
  * Call `resolve(value)` on success
  * Call `reject(value)` on failure.
* **`promise.then(onFulfilled).catch(onRejected)`** — handle a Promise’s result or error. 
  * **`.then()`** — schedules a callback to run when a Promise resolves; Returning a value (or Promise) from `.then()` passes it to the next `.then()`.
  * **`.catch()`** — schedules a callback to run when a Promise rejects; used to handle errors in a chain.
* **`Promise.all(arrayOfPromises)`** — returns a Promise that resolves when all given Promises resolve, with an array of their values; rejects if any of them reject.

## Why Promises?

In the last two lessons we focused on the **DOM**: 
* selecting elements with `querySelector`
* responding to clicks and other events with `addEventListener`
* creating and appending elements to build dynamic lists and interfaces. 

With these skills, you can build quite a lot! 

However, the final piece to building interactive frontend web applications is **fetching data from web APIs**. When our app requests data from a weather API, a list of products, or a user profile, the response comes back later—maybe in 100ms, maybe in a few seconds. 

The timing depends on internet speed and how many requests the API is receiving. We can't freeze the page until it arrives. We need a way to say "start this request" and then "when the response is here, run this code." That's exactly what **Promises** are for: they represent work that isn't done yet and let us schedule what to do when it succeeds or fails. So, before we can fetch data from an API, we need to learn how to use Promises. 

We'll also need to move on from using the `file://` protocol and start using development servers to build and test our applications. That's what we'll cover next.

Once we've learned these two skills, we can start fetching from APIs!

## Synchronous vs. Asynchronous Functions and `setTimeout`

Most of the functions we have used are **synchronous functions**.

The order in which synchronous functions execute is _sequential_, meaning we must complete the execution of one function before starting to execute the next.

```js
const start = () => console.log('start');
const longTask = () => {
  // runs 10 billion times! 
  for (let i = 0; i < 10000000000; i++) { }
  console.log(`done looping 10 billion times!`);
}
const end = () => console.log('end');

start();
longTask();
end();
```

Depending on how fast your computer is, this program could take a while to print out `'end'` for the very reason that the `end()` function can't start until `longTask` is complete.

**Asynchronous functions** do not execute sequentially. Instead, they start a process (typically one that takes time) but do NOT prevent subsequent code from running. This is why it is referred to as **"non-blocking"**.

Often, asynchronous functions accept a callback to execute when the long process is completed. We can see this clearly with `setTimeout` which starts a timer and executes a callback when the timer is up:

```jsx
console.log("starting the first task");
setTimeout(() => { // this task takes 3 seconds
  console.log("first task completed")
}, 3000)

console.log("starting the second task");
setTimeout(() => { // this task takes 1 seconds
  console.log("second task completed")
}, 1000)

console.log("starting the third task");
setTimeout(() => { // this task takes 2 seconds
  console.log("third task completed")
}, 2000)
```

**<details><summary>Q: In what order will the console log statements be executed? What happens if we set the timeouts to 0 milliseconds?</summary>**

```
starting the first task
starting the second task
starting the third task
second task completed
third task completed
first task completed
```

As a result, the `"starting the X task"` statements are printed first and in the order in which they were written. Then, as each timer finishes, the console.log statements are executed asynchronously.

The scheduling of each callback happens sequentially but the actual invocation of the callbacks happens asynchronously, that is, when each timer reaches 0.

Even if we set all of the timers to 0, synchronous functions will always complete first, then asynchronous functions. This is due to [how the event loop works](https://www.youtube.com/watch?v=8aGhZQkoFbQ\&ab_channel=JSConf).

</details>

The main benefit of asynchronous functions is that they can be started and then completed at a later time without causing the rest of the program to wait.

Other examples of asynchronous operations include:

* processing an image
* fetching data over the internet
* counting down a timer
* waiting for a user to click on a button.

**<details><summary>Q: setTimeout uses a callback to handle the completion of the timer. Which example from that list have we already used that also uses callbacks?</summary>**

Event handlers!

```js
console.log('start');

document
  .querySelector('#clickButton')
  .addEventListener('click', () => {
    console.log('the button has been clicked!');
  });

console.log('end');
```

No matter how quickly the user clicks the button, the order will always be:

```
start
end
the button has been clicked
```

In the code snippet above, the callback provided to `addEventListener` is an example of an **asynchronous function**.

</details>

### Executing Asynchronous Code With Callbacks

Another great example of an asynchronous function using callbacks is the asynchronous `fs.readFile` function. The `fs` module is only available in Node (not in the browser) and it has functions that allow the programmer to access files in their filesystem.

Take a look at the `data/booksHuge.csv` file, it contains data about books. Each line in that file represents a different book and we will try write a program to count how many lines (books) there are.

{% hint style="info" %}
A **comma-separated value (CSV) file** is a simple way to represent large datasets in rows and columns. Each row in a CSV file represents a single entry in the table (except the first line which defines the names of each column). Each entry contains data separated by some uniform character like a comma `,` or semicolon `;`.

For example, this CSV file has three columns and 3 entries.

```csv
"name","age","email"
"kobe","25","kobe@mail.com"
"lebron","39","lebron@mail.com"
"sue","42","sue@mail.com"
```
{% endhint %}

The `fs.readFile` function lets us import the contents of a file by providing:

1. A filepath
2. A [character encoding](https://en.wikipedia.org/wiki/Character_encoding) (in the example we use `'utf-8'` which basically means "read the file as normal text" and is the encoding used 99% of the time).
3. A callback to execute when `fs.readFile` is done reading the file.

Since reading a file could potentially take a long time, we use the callback to "schedule" a handler function to run when the file has been completely read:

```js
// the fs module has functions for interacting with the file system
const fs = require('node:fs');

console.log('Reading the booksHuge.csv file');

fs.readFile('../data/booksHuge.csv', 'utf-8', (err, data) => {
  // this code is scheduled to run when the file is done being processed
  if (err) {
    console.error(err);
  } else {
    const lines = data.split('\n').length;
    console.log(`Done reading the booksHuge.csv file. There were ${lines - 1} books.`);
  }
});
```

In this example, we are reading a file called `booksHuge.csv`.

* When the file has been read completely, the callback is executed.
* We first check to see if an error occurred, printing it out if it has.
* If no error exists, the `data` contains the contents of the file as a single string. We check to see how many lines are in the file and print it out!

### Sequential Asynchronous Callbacks Leads to Callback Hell

Okay, so asynchronous callbacks are great for executing time-consuming tasks without blocking our synchronous code.

But what if we DO want our asynchronous code to be blocking. That is, to wait for one function to finish before starting the next, just like synchronous code?

With callbacks, we are forced to do something like this, appropriately called **callback hell**:

```js
console.log("starting the first task!");
setTimeout(() => {
  console.log("first task completed");
  // now start the next task
  console.log("starting the second task!");
  setTimeout(() => {
    console.log("second task completed");
    // now start the final task
    console.log("starting the third task!");
    setTimeout(() => {
      console.log("third task completed");
    }, 2000);
  }, 1000);
}, 3000);
```

Yikes, that is hard to read! Here is what is happening:

* Start the first task
  * After 3 seconds, the first task will complete and we start the second task
    * After 1 seconds, the second task will complete and we start the third task
      * After 2 seconds, the third task will complete

## Promises!

Promises provide an alternate approach to callbacks, helping us avoid callback hell.

Consider the example below which uses the Promises version of the `fs.readFile` function.

The Promise version of `readFile` returns a Promise object instead of taking in a callback (toggle between the two versions to compare them)

* We provide a callback to `.then` to be invoked when the promise "resolves" (suceeds and the `data` is provided).
* We provide a callback to `.catch` to be invoked if the promise "rejects" (fails and the `err` is provided).
* In both cases, the callbacks are executed asynchronously.

{% tabs %}
{% tab title="Promises" %}
```js
// Now, we'll use the promises version of fs
const fs = require('node:fs/promises');

console.log("Reading the-raven.txt");

// A Promise object is returned
const promise = fs.readFile('../data/the-raven.txt', 'utf8');

// The .then method takes a callback to execute when the promise resolves (succeeds)
// The .catch method takes a callback to execute when the promise rejects (fails)
promise
  .then((data) => {
    const ravenCount = data.match(/raven/gi).length;
    console.log(`Done reading the-raven.txt.`)
    console.log(`There were ${ravenCount} mentions of "Raven".`);
  })
  .catch((err) => {
    console.log('Something went wrong!');
    console.error(err);
  });

// The object on its own looks like this: Promise { <pending> }
console.log(promise);
```
{% endtab %}

{% tab title="Callbacks" %}
```js
// Here, we use the default callback version of fs
const fs = require('node:fs');

console.log("Reading the-raven.txt");

// We have to provide a callback as the third argument
fs.readFile('../data/the-raven.txt', 'utf-8', (err, data) => {
  if (err) {
    console.log('Something went wrong!');
    console.error(err);
  } else {
    const ravenCount = data.match(/raven/gi).length;
    console.log(`Done reading the-raven.txt.`)
    console.log(`There were ${ravenCount} mentions of "Raven".`);
  }
});
```
{% endtab %}
{% endtabs %}

A **Promise** is a type of object (a "class" of object) that represents a pending asynchronous operation.

* A Promise object can exist in one of three states:
  * **pending** - the function is still in process.
  * **resolved** - the function was a success! We got the value we wanted. Also referred to as "fulfilled”
  * **rejected** - the function failed. We got an error back.
* The `Promise.then()` method schedules a callback to be executed when the promise resolves.
* The `Promise.catch()` method schedules a callback to be executed when the promise rejects.
* `Promise.then()` returns a Promise allowing it to be chained.

### Sequential Asynchronous Functions With Promises

At this point, the code isn't dramatically different. However, Promises start to shine when we want to perform multiple asynchronous functions sequentially.

Compare and contrast these two approaches for reading the `booksHuge.csv` file first followed by the `books.csv` file second:

{% tabs %}
{% tab title="Callbacks" %}
As you can see in the callbacks version of the code, we very quickly get to four levels of indentation, causing the readability of our code suffer.

```js
const fs = require('node:fs');

console.log("Reading the booksHuge.csv file");

fs.readFile('../data/booksHuge.csv', 'utf-8', (err, data) => {
  if (err) {
    console.error(err);
  } else {
    const lines = data.split('\n').length;
    console.log(`Done reading the booksHuge.csv file. There were ${lines - 1} books.`);

    console.log("Reading the books.csv file");
    fs.readFile('../data/books.csv', 'utf-8', (err, data) => {
      if (err) {
        console.error(err);
      } else {
        const lines = data.split('\n').length;
        console.log(`Done reading the books.csv file. There were ${lines - 1} books.`);
      }
    });
  }
});
```
{% endtab %}

{% tab title="Promises" %}
With promises, since `.then` calls can be chained together, we limit the indentation to two levels. This is only possible because the first `.then` returns a promise that can be handled by the second `.then`. Additionally, we only need to write one error handler with `.catch` which can handle errors that occur at any point in the chain of promises.

```js
const fs = require('node:fs/promises');

console.log("Reading the booksHuge.csv file");

// Often, we will just call .then directly on the function call
fs.readFile('../data/booksHuge.csv', 'utf-8')
  .then((data) => {
    const lines = data.split('\n').length;
    console.log(`Done reading the booksHuge.csv file. There were ${lines} lines.`);
    
    // start the next async process
    console.log("Reading the books.csv file next");

    // the promise returned here is handled by the next .then()
    return fs.readFile('../data/books.csv', 'utf-8');
  })
  .then((data) => {
    // handle the data from books.csv
    const lines = data.split('\n').length;
    console.log(`Done reading the books.csv file. There were ${lines} lines.`);
  })
  .catch((err) => {
    // We only need one error handler for both asynchronous function calls
    console.error(err);
  });
```
{% endtab %}
{% endtabs %}

### Promise.all()

In the previous example, we wait for the first `fs.readFile()` to finish before starting the second `fs.readFile()` call. In between, we were able to run some `console.log` statements.

However, if we want to wait until BOTH operations are completed before handling them, we can use the `Promise.all` function:

```js
const fs = require('node:fs/promises');

// First, start both processes and save the promises
console.log("Reading both files!");
const booksHugePromise = fs.readFile('../data/booksHuge.csv', 'utf-8')
const booksPromise = fs.readFile('../data/books.csv', 'utf-8')

// Then, put the promises into an array
const promises = [booksHugePromise, booksPromise]

// Promise.all returns a promise that settles once all of the provided promises have settled
Promise.all(promises)
  .then((values) => {
    // values will contain the resolved values of the provided promises
    const booksHugeLines = values[0].split('\n').length;
    const booksLines = values[1].split('\n').length;
    console.log(`There were ${booksHugeLines} lines in booksHuge.csv!`);
    console.log(`There were ${booksLines} lines in books.csv!`);
  })
  .catch((err) => {
    // the err value will be from the first promise that fails
    console.error(err);
  });
```

`Promise.all` takes in an array of promise objects and returns a promise that settles once all of the provided promises have settled.

* The resulting values of each promise are added to an array.
* When all promises provided to `Promise.all` resolve, the callback provided to `.then` will be invoked with the array of resolved values.
* If any of the promises reject (fail), the `.catch()` callback will be invoked with the error of the first failing promise.

## Making Promises

To better understand functions that return promises, we can make a Promise ourselves!

Remember, a Promise represents an asynchronous operation that will take some time to complete. When it completes (when it "resolves") or when it fails (when it "rejects"), we can decide what to do next using callbacks provided to `.then` and `.catch`

To create a Promise, use the `new Promise()` constructor function:

```js
const myPromise = new Promise((resolve, reject) => {
  // asynchronous functions here
});
```

* The `new Promise()` constructor function takes in a callback that contains the asynchronous functions to be performed.
* The provided callback will be given two functions as parameters: `resolve` and `reject`.
  * Invoke `resolve(successValue)` to indicate that the asynchronous function succeeds.
  * Invoke `reject(failureValue)` to indicate failure.

In the example below, we create a new Promise that starts a 5 second timer (that's our asynchronous operation). When the timer is done, a random number from 1-6 is generated and the Promise either resolves or rejects depending on the result. Below the creation of the Promise, we schedule callbacks to handle the value produced in each scenario:

```js
// The callback determines when and how the promise resolves (or rejects)
const rollPromise = new Promise((resolve, reject) => {
  // wait 5 seconds before settling the promise
  setTimeout(() => {
    const roll = Math.ceil(Math.random() * 6);

    // In this case, rolls 3, 4, 5, or 6 will resolve the promise
    // Rolls 1 or 2 will reject the promise
    if (roll > 2) {
      resolve(`Success! ${roll} was rolled`);
    } else {
      reject(`Failure. ${roll} was rolled`)
    }
  }, 5000);
});

// schedule callbacks to execute when the promise resolves or rejects
rollPromise
  .then((successMessage) => {
    console.log(successMessage);
    // Prints "Success! X was rolled" (assuming X is a number 3-6)
  })
  .catch((failureMessage) => {
    console.error(failureMessage);
    // Prints "Failure. X was rolled" (assuming X is 1 or 2)
  })
```

Let's break down this example:

* We declare `rollPromise` to hold the new `Promise` object being created. We use this variable to define resolve/reject handlers with `.then` and `.catch`.
* Within the callback provided to `new Promise()`, the asynchronous function is a timer counting down from 5.
* When the timer is done, we will "roll a die".
  * If the die roll is greater than 2, we invoke `resolve` with a success message.
  * Otherwise, we invoke `reject` with a failure message.
* The value we invoke `resolve` with is passed to the `.then` handler which we've decided to print with `console.log`.
* The value we invoke `reject` with is passed to the `.then` handler which we've decided to print with `console.error`.

### A Function that Makes and Returns a Promise

Here is an asynchronous function that returns a promise that ALWAYS resolves

```js
const makeGuarantee = () => {
  return new Promise((resolve, reject) => {
    resolve('100% of the time, it works every time');
  });
}

makeGuarantee().then(data => {
  console.log("I dont even need a catch since it will always resolve");
  console.log(data);
});
```

{% hint style="info" %}
Most often, you don’t create Promises yourself. You’ll just "consume" them from functions like `fs.readFile` or `fetch`.
{% endhint %}

**<details><summary>Q: Is it possible for an asynchronous function to complete before a synchronous function?</summary>**

Synchronous code will **always** be executed before asynchronous code.

</details>

## Summary

Using a Promise involves two steps:

1. Start the asynchronous function and get a Promise back (order your pizza, get your ticket)
2. Define how to handle the resolved/rejected Promise using `.then()` and `.catch()` (when ready, I will hand in my ticket and get my pizza)

![Diagram showing the flow of Promises](../.gitbook/assets/Promises.svg)

> Imagine walking into a Pizza shop and you ask for a slice of cheese pizza. The pizza isn’t ready yet so you have to wait. The person at the register gives you a ticket to claim your slice when it is ready. Meanwhile, you are free to run other errands and can return later to pick up your pizza. You get notified that the pizza is done so you return to the shop, hand in your ticket, and take your pizza home.

**<details><summary>Q: In the pizza shop example, what is the promise object we are given? what is the resulting value?</summary>**

The ticket is the promise. The resulting value is the pizza (or them telling us that they ran out of pizza).

</details>

### Coming up...

Next time, we’ll write code like this:

```js
// Execute async code that returns a Promise
const fetchPromise = fetch('https://pokeapi.co/api/v2/pokemon/pikachu');

// Define what to do with the resolved Promise
fetchPromise
  .then((response) => response.json())
  .then((jsonData) => console.log(jsonData))
  .catch((error) => console.error(error.message))
```
