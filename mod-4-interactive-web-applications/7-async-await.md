# Async & Await

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-0-3-async-await)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Intro: Promise Returning Is Tricky](#intro-promise-returning-is-tricky)
  - [The benefits of `async`/`await`](#the-benefits-of-asyncawait)
- [Fetching "Synchronously" with Async/Await](#fetching-synchronously-with-asyncawait)
  - [Handling Errors with Try/Catch](#handling-errors-with-trycatch)

## Key Concepts

* **`async` function** — a function that always returns a Promise. Lets you use `await` inside it.
* **`await`** — used inside an `async` function; pauses until the Promise resolves, then gives you the resolved value. Makes async code look like synchronous code.
* **`try` / `catch`** — run code in `try`; if any of it throws (including from `await`), execution jumps to `catch` so you can handle the error instead of crashing.

## Intro: Promise Returning Is Tricky

What's wrong with this code? Why does it print `undefined`?

```js
fetch('https://pokeapi.co/api/v2/pokemon/pikachu')
  .then((response) => {
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    const readingPromise = response.json();
  })
  .then((data) => {
    console.log(data); // prints undefined
  })
  .catch((error) => {
    console.log("Error caught!");
    console.log(error.message);
  })
```

<details><summary>Answer</summary>

We forgot to return `readingPromise` from the first `.then` callback, so the second `.then` callback never got a value!

</details>

### The benefits of `async`/`await`

Using the `async`/`await` syntax with `try` and `catch` has a number of benefits. The main ones being **readability** and **debuggability**.

* We can write async code in a synchronous-like manner
* We avoid having to write a bunch of callbacks
* We can avoid common mistakes made when using callbacks
* `try/catch` is a more general-purpose way of handling errors that can be used for more than just fetching.

## Fetching "Synchronously" with Async/Await

When fetching, it is important to observe that we have two sequential asynchronous processes involved: 
1. fetching
2. reading the response body's `ReadableStream`. 

And we've handled those processes using Promises like this:

* `fetch()` is an asynchronous function so it returns a promise.
* The first `.then` callback handles that promise's `response` value.
* In that callback we start reading in the response data with `response.json()`, which returns another promise.
* The second `.then` callback handles that promise's `responseData` value.

```js
// in fetch-helpers.js
export const getUsers = () => {
  return fetch('https://reqres.in/api/users')
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      // start the async process of reading the ReadableStream and return a promise
      return response.json();
    })
    .then((responseData) => {
      // do something with the response data
      console.log("Here is your data:", responseData);
      return responseData;
    })
    .catch((error) => {
      console.error(error.message);
    });
}

// in main.js
const main = () => {
  getUsers().then(renderUsers);
}
```

Effectively, we are saying, "wait for the `fetch()` promise to resolve, then wait for the `response.json()` promise to resolve, then begin working with the data". 

However, working with callbacks can be conceptually difficult to follow at times. So, an alternate syntax was created to achieve the same effect but without the need for callbacks. 

This approach utilizes the `async` and `await` keywords:

* The `await` keyword is placed before any *function call* that returns a Promise
  * It causes our code to pause and wait for the Promise to resolve. 
  * It then "unpacks" the Promise and returns the resolved value.
* The `async` keyword is placed before any *function* that uses `await` inside:
  * Any value returned by this function now will be wrapped in a Promise
  
```jsx
// A function marked with `async` returns a Promise
// You MUST put `async` if you want to use `await`
const getUsersAsyncAwait = async () => { 
  // When we await a Promise, we are given the resolved value (the Response object)
  // An awaited statement becomes "blocking"
  const response = await fetch('https://reqres.in/api/users');
  
  if (!response.ok) {
    throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
  }

  // Since response.json() also returns a Promise, we can await it too.
  const jsonData = await response.json();

  console.log("here is the data: ", data);

  // we can also just return await response.json()
  return jsonData;
};

// in main.js
const main = async () => {
  const users = await getUsersAsyncAwait();
  renderUsers(users);

  // Or, we can still use .then if we want to
  getUsersAsyncAwait().then(renderUsers);
}
```

### Handling Errors with Try/Catch

There are some functions (like `fetch()` and `response.json()`) that are capable of throwing an error.

We can manually throw our own errors too using `throw new Error('message')`. When an error is thrown, the program crashes immediately:

```js
console.log('start');

throw new Error('uh oh!');

console.log('end'); // this code won't even run
```

`try`/`catch` is the standard way to handle those errors and it will prevent the application from crashing. Instead, we can handle any errors that occur and continue on:

```js
console.log('start');

try {
  throw Error('uh oh!')
}
catch (error) {
  console.error(error.message)
}

console.log('end'); // now, this code can run!
```

So, when using `fetch()` or `response.json()`, we should always use `try` and `catch`, allowing us to gracefully handle errors:

```js
const getUsersAsyncAwait = async () => { 
  try {
    const response = await fetch('https://reqres.in/api/users');
  
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    // Since response.json() also returns a Promise, we can await it too.
    const users = await response.json();
    console.log("here is the data: ", users);
    
    return users;
  } catch (error) {
    console.log("Error caught! " + error.message);
    return null;
  }
};
```
