# Async & Await

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-0-3-async-await)!
{% endhint %}

* [Fetching "Synchronously" with Async/Await](4-async-await.md#fetching-synchronously-with-asyncawait)
* [Handling Errors with Try/Catch](4-async-await.md#handling-errors-with-trycatch)
* [The benefits of `async`/`await`](4-async-await.md#the-benefits-of-asyncawait)
* [Making a generic fetch helper](4-async-await.md#making-a-generic-fetch-helper)
  * [Why return a tuple?](4-async-await.md#why-return-a-tuple)

## Fetching "Synchronously" with Async/Await

So far we have written `fetch` code like this:

```js
fetch('https://pokeapi.co/api/v2/pokemon/pikachu')
  .then((response) => {
    if (!response.ok) {
      return console.log(`Fetch failed. ${response.status} ${response.statusText}`)
    }
    return response.json()
  })
  .then((responseData) => {
      console.log("Here is your data:", responseData);
      // do something with the response data
  })
  .catch((error) => {
    console.log("Error caught!");
    console.error(error.message);
  })
```

We have taken the `Promise` returned by `fetch()` and used the `.then` and `.catch` methods to schedule callbacks to execute when the promise resolves/rejects.

However, an alternate syntax was created to achieve the same effect but in a more "synchronous-like" manner. This approach utilizes the `async` and `await` keywords

```jsx
// A function marked with `async` is "non-blocking" and returns a Promise
// You MUST put `await` statements inside of a function marked with `async`
const getPikachuData = async () => { 
  // When we await a Promise, we are given the resolved value (the Response object)
  // An awaited statement becomes "blocking"
  const response = await fetch('https://pokeapi.co/api/v2/pokemon/pikachu');
  
  if (!response.ok) {
    return console.log(`Fetch failed. ${response.status} ${response.statusText}`)
  }

  // Since response.json() also returns a Promise, we can await it too.
  const jsonData = await response.json();

  // now we do something with the data
  console.log("Here is your data:", responseData);
};

getPikachuData(); // non-blocking and returns a Promise (we can .then it if we wanted to)
console.log('when does this happen?') 
```

* The `await` keyword causes our code to pause and wait for the Promise to resolve. It then unpacks the Promise and returns the resolved value.
* The `async` keyword does two things:
  * First, it labels a function as asynchronous. This is required for any function that makes use of the `await` keyword
  * Second, it wraps the function’s returned value in a Promise. If we were to store the returned value of `getPikachuData()`, it would be a Promise.

## Handling Errors with Try/Catch

There are some functions (like `fetch()`) that are capable of throwing an error.

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

So, when using a function that can throw an error like `fetch()` or `response.json()`, we should always use `try` and `catch`:

```js
const getPikachuData = async () => { 
  try {
    const response = await fetch('https://pokeapi.co/api/v2/pokemon/pikachu');
    
    if (!response.ok) {
      return console.log(`Fetch failed. ${response.status} ${response.statusText}`)
    }

    // Since response.json() also returns a Promise, we can await it too.
    const jsonData = await response.json();

    // now we do something with the data
    console.log("Here is your data:", jsonData);
  }
  catch (error) {
    console.log("Error caught!");
    console.error(error.message);
  }
};

getPikachuData();
```

## The benefits of `async`/`await`

Using the `async`/`await` syntax with `try` and `catch` has a number of benefits. The main ones being **readability** and **debuggability**.

* We can write async code in a synchronous-like manner
* We avoid having to write a bunch of callbacks
* We can avoid common mistakes made when using callbacks
* `try/catch` is a more general-purpose way of handling errors that can be used for more than just fetching.

<details>

<summary>For example, what's wrong with this code? Why does it print `undefined`?</summary>

Forgot to return from the first `.then` when chaining to a second `.then`

</details>



```js
const promise = fetch('https://reqres.in/api/users')

promise
  .then((response) => {
    if (!response.ok) throw Error(response.status);
    response.json();
  })
  .then((data) => {
    console.log(data); // print undefined
  })
  .catch((error) => {
    console.log("Error caught!");
    console.log(error.message);
  })
```

## Making a generic fetch helper

The code for fetching data is almost always the same:

* In a `try` block, `fetch` from a URL and parse the response as JSON
* In a `catch` block, log the caught `error`. Any error that occurs in the `try` block will be caught by this one shared `catch` block

So, we can refactor our code a bit, add in some safety measures, and create a helper function that abstracts away this logic:

```js
const fetchData = async (url, options = {}) => {
  try {
    const response = await fetch(url, options);

    // Throw an error if the response was not 2xx - let the catch statement handle it
    if (!response.ok) throw new Error(`Fetch failed. ${response.status} ${response.statusText}`)

    // Make sure that the content type of the response is JSON before parsing it
    // and return a tuple with the data and a null error.
    const contentType = response.headers.get('content-type');
    if (contentType !== null && contentType.includes('application/json')) {
      const jsonData = await response.json();
      return [jsonData, null]
    }

    // If the contentType of the response is not JSON, parse it as plain
    // text and return a tuple with a null error
    const textData = await response.text();
    return [textData, null]
  }
  catch (error) {
    // if there was an error, log it and return a tuple: [data, error]
    console.error(error.message);
    return [null, error];
  }
}
```

Let's break down this `fetchData` helper

* It accepts a `url` and an `options` argument allowing other types of requests to be made (POST, PATCH/PUT, DELETE, etc...). If the caller of `fetchData` does not provide `options`, it will default to an empty object.
* If the `!response.ok` guard clause is triggered, an error is thrown instead of returning. This let's us handle `4xx` and `5xx` responses in the `catch` block and treat them the same as errors thrown by `fetch` and `response.json()`.
* It checks the content type of the `response` to determine how to parse (with `response.json()` or `response.text()`)
* It returns the data in a "tuple" format — an array with 2 values where the first value is _always_ the data (if present) and the second value is _always_ the error (if present). Only one of the two values will ever be present.

With the helper built, we can use it like this

```js
// Example Using the helper - remember to make it async!
const postUser = async (user) => {
  const options = {
    method: "POST",
    body: JSON.stringify(user),
    headers: {
      "Content-Type": "application/json",
    }
  }

  // Here, we use the `fetchData` helper.
  // Since it is an `async` function, it returns a Promise that we can `await`
  const [newUserData, error] = await fetchData('https://reqres.in/api/users', options);
  
  // Remember, `fetchData` will return a tuple where the first value is ALWAYS the
  // fetched data (if the fetch was successful) and the second value is ALWAYS the
  // error (if the fetch was unsuccessful). We can then deal with th
  if (error) {
    document.querySelector("#error-text").innerText = "sorry an error occurred, please try again later"
  };
  else {
    renderNewUser(newUserData) 
  }
}

postUser({ name: "morpheus", job: "leader" })
```

### Why return a tuple?

You may be wondering, why couldn't we write this helper function such that it just returns the data if there are no errors, or returns the error if there is one?

```js
const fetchData = async (url, options = {}) => {
  try {
    const response = await fetch(url, options);

    // Throw an error if the response was not 2xx - let the catch statement handle it
    if (!response.ok) throw new Error(`Fetch failed. ${response.status} ${response.statusText}`)

    // Make sure that the content type of the response is JSON before parsing it
    // and return a tuple with the data and a null error.
    const contentType = response.headers.get('content-type');
    if (contentType !== null && contentType.includes('application/json')) {
      const jsonData = await response.json();
      return jsonData
    }

    // If the contentType of the response is not JSON, parse it as plain
    // text and return a tuple with a null error
    const textData = await response.text();
    return textData;
  }
  catch (error) {
    console.error(error.message);
    return error;
  }
}
```

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
