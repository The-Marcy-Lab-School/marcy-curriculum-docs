# Fetching Best Practices: Async & Await

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-6-async-await)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Intro: Promises Are Tricky](#intro-promises-are-tricky)
  - [So many `.then()` calls](#so-many-then-calls)
- [Async / Await](#async--await)
  - [What about Errors? Try and Catch](#what-about-errors-try-and-catch)
  - [The benefits of `async`/`await`](#the-benefits-of-asyncawait)
- [Best Practice: Standardize the Return Structure](#best-practice-standardize-the-return-structure)
- [Best Practice: Fetch Helper Functions](#best-practice-fetch-helper-functions)

## Key Concepts

* **`async` function** — a function that always returns a Promise. Lets you use `await` inside it.
* **`await`** — used inside an `async` function; pauses until the Promise resolves, then gives you the resolved value. Makes async code look like synchronous code.
* **`try` / `catch`** — run code in `try`; if any of it throws (including from `await`), execution jumps to `catch` so you can handle the error instead of crashing.

## Intro: Promises Are Tricky

Take a look at this code in `src/fetch-helpers.js`. What's missing from with this code? Why does it produce the error `"Cannot read properties of undefined (reading 'map')"`?

```js
export const getUsers = () => {
  const promiseToGetResponse = fetch('https://jsonplaceholder.typicode.com/users');

  return promiseToGetResponse
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      const promiseToExtractData = response.json();
    })
    .then((data) => {
      // Print out the data to see its structure
      console.log(data); // <-- undefined!

      // Here, we're trying to choose the data for each user to return
      const users = data.map((user) => ({
        id: user.id,
        name: user.name,
        username: user.username,
        email: user.email
      }));

      return users;
    })
    .catch((error) => {
      // TypeError: Cannot read properties of undefined (reading 'map'). Why?
      console.log("Error caught!", error);
      return null;
    })
};
```

<details><summary>Answer</summary>

We forgot to return `promiseToExtractData` from the first `.then` callback. This means that the first `.then()` returns a promise that resolves to `undefined` in the second `.then()` callback.

</details>

### So many `.then()` calls

When fetching, it is important to observe that we have two sequential asynchronous processes involved, each with their own promise and each with their own `.then()`: 

* `fetch()` returns a promise handled by the first `.then()`
  * When `fetch()` resolves, we start extracting data with `response.json()` which returns another promise handled by the second `.then()`.
    * When `response.json()` resolves, we do some data manipulation and return the formatted data. This is the final return statement.

```js
//////////////////////
// fetch-helpers.js
//////////////////////

export const getUsers = () => {
  // 1. fetch returns a promise
  const promiseToGetResponse = fetch('https://jsonplaceholder.typicode.com/users');

  // 2. `promise.then().then().catch()` returns a promise that resolves to the last value returned
  return promiseToGetResponse
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      const promiseToExtractData = response.json();
      return promiseToExtractData; // 3. This promise is handled by the next `.then()`
    })
    .then((data) => {
      console.log(data);

      // Here, we're trying to choose the data for each user to return
      const users = data.map((user) => ({
        id: user.id,
        name: user.name,
        username: user.username,
        email: user.email
      }));
    
      return users; // 4. The final return, `getUsers()` returns a promise that resolves to this value
    })
    .catch((error) => {
      console.log("Error caught!", error);
      return null; // 4. The final return, `getUsers()` returns a promise that *rejects* to this value
    })
};

//////////////////////
// main.js
//////////////////////

import { renderUsers } from './dom-helpers.js'
import { getUsers } from './fetch-helpers.js'

const main = () => {
  // getUsers returns a promise!
  getUsers().then((result) => {
    renderUsers(result);
  });
}

main();
```

We have to also remember that the entire `getUsers()` function is performing these asynchronous operations so it is also inherently asynchronous. When we return from `getUsers()`, we return a promise that the main code must resolve. So many promises to resolve! So many `.then()` calls!

There must be an easier way!

## Async / Await

Working with callbacks can be conceptually difficult to follow at times. So, an alternate syntax was created to achieve the same effect but without the need for callbacks. 

This approach utilizes the `async` and `await` keywords:

* The `await` keyword is placed before any **expression** that returns a Promise
  * It causes our code to pause and wait for the Promise to resolve. 
  * It then "unpacks" the Promise and returns the resolved value.
* The `async` keyword is placed before any **function** that uses `await` inside:
  * Any value returned by this function now will be wrapped in a Promise

{% tabs %}

{% tab title="async/await" %} 

```js
// A function marked with `async` returns a Promise
// You MUST put `async` if you want to use `await`
const getUsers = async () => { 
  // When we await a Promise, we are given the resolved value (the Response object)
  // An awaited statement becomes "blocking"
  const response = await fetch('https://jsonplaceholder.typicode.com/users');

  if (!response.ok) {
    throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
  }

  // Since response.json() also returns a Promise, we can await it too.
  const data = await response.json();

  console.log(data);

  // Choose the data for each user to return
  const users = data.map((user) => ({
    id: user.id,
    name: user.name,
    username: user.username,
    email: user.email
  }));

  return users;
};

// in main.js
const main = async () => {
  const users = await getUsers();
  renderUsers(users);

  // We can still use .then if we want to!
  getUsers().then((users) => renderUsers(users));
}
```

{% endtab %}

{% tab title=".then()/.catch()" %} 

```js
//////////////////////
// fetch-helpers.js
//////////////////////

export const getUsers = () => {
  // 1. fetch returns a promise
  const promiseToGetResponse = fetch('https://jsonplaceholder.typicode.com/users');

  // 2. `promise.then().then().catch()` returns a promise that resolves to the last value returned
  return promiseToGetResponse
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      const promiseToExtractData = response.json();
      return promiseToExtractData; // 3. This promise is handled by the next `.then()`
    })
    .then((data) => {
      console.log(data);

      // Here, we're trying to choose the data for each user to return
      const users = data.map((user) => ({
        id: user.id,
        name: user.name,
        username: user.username,
        email: user.email
      }));
    
      return users; // 4. The final return, `getUsers()` returns a promise that resolves to this value
    })
    .catch((error) => {
      console.log("Error caught!", error);
      return null; // 4. The final return, `getUsers()` returns a promise that *rejects* to this value
    })
};

//////////////////////
// main.js
//////////////////////

import { renderUsers } from './dom-helpers.js'
import { getUsers } from './fetch-helpers.js'

const main = () => {
  // getUsers returns a promise!
  getUsers().then((users) => {
    renderUsers(users);
  });
}

main();
```

{% endtab %}

{% endtabs %}

### What about Errors? Try and Catch

This entire process is very error prone in ways that we can't avoid:
- `fetch()` throws an error if the given URL is invalid or your network is down.
- `response.json()` throws an error if the `response` data is not in JSON format
- We manually throw our own error using `throw new Error()` when the fetch fails due to a 4xx or 5xx response:

```js
if (!response.ok) {
  throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
}
```

All of these errors crash the program, which we want to avoid.

`try`/`catch` is the standard way to handle those errors and it will prevent the application from crashing:

{% tabs %}

{% tab title="async/await" %} 

```js
const getUsers = async () => { 
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }

    const data = await response.json();
    console.log(data);
    
    const users = data.map((user) => ({
      id: user.id,
      name: user.name,
      username: user.username,
      email: user.email
    }));

    return users;
  }
  catch (error) {
    console.log("Error caught!", error);
    return null;
  }
};
```
{% endtab %}

{% tab title=".then()/.catch()" %} 

```js
export const getUsers = () => {
  // 1. fetch returns a promise
  const promiseToGetResponse = fetch('https://jsonplaceholder.typicode.com/users');

  // 2. `promise.then().then().catch()` returns a promise that resolves to the last value returned
  return promiseToGetResponse
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      const promiseToExtractData = response.json();
      return promiseToExtractData; // 3. This promise is handled by the next `.then()`
    })
    .then((data) => {
      console.log(data);

      // Here, we're trying to choose the data for each user to return
      const users = data.map((user) => ({
        id: user.id,
        name: user.name,
        username: user.username,
        email: user.email
      }));
    
      return users; // 4. The final return, `getUsers()` returns a promise that resolves to this value
    })
    .catch((error) => {
      console.log("Error caught!");
      console.log(error);
      return null; // 4. The final return, `getUsers()` returns a promise that *rejects* to this value
    })
};
```

{% endtab %}

{% endtabs %}

Note with either syntax, our main code doesn't need to catch anything. Our main code simply checks if the data is present or if it was `null` in which case we can render an error message to the screen.

```js
const main = async () => {
  const users = await getUsers(); // Error catching is handled by this function
  if (users === null) {
    renderError('An error occurred!');
    return;
  }
  renderError('');
  renderUsers(users);
}
```

### The benefits of `async`/`await`

Using the `async`/`await` syntax with `try` and `catch` has a number of benefits. The main ones are **readability** and **debuggability**.

* We can write async code in a synchronous-like manner
* We avoid having to write a bunch of callbacks
* We can avoid common mistakes made when using callbacks
* `try/catch` is a more general-purpose way of handling errors that can be used for more than just fetching.

## Best Practice: Standardize the Return Structure

There is still one way to improve `getUsers()`. 

We are currently telling the caller of the function (the main code) that an error occurred by returning `null`:

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
const getUsers = async () => { 
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
  
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    const data = await response.json();
    console.log(data);

    const users = data.map((user) => ({
      id: user.id,
      name: user.name,
      username: user.username,
      email: user.email
    }));
    
    return { data: users, error: null };
  } catch (error) {
    console.log("Error caught! " + error.message);
    return { data: null, error: error };
  }
};
```

This allows the main code to check the `.data` and `.error` properties to determine what to do next:

```js
const main = async () => {
  const result = await getUsers(); // Error catching is handled by this function
  
  if (result.error) {
    renderError(result.error.message);
    return;
  }
  renderError('');
  renderUsers(result.data);
}
```

**<details><summary>Q: Why can't we simply return the `users` if the fetch succeeds and the `error` if it fails?</summary>**

There won't be an easy way to differentiate the two if `result` can be either a `users` array or an `error` object. 

In both cases, we have a non-null value so `if (result === null)` checks won't work.

We *could* do something like `if (result instanceof Error)` to determine if the value is an `Error` object but it is entirely possible that the API we are fetching from has data in an error-like object.

This strategy avoids any ambiguity. It ensures that the `data` property contains the data we're fetching and the `error` property contains an error if one occurred.

</details>

## Best Practice: Fetch Helper Functions

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
const getUsers = () => {
  const { data, error } = fetchData('https://jsonplaceholder.typicode.com/users');
  const users = data.map((user) => ({
    id: user.id,
    name: user.name,
    username: user.username,
    email: user.email
  }));
  return users;
}

const postUser = (userData) => {
  const config = getPostConfig(userData);
  return fetchData('https://jsonplaceholder.typicode.com/users', config);
}
```

