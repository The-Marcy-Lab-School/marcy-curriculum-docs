# Async & Await

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-0-3-async-await)!
{% endhint %}

- [Intro: Promise Returning Is Tricky](#intro-promise-returning-is-tricky)
  - [The benefits of `async`/`await`](#the-benefits-of-asyncawait)
- [Fetching "Synchronously" with Async/Await](#fetching-synchronously-with-asyncawait)
  - [Handling Errors with Try/Catch](#handling-errors-with-trycatch)
- [Making a Generic Fetch Helper](#making-a-generic-fetch-helper)
  - [Using the Helper](#using-the-helper)
  - [Why return a tuple?](#why-return-a-tuple)


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

  // Or, we can still use .then if we want
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
    const jsonData = await response.json();
    console.log("here is the data: ", data);
    return jsonData;
  } catch (error) {
    console.log("Error caught! " + error.message);
    return null;
  }
};
```

## Making a Generic Fetch Helper

The code for fetching data is almost always the same:

* In a `try` block, `fetch` from a URL and parse the response as JSON
* In a `catch` block, log the caught `error`. Any error that occurs in the `try` block will be caught by this one shared `catch` block

So, we can refactor our code a bit, add in some safety measures, and create a helper function that abstracts away this logic. The function below does the following:

* It accepts a `url` and an `options` argument allowing other types of requests to be made (POST, PATCH/PUT, DELETE, etc...). If the caller of `fetchData` does not provide `options`, it will default to an empty object.
* If the `!response.ok` guard clause is triggered, an error is thrown instead of returning. This let's us handle `4xx` and `5xx` responses in the `catch` block and treat them the same as errors thrown by `fetch` and `response.json()`.
* It checks the content type of the `response` to determine how to parse (with `response.json()` or `response.text()`)
* It returns the data in a "tuple" format — an array with 2 values where the first value is _always_ the data (if present) and the second value is _always_ the error (if present). Only one of the two values will ever be present.

```js
const fetchData = async (url, options = {}) => {
  try {
    const response = await fetch(url, options);

    // Throw an error if the response was not 2xx - let the catch statement handle it
    if (!response.ok) throw new Error(`Fetch failed. ${response.status} ${response.statusText}`)

    // Guard clause: make sure that the content type of the response is JSON before reading it
    const contentType = response.headers.get('content-type');
    if (contentType === null || !contentType.includes('application/json')) {
      // If the contentType of the response is not JSON, read the stream as plain text
      const textData = await response.text();
      return [textData, null]
    }

    // Otherwsie, read the stream as JSON
    const jsonData = await response.json();
    return [jsonData, null]
  }
  catch (error) {
    // if there was an error, log it and return a tuple: [data, error]
    console.error(error.message);
    return [null, error];
  }
}
```

We've added in one detail that can occasionally trip us up: the data is NOT in JSON format. `DELETE` requests, for example, often do not return ANY data in response. In other cases, we may be fetching other data formats. In those cases, we can't use the `response.json` function and instead will use `response.text` to read the incoming response body `ReadableStream`. 

So, to make our function more flexible, we check the `response.headers` to determine the `contentType` and then use `response.json()` if we're dealing with JSON, and `response.text()` if we're dealing with anything else.

### Using the Helper

With the helper built, we can make more specific helper functions like this

```js
// GET Requests are pretty straight forward. 
// Because fetchData is marked with 'async', it returns a promise
export const getUsers = () => {
  return fetchData('https://reqres.in/api/users')
}

export const getUser = (userId) => {
  return fetchData(`https://reqres.in/api/users/${userId}`);
}

// POST requests require some configuration before fetching
export const createUser = async (name, job) => {
  const newUser = { name, job };
  const options = {
    method: "POST",
    body: JSON.stringify(newUser),
    headers: {
      "Content-Type": "application/json",
    }
  };
  // Here, we use the `fetchData` helper.
  // Since it is an `async` function, it returns a Promise
  return fetchData(`https://reqres.in/api/users`, options);
}

// in main.js
const main = async () => {
  // these functions now return tuples, allowing for error rendering
  const [user, error] = await createUser("ben", "instructor");
  if (!error) {
    renderUser(user);
  } else {
    renderError(error);
  }
};
```

### Why return a tuple?

You may be wondering, why couldn't we write this helper function such that it just returns the data if there are no errors, or returns the error if there is one?

The reason we don't do this is to make the code that uses this function cleaner. The code that uses `fetchData` will need to know if the data it receives is an error or JSON data. The problem is that `error` objects and the `jsonData` can often be difficult to differentiate. An `error` object will have a `message` property, and often times, so do JSON response objects! Take the [Dog Image API](https://dog.ceo/dog-api/) as an example. It will return its data like this:

```json
{
    "message": "https://images.dog.ceo/breeds/mix/dog3.jpg",
    "status": "success"
}
```

Since `error` objects and `jsonData` objects can look so similar in their structure, we can't simply check the structure of the returned object to know if it was an error or JSON data. We _could_ do something like this:

```js
const getDogImage = async () => {
  const dogImage = await fetchData('https://dog.ceo/api/breeds/image/random')
  console.log(dogImage.message); // I can't know if this is going to be an error message or a dog picture.

  // I could check the type of the object
  if (dogImage instanceof Error) {
    // handle the error
  }
  else {
    // use the dog image
  }
}
```

But if our `fetchData` function always returns a `[data, error]` tuple where one of those values will ALWAYS be `null` while the other is defined, then the code that uses `fetchData` will become much cleaner:

```js
const getDogImage = async () => {
  const [dogImage, error] = await fetchData('https://dog.ceo/api/breeds/image/random')
  
  if (error) {
    // handle the error
  }
  else {
    // use the dog image
  }
}
```
