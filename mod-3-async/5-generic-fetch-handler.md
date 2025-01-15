# Making a Generic Fetch Helper

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-0-3-async-await)!
{% endhint %}

- [The Fetch Helper](#the-fetch-helper)
- [Why Are We Checking the Content Type?](#why-are-we-checking-the-content-type)
- [Why return a tuple?](#why-return-a-tuple)
- [Using the Helper](#using-the-helper)

## The Fetch Helper

The code for fetching data is almost always the same:

* In a `try` block, `fetch` from a URL and parse the response as JSON
* In a `catch` block, log the caught `error`. Any error that occurs in the `try` block will be caught by this one shared `catch` block

So, we can refactor our code a bit, add in some safety measures, and create a helper function that abstracts away this logic. 

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

    // Return a "tuple", making error handling on the "receiving" end easier
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

The function below does the following:

* It accepts a `url` and an `options` argument allowing other types of requests to be made (POST, PATCH/PUT, DELETE, etc...). If the caller of `fetchData` does not provide `options`, it will default to an empty object.
* If the `!response.ok` guard clause is triggered, an error is thrown instead of returning. This let's us handle `4xx` and `5xx` responses in the `catch` block and treat them the same as errors thrown by `fetch` and `response.json()`.
* It checks the content type of the `response` to determine how to parse (with `response.json()` or `response.text()`)
* It returns the data in a "tuple" format — an array with 2 values where the first value is _always_ the data (if present) and the second value is _always_ the error (if present). Only one of the two values will ever be present.

## Why Are We Checking the Content Type?

We've added in one detail that can occasionally trip us up: the data is NOT in JSON format. `DELETE` requests, for example, often do not return ANY data in response. In other cases, we may be fetching other data formats. In those cases, we can't use the `response.json` function and instead will use `response.text` to read the incoming response body `ReadableStream`. 

So, to make our function more flexible, we check the `response.headers` to determine the `contentType` and then use `response.json()` if we're dealing with JSON, and `response.text()` if we're dealing with anything else.

## Why return a tuple?

You may be wondering, why couldn't we write this helper function such that it just returns the data if there are no errors, or returns the error if there is one?

The reason we don't do this is to make the code that uses this function cleaner. The code that uses `fetchData` will need to know if the data it receives is an error or JSON data. 

The problem is that `error` objects and the `jsonData` can often be difficult to differentiate. An `error` object will have a `message` property, and often times, so do JSON response objects! Take the [Dog Image API](https://dog.ceo/dog-api/) as an example. It will return its data like this:

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
  // either dogImage or error will be null
  const [dogImage, error] = await fetchData('https://dog.ceo/api/breeds/image/random')
  
  if (error) {
    // if the error exists at all, that means that dogImage is null and we should handle the error
  }
  else {
    // otherwise, the error must be null and we can handle the dogImage
  }
}
```

## Using the Helper

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
export const createUser = (name, job) => {
  const newUser = { name, job };
  const options = {
    method: "POST",
    body: JSON.stringify(newUser),
    headers: {
      "Content-Type": "application/json",
    }
  };
  return fetchData(`https://reqres.in/api/users`, options);
}

//////////////////////////////////////////

// in main.js
const main = async () => {
  // these functions now return tuples, allowing for graceful error rendering
  const [user, error] = await createUser("ben", "instructor");
  if (error) {
    renderError(error);
  } else {
    renderUser(user);
  }
};
```
