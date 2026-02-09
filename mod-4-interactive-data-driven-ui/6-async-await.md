# Async & Await

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-6-async-await)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Intro: Promise Returning Is Tricky](#intro-promise-returning-is-tricky)
  - [The benefits of `async`/`await`](#the-benefits-of-asyncawait)
- [Fetching "Synchronously" with Async/Await](#fetching-synchronously-with-asyncawait)
  - [What about Errors?](#what-about-errors)
  - [Best Practice: Standardize the Return Structure](#best-practice-standardize-the-return-structure)
- [Fetch Helper Functions](#fetch-helper-functions)

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
  return fetch('https://jsonplaceholder.typicode.com/users')
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
  const response = await fetch('https://jsonplaceholder.typicode.com/users');

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

{% hint style="info" %}
We're assuming that `renderUsers` is implemented somewhere else and trust that it handles displaying the data.
{% endhint %}

### What about Errors?

There are some functions (like `fetch()` and `response.json()`) that are capable of throwing an error.

Remember, we manually throw our own error using `throw new Error('message')` when the fetch fails:

```js
if (!response.ok) {
  throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
}
```

While this allows us to control how the error is thrown, it still crashes the program which we want to avoid.

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
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
  
    if (!response.ok) {
      // This error will be caught and handled below
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

With this code, our main code doesn't need to catch anything. Our main code simply checks if the data is present or if it was `null` in which case we can render an error message to the screen.

```js
const main = async () => {
  const users = await getUsersAsyncAwait(); // Error catching is handled by this function
  
  if (users === null) {
    renderError('An error occurred!');
  } else {
    renderUsers(users);
  }

  // At this point, .then isn't much better
  getUsersAsyncAwait().then((users) => {
    if (users === null) {
      renderError('An error occurred!');
    } else {
      renderUsers(users);
    }
  })
}
```

{% hint style="info" %}
Again, we're assuming that `renderError()` and `renderUsers` are implemented somewhere else and trust that they handle displaying the error/data.
{% endhint %}

### Best Practice: Standardize the Return Structure

There is still one problem with this `getUsersAsyncAwait()` function: the only way to communicate to the caller whether an error occurred or the fetch was successful is by returning `null` for an error and the `users` for successfully fetched data.

```js
try {
  // fetching logic...

  // success -> return data
  return data;
} catch (error) {
  // failure -> return null
  return null;
}
```

What if we wanted to return the `error` itself? This could be useful if we want our main code to display the exact error message to the user.

One common solution to this is to *always* return an object with `data` and `error` properties:
* If the fetch succeeds, `error` will be `null`: `{ data: users, error: null }`
* If the fetch fails, `data` will be `null`: `{ data: null, error: error }`

```js
const getUsersAsyncAwait = async () => { 
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
  
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    // Since response.json() also returns a Promise, we can await it too.
    const users = await response.json();
    console.log("here is the data: ", users);
    
    return { data: users, error: null };
  } catch (error) {
    console.log("Error caught! " + error.message);
    return { data: null, error: error };
  }
};
```

This allows the main code to simply check the `.data` and `.error` properties to determine what to do next:

```js
const main = async () => {
  const result = await getUsersAsyncAwait(); // Error catching is handled by this function
  
  if (result.error) {
    renderError(result.error.message);
  } else {
    renderUsers(result.data);
  }
}
```

**<details><summary>Q: Why can't we simply return the `users` if the fetch succeeds and the `error` if it fails?</summary>**

There won't be an easy way to differentiate the two if `result` can be either a `users` array or an `error` object. 

In both cases, we have a non-null value so `if (result === null)` checks won't work.

We *could* do something like `if (result instanceof Error)` to determine if the value is an `Error` object but it is entirely possible that the API we are fetching from has data in an error-like object.

This strategy avoids any ambiguity. It ensures that the `data` property contains the data we're fetching and the `error` property contains an error if one occurred.

</details>

## Fetch Helper Functions

Since most of this code is "boilerplate", it is a good practice to create a helper function that handles most of this fetching logic for us:

```js
// fetch-helpers.js
export const fetchData = (url, config) => {
  try {
    const response = await fetch(url, config);
  
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    // Since response.json() also returns a Promise, we can await it too.
    const data = await response.json();
    console.log("here is the data: ", data);
    
    return { data, error: null };
  } catch (error) {
    console.log("Error caught! " + error.message);
    return { data: null, error };
  }
}
```

We can also make helpers to create config objects for us:

```js
// fetch-helpers.js
export const getPostConfig = (bodyData, options) => {
  const config = {
    method: 'POST',
    body: JSON.stringify(bodyData),
    headers: {
      'Content-Type': 'application/json',
      'accept': 'application/json'
    },
    ...options
  };

  return config;
}

export const getPatchConfig = (bodyData, options) => {
  const config = {
    method: 'PATCH',
    body: JSON.stringify(bodyData),
    headers: {
      'Content-Type': 'application/json',
      'accept': 'application/json'
    },
    ...options
  };

  return config;
}

export const getDeleteConfig = (options) => {
  const config = {
    method: 'DELETE',
    headers: {
      'accept': 'application/json'
    },
    ...options
  };

  return config;
}
```

We can use these functions like this to really simplify the process of writing functions for specific endpoints / HTTP methods:

```js
const getUsersAsyncAwait = () => {
  return fetchData('https://jsonplaceholder.typicode.com/users');
}

const postUser = (userData) => {
  const config = getPostConfig(userData);
  return fetchData('https://jsonplaceholder.typicode.com/users', config);
}
```

