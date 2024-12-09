# Fetch

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-0-1-intro-to-fetch)!
{% endhint %}

- [What is a Web API?](#what-is-a-web-api)
- [The `fetch()` function](#the-fetch-function)
  - [Steps 1 and 2: Getting A Response Object](#steps-1-and-2-getting-a-response-object)
  - [Steps 3 and 4: Reading Data From the Response Object](#steps-3-and-4-reading-data-from-the-response-object)
  - [Steps 5 and 6: Do Something With the Data (and Errors)](#steps-5-and-6-do-something-with-the-data-and-errors)
- [Challenge: Make Your Own API App](#challenge-make-your-own-api-app)
- [More Information About Requests and Responses](#more-information-about-requests-and-responses)
  - [HTTP Status Codes](#http-status-codes)
  - [URL Structure](#url-structure)
  - [Kinds of Requests - HTTP Verbs](#kinds-of-requests---http-verbs)

## What is a Web API?

In the last lesson, we learned about how to handle asynchronous functions with Promises. Today we will learn perhaps the most important asynchronous function, `fetch`. The `fetch` function lets us request data from a **web API**

![](img/http-request-response-cycle.png)

Remember, API stands for "Application Programming Interface". An interface can be thought of as a set of tools or resources. So an API is a set of tools that an application can interact with to create a program. Very generic, right?

The DOM API is one type of API that provides an interface made up of objects and methods that our browser applications can use to dynamically manipulate a web page.

```js
document.querySelector("h1").textContent = "Hello World!";
```

A **web API** is another type of API whose interface is made up of URLs that each provide access to a different piece of data. To use a web API, instead of calling a function, we send an HTTP(S) request to a URL called an **API endpoint**.

For example, try entering these two API endpoints into your browser's address bar:

https://dog.ceo/api/breeds/list/all

https://dog.ceo/api/breeds/image/random

When we use the HTTPS protocol, we are sending a request over the internet to the dog.ceo servers. Those servers are set up to listen for incoming requests and send back the data according the requested endpoint (the specific URL that was used in the request)

{% hint style="info" %}

Web APIs make it possible for applications to utilize data from other sources and combine them in interesting ways. For example, we can build an application that uses the Google Maps API to get directions from point A to point B and then use a weather API to display the weather along the route.

{% endhint %}

But before we get ahead of ourselves, let's look at how to use the `fetch` function to send an HTTP request to a web API.

## The `fetch()` function

The `fetch` function is used to send HTTP requests from within our programs. It returns a `Promise` object. 

When, using the `fetch` function, we will follow these 6 steps:

```js
// 1. Invoke fetch with an API endpoint. A promise is returned.
const fetchPromise = fetch('API_ENDPOINT_GOES_HERE');

// 2. Define promise handlers with .then and .catch
fetchPromise
  .then((response) => {
    // 3. Check that the response is ok. If it isn't throw a useful error.
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
    }

    // 4. Start reading the response body's ReadableStream
    const readingPromise = response.json();
    return readingPromise
  })
  .then((responseBody) => {
    // 5. When the response body is read, do something with it!
    // What you do will depend on the API you're using
    // TIP: Print the structure of the body to see what you're working with.
    console.log(responseBody);
  })
  .catch((err) => {
    // 6. Handle Errors
    console.error(err);
  })
```

Let's break this down.

### Steps 1 and 2: Getting A Response Object

Let's start with the first two steps:
1. Invoke `fetch` with an API endpoint (a specific URL provided by a web API). A promise is returned.
2. Define promise handlers with `.then` and `.catch`

To test this, we can use the API endpoint `https://dog.ceo/api/breeds/image/random` and, in the `.then()` handler, print out the `response` object that the returned promise resolves to.

```js
const fetchPromise = fetch('https://dog.ceo/api/breeds/image/random');

fetchPromise
  .then((response) => {
    console.log(response)	

    // the rest of the code ...
  })
```

We should get something that looks like this:

![A response object printed to the console](img/response-object.png)

The returned object is a [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) object. It contains useful information about both the request and the response including:
  * `response.body` — a stream of data that we can read to get the response data
  * `response.ok` — indicates if the response succeeded or failed
  * `response.status` — a 3-digit code the type of success or failure that occurred (`200` means the requested data was returned)
  * `response.statusText` — a string with more information about the response
  * `response.url` — the endpoint requested

The `response.body` is the main thing we want from this response, but as you can see it is a `ReadableStream` object, not a string or normal object with the data inside.

Let's look at the next steps to see how we get the data from this `ReadableStream`.

### Steps 3 and 4: Reading Data From the Response Object

Our objective is to get the data from this response so that we can use it in our application. The code below accomplishes that task, but how?

```js
fetchPromise
  .then((response) => {
    // 3. Check that the response is ok. If it isn't throw a useful error.
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
    }

    // 4. Start reading the response body's ReadableStream
    const readingPromise = response.json();
    return readingPromise;
  })
```

Step 3 is a guard clause for step 4, so let's start with step 4 and then return to step 3:

![Data is downloaded and processed in chunks, rather than all at once](img/readable-stream.png)

When an HTTP request is sent to a web API, the response data isn't sent back all at once. Instead, it is sent back in a continuous "stream" of data chunks. That's what the `ReadableStream` in the `response.body` is.

**<details><summary>Q: Why might it be useful to send data in a stream of chunks, rather than all at once</summary>**
Getting data from another source requires two steps: first downloading the data and then reading it. If the data is really large, downloading the data can become a blocking task. 
 
By chunking the data, we can download the data in smaller pieces and start the process of reading it as it comes in. If we had to wait for all of the data to be downloaded before we started reading it, the process would take longer.
</details>

Reading the incoming data stream is an asynchronous process that we can initiate by invoking the `response.json()` function, which will read the data stream as JSON.

{% hint style="info" %}
Using `response.json()` assumes that the data is in JSON format (99% of the time it will be). If it isn't, there are [other methods available](https://developer.mozilla.org/en-US/docs/Web/API/Response#instance_methods).
{% endhint %}


`response.json()` returns a promise.

```js
// 4. Start reading the response body's ReadableStream
const readingPromise = response.json();
```

However, before we do this, we need to check to see if the `response` failed. If it did, then there won't be any data to read in. We don't want to start an asynchronous process unnecessarily if we don't have to. 

So, we include a guard clause that checks the `response.ok` property and throws a useful error with the status of the response:

```js
fetchPromise
  .then((response) => {
    // 3. Check that the response is ok. If it is NOT, throw a useful error.
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
    }

    // Assuming that the response IS ok, go ahead and read the data stream.
  })
```

So, what we have so far is this:

```js
const fetchPromise = fetch('https://dog.ceo/api/breeds/image/random');

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
    }

    const readingPromise = response.json();
    return readingPromise;
  })
```

Note that the ONLY thing that we've changed from the pattern is the API endpoint that we used to invoke `fetch()`. Most of this code is "boilerplate" (it will be the same every time).

But why do we return the `readingPromise` from the callback given to `.then()`?

### Steps 5 and 6: Do Something With the Data (and Errors)

Remember, the `Promise.then()` method returns a Promise. That Promise will resolve to whatever is returned by the callback invoked by `.then()`!

So, if we return the `readingPromise` from `.then()`, then we can handle that Promise by chaining another invocation of `.then()` to our first one! When the `readingPromise` finishes reading the `response.body` data stream, the second `.then()` will be invoked with the requested data:

```js
const fetchPromise = fetch('https://dog.ceo/api/breeds/image/random');

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
    }
    const readingPromise = response.json();

    // readingPromise can be handled by the next .then, but only if we return it
    return readingPromise;
  })
  .then((responseBody) => {
    // When the response body is read, do something with it!
    // TIP: Print the structure of the body to see what you're working with.
    console.log(responseBody);
    dogImage.src = responseBody.message;
  })
  .catch((err) => {
    // 6. Handle Errors
    console.error(err);
  })
```

- What you do will depend on the API you're using
- For example, the dog.ceo API puts the image source URL in the `responseBody.message` property. 
- Every web API should include documentation that outlines the structure of the `responseBody` data.

The final step of handling errors isn't that new or interesting. If an error is thrown (or if a Promise rejects), we catch it and print it out. However, we should be aware of what can cause an error (or a rejected Promise). 

There are two likely causes of errors / rejected Promises:
* A `fetch()` Promise rejects when the request itself fails. For example, the URL might be malformed (`hxxp://example.com`) or there is a network error (you don't have internet). 
  * **Note**: a `fetch()` promise can still resolve but have `response.ok` be `false`. For example, if the URL is properly formatted but the API you are requesting from is down, the `fetch()` call itself will work but `response.ok` will be `false`. You can check the [HTTP status code](#http-status-codes) to see exactly what caused the request to fail. In this case, we manually throw our own `Error` in step 3.
* A `response.json()` Promise rejects when the `response` body is NOT in JSON format and therefore cannot be read.


## Challenge: Make Your Own API App

Here we are using the API from https://dog.ceo/ and the specific “endpoint” URL https://dog.ceo/api/breeds/image/random. Try these other APIs:

- https://pokeapi.co/api/v2/pokemon/pikachu
- https://v2.jokeapi.dev/joke/Programming
- https://api.sunrise-sunset.org/json?lat=40.7128&lng=-74.0060&tzid=America/New_York

How can you build an application that fetches this data and then displays it in some useful way?


## More Information About Requests and Responses

### HTTP Status Codes

Every Response that we receive from a server will include a status code indicating how the request was processed. Was the resource found and returned? Was the resource not found? What there an error? Did the POST request successfully create a new resource? 

Responses are grouped in five classes:

- Informational responses (100 – 199)
- Successful responses (200 – 299)
- Redirection messages (300 – 399)
- Client error responses (400 – 499)
- Server error responses (500 – 599)

Important ones to know are 200, 201, 204, 404, and 500

### URL Structure

Every URL has a few parts. Understanding those parts can help us fetch precisely the data we want.

Consider this URL which tells us information about the sunrise and sunset at a particular [latitude and longitude](https://en.wikipedia.org/wiki/Geographic_coordinate_system#Latitude_and_longitude):

```
https://api.sunrise-sunset.org/json?lat=36.7201600&lng=-4.4203400&date=2023-3-15
```

Let's break it down:

- `https://api.sunrise-sunset.org` — This is the **host**. It tells the client where the resource is hosted/located.
- `/json` - This is the **path**. It shows what resource is being requested
- `?lat=36.7201600&lng=-4.4203400&date=2023-3-15` - These are **query parameters** and this particular URL has 3: `lat`, `lng`, and `date`. Query parameters begin with a `?` are are separated with `&`. Each parameter uses the format `name=value`. Try changing the `date` parameter!

When using a new API, make sure to look at that API's [documentation](https://api.sunrise-sunset.org/) (often found at the host address) to understand how to format the request URL.

### Kinds of Requests - HTTP Verbs

HTTP requests can be made for a variety of purposes. Consider these examples related to Instagram:

- The client requests to see all posts made by Beyonce (Read)
- The client requests to post a new picture on their profile (Create)
- The client requests to update a post they made yesterday (Update)
- The client requests to delete a post they made yesterday (Delete)

Each of these actions has an HTTP verb that is associated with it.

- `GET` - Read
- `POST` - Create
- `PATCH` - Update
- `DELETE` - Delete

This HTTP verb or “method” is sent in the HTTP Request so that the server knows what kind of request it is receiving.

The default behavior of using `fetch` is to make a `GET` request, but we can also make other kinds of requests by adding a second `options` argument to `fetch()`:

```jsx
const newUser = { name: "morpheus", job: "leader" };

const options = {
  method: "POST",                      // The type of HTTP request
  body: JSON.stringify(newUser),       // The data to be sent to the API
  headers: {
    "Content-Type": "application/json" // The format of the body's data
  }  
}

// This is a good API to practice GET/POST/PATCH/DELETE requests
const url = 'https://reqres.in/api/users';

// Notice that the options object is provided as the second arg
fetch(url, options)
  .then((response) => {
    if (!response.ok) {
      return console.log(`Fetch failed. ${response.status} ${response.statusText}`)
    }
    return response.json();
  })
  .then((responseData) => {
      // A POST request will return the object created with an id and a createdAt timestamp
      console.log("Here is your data:", responseData);
  })
  .catch((error) => {
    console.log("Error caught!");
    console.error(error.message);
  })
```

Most of the `options` object is boilerplate (it's mostly the same each time): 
* The `method` determines the kind of request
* The `body` determines **what** we send to the server. Note that it must be `JSON.stringify()`-ed first.
* The `headers` object provides meta-data about the request. It can provide many pieces of information but here all we need to share is the format of the data we are sending to the API.

The url used here is from https://reqres.in which is a great "dummy" API that you can use to practice making various kinds of fetch requests.
