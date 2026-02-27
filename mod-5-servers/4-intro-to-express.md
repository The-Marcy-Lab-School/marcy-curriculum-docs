# 1. Intro to Express

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-2-intro-to-express)!
{% endhint %}

In the last lesson, you built an HTTP server from scratch using Node's built-in `node:http` module. You manually matched routes with `if/else`, set headers on every response, called `res.end()` on every code path, and parsed query strings by hand. It worked — but it took a lot of boilerplate.

**Express** is a Node framework that wraps `node:http` and handles all of that boilerplate for you. In this lesson you'll see exactly what Express gives you, learn the core patterns you'll use in every server you build, and finish with a clear picture of what's coming next.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Review: The Client-Server Model](#review-the-client-server-model)
- [From `node:http` to Express](#from-nodehttp-to-express)
  - [What Express Handles For You](#what-express-handles-for-you)
  - [Installing and Creating the App](#installing-and-creating-the-app)
- [Endpoints and Controllers](#endpoints-and-controllers)
  - [The Controller Signature](#the-controller-signature)
  - [Sending Responses](#sending-responses)
  - [Specifying Status](#specifying-status)
- [The Request Object](#the-request-object)
  - [Query Strings](#query-strings)
  - [Route Parameters](#route-parameters)
- [What's Next: Middleware](#whats-next-middleware)


## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is Express and what problem does it solve compared to `node:http`?
2. What is an endpoint? What is a controller?
3. How do you define an endpoint and attach a controller using Express?
4. What are query strings? How do you access them inside a controller?
5. What are route parameters? How do you access them inside a controller?

## Key Concepts

* **Express** — the most popular Node framework for building HTTP server applications. It wraps `node:http` and removes repetitive boilerplate.
* **Express `app`** — the object returned by calling `express()`. Used to register endpoints, middleware, and start the server.
* **Endpoint** — a specific URL path clients can send requests to. For example: `/api/users` or `/api/posts/:id`.
* **Controller** — a callback function invoked when a matching request arrives. It reads the request and sends a response.
* **`req`** — the request object. Contains the URL, method, headers, query strings, and body of the incoming request.
* **`res`** — the response object. Has methods for sending data back to the client.
* **`next`** — a function that passes the request to the next handler in the chain. Used by middleware (covered in the next lesson).
* **query strings** — key-value pairs appended to a URL after `?` and separated by `&`, used to filter or customize a response. Accessible via `req.query`.
* **Route Parameters** — named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the `req.params` object.

## Review: The Client-Server Model

You've already learned this pattern — but it's worth keeping in front of you as the foundation for everything in this module.

A **server** receives requests from a **client** and sends back responses. The server decides how to respond based on the URL and HTTP method of each request.

![express diagram](./img/4-intro-to-express/express-diagram-simple.svg)

The server we're building is the middle layer: it receives requests from the browser, does whatever work is needed (looking up data, calling an API, reading from a database), and sends a response back.

## From `node:http` to Express

### What Express Handles For You

Here is the same `GET /api/todos` route written with `node:http` and with Express:

```js
// node:http — manual
if (req.method === 'GET' && req.url === '/api/todos') {
  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify(todos));
  return;
}

// Express — the same route
app.get('/api/todos', (req, res) => {
  res.send(todos);
});
```

`res.send(todos)` does the work of three lines: it sets the status to `200`, sets the `Content-Type` header to `application/json`, and serializes the object to JSON — all at once.

More broadly, Express handles everything you had to do manually with `node:http`:

| With `node:http`                               | With Express                    |
| ---------------------------------------------- | ------------------------------- |
| `if/else` chains on `req.method` and `req.url` | `app.get()`, `app.post()`, etc. |
| `res.writeHead()` on every response            | `res.send()` handles it         |
| `JSON.stringify()` on every response body      | `res.send()` handles it         |
| `res.end()` on every code path                 | `res.send()` handles it         |
| `new URL(req.url)` to parse query strings      | `req.query`                     |
| Stream events to read the request body         | `express.json()` middleware     |

Express doesn't add capabilities that `node:http` doesn't have — everything it does, you *could* write yourself. It just removes the repetition so you can focus on what your server actually does.

### Installing and Creating the App

```sh
npm i express
```

The main export of Express is the `express` function, which returns an `app` object used to configure everything:

```js
// index.js
const express = require('express');
const app = express();

const port = 8080;
app.listen(port, () => console.log(`Listening at http://localhost:${port}`));
```

`app.listen()` starts the server on the given port — the same role `server.listen()` played with `node:http`. During development, `localhost` (your own machine) is the host and `8080` is the port, so the server is reachable at `http://localhost:8080`.

## Endpoints and Controllers

With `node:http` you routed requests using `if/else` chains. Express replaces that with dedicated methods for each HTTP verb:

```js
app.get(path, controller)     // GET requests
app.post(path, controller)    // POST requests
app.patch(path, controller)   // PATCH requests
app.delete(path, controller)  // DELETE requests
```

An **endpoint** is the URL `path` — the string that tells Express which requests to match. A **controller** is the callback that runs when a matching request arrives.

```js
app.get('/', serveHTML);
app.get('/api/hello', serveHello);
```

When your server is running at `http://localhost:8080`, each endpoint becomes:
- `http://localhost:8080/`
- `http://localhost:8080/api/hello`

{% hint style="info" %}
It is a best practice to prefix data endpoints with `/api/` to distinguish them from endpoints that serve HTML and other static content.
{% endhint %}

### The Controller Signature

Controllers are invoked when the matching request arrives at the server. They are responsible for interpreting the incoming request and sending a response. 

To do this job, every controller receives three arguments called `req`, `res`, and `next`:

```js
const serveHTML = (req, res, next) => {
  res.send('<h1>Welcome to the API</h1>');
};

const serveHello = (req, res, next) => {
  res.send({ message: 'hello!' });
};

app.get('/', serveHTML);
app.get('/api/hello', serveHello);
```

* **`req`** — the incoming request. Contains everything about what the client sent.
* **`res`** — methods for building and sending the response.
* **`next`** — a function that passes the request to the next handler if multiple handlers are used to process the request. Controllers typically don't use this — but middleware does. More on that in the next lesson.

{% hint style="info" %}
Write controllers as named functions rather than inline arrow functions. Named controllers are easier to read, test, and reuse across multiple endpoints.
{% endhint %}

Let's start by looking at sending responses with `res`. Then, we'll look at the `req` object. In the next lesson, we'll look closely at the `next` function.

### Sending Responses

The most common way to send a response is with `res.send()`. With `node:http`, we would have to use `res.writeHead()` to set the status and specify the content type and `res.end()` to send data.

In express, `res.send()` always sends a 200 status, automatically sets the content type, and sends the given data:

```js
const serveHTML = (req, res, next) => {
  // status 200, content type: 'text/html'
  res.send('<h1>Welcome to the API</h1>');
};

const serveHello = (req, res, next) => {
  // status 200, content type: 'application/json'
  res.send({ message: 'hello!' });
};

app.get('/', serveHTML);
app.get('/api/hello', serveHello);
```

Test this in your terminal with `curl -i`

```
curl http://localhost:8080/ -i
curl http://localhost:8080/api/hello -i
```

---

**Endpoint and Controller Challenge**: Add an endpoint to your server for `GET http://localhost:8080/api/users` requests that sends the `users` array below as a response:

```js
const users = [
  { id: 1, name: 'Carmen' },
  { id: 2, name: 'Maya' },
  { id: 3, name: 'Reuben' },
];
```

**<details><summary>Solution</summary>**

```js
const users = [
  { id: 1, name: 'Carmen' },
  { id: 2, name: 'Maya' },
  { id: 3, name: 'Reuben' },
];

const serveUsers = (req, res) => {
  res.send(users);
};

app.get('/api/users', serveUsers);
```

</details>

### Specifying Status

When we want to specify the status code, we have two main options.

**`res.sendStatus(code)`** sends a status code with no body. This is useful for simple health check endpoints:

```js
const handlePing = (req, res) => {
  res.sendStatus(200);
};

app.get('/api/ping', handlePing);
```

{% hint style="info" %}
A "health check" endpoint can be used to quickly check to see if a server is running
{% endhint %}

**`res.status().send()`** sets the status code before sending the body. We can use this to write a "catch-all" fallback endpoint that sends a 404 when a request doesn't match any defined endpoints

```js
// `app.use()` captures ALL requests, not just get requests
// Must come after all other routes
app.use((req, res) => {
  res.status(404).send({ error: `Not found: ${req.originalUrl}` });
});
```

Any request that doesn't match our defined endpoints will land here and get a clean JSON error.

{% hint style="info" %}
In the next lesson, once you add a full frontend, this catch-all will change. Instead of returning a JSON `404`, it will serve `index.html` — so the browser's client-side router can handle unknown routes rather than the server.
{% endhint %}

## The Request Object

The `req` parameter is an object containing information about the incoming request. It has a few key properties:
* `req.query`: captures query strings—the `key=value` pairs after the `?` in a URL
* `req.params`: captures route parameters—named segments of the URL defined in the route path

### Query Strings

Query strings are the parts of a URL that come after a `?` and come in `key=value` pairs. They are often used by clients to filter or modify the data they are requesting.

In these examples, can you tell what the query strings are doing to the results?
* [https://www.google.com/search?q=express+js](https://www.google.com/search?q=express+js)
* [https://www.youtube.com/results?search_query=http](https://www.youtube.com/results?search_query=http)
* [https://www.nike.com/w/?sortBy=priceDesc](https://www.nike.com/w/?sortBy=priceDesc)

In our server, let's make it so that our `/api/hello` endpoint can send accept query strings for a `first` and `last` name and send a custom message in response.

Express parses query strings automatically and makes them available in the `req.query` object

```js
// GET /api/hello?first=ada&last=lovelace
const serveHello = (req, res) => {
  console.log(req.query); // { first: 'ada', last: 'lovelace' }

  const { first, last } = req.query;
  if (!first || !last) {
    return res.send({ message: 'hello, stranger' });
  }
  res.send({ message: `hello, ${first} ${last}` });
};

app.get('/api/hello', serveHello);
```

Test it with `curl`!

```sh
curl http://localhost/api/hello?first=ada&last=lovelace
```

With a `node:http` server, reading query strings required manually constructing a `URL` object. 

--- 

**Query String Challenge**: Modify `serveUsers` so that when a `contains` query string is provided, only users whose name matches are returned.

For example: `/api/users?contains=a` would return Carmen and Maya

<details><summary>Solution</summary>

```js
const serveUsers = (req, res) => {
  const { contains } = req.query;
  if (!contains) return res.send(users);

  const filtered = users.filter((user) => user.name.includes(contains));
  res.send(filtered);
};
```

</details>

### Route Parameters

If you completed the endpoints and controllers challenge, you will have a `/api/users` endpoint that sends the entire `users` array:

```js
const users = [
  { id: 1, name: 'Carmen' },
  { id: 2, name: 'Maya' },
  { id: 3, name: 'Reuben' },
];

const listUsers = (req, res) => {
  res.send(users);
};

app.get('/api/users', listUsers);
```

Suppose our client knows the `id` of the user they want to see and just wants that user's data. We can set up an endpoint to handle requests like `GET /api/users/3` where the value `3` indicates the id of the user they want.

On the server, we can define an endpoint with a **route parameter** using the syntax `:id`, like so:

```js
// :id is called a "route parameter"
app.get('/api/users/:id', findUser);
```

The `findUser` controller can access the provided `id` from the `req.params` object:

```javascript
const findUser = (req, res) => {
  // Make sure the property name matches the route parameter below
  const { id } = req.params;

  // Keep in mind, route parameters are stored as strings.
  const user = users.find(user => user.id === Number(id));

  res.send(user);
};

// req.params.id will hold the value of the requested id
app.get('/api/users/:id', findUser);
```

**Route Parameters Challenge:** It is possible that the client requests a user that doesn't exist. Add a guard clause to the `findUser` controller that sends a response with the body `{ message: No user with the given id }` and a 404 status.


**<details><summary>Solution</summary>**
```js
if (!user) {
  // 404 means "Resource Not Found"
  return res.status(404).send({
    message: `No user with the id ${id}`
  });
}
```
</details>

## What's Next: Middleware

**Middleware** is a function that runs for every incoming request before a controller sends a response. It uses `next()` to pass the request along the chain, and `app.use()` to register it globally:

```js
// A middleware that logs every incoming request
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next(); // pass to the next handler
};

app.use(logRoutes); // runs before every controller
```

Middleware is how Express handles serving static files, parsing request bodies, protecting routes, and much more. Next lesson you'll learn exactly how it works — and use it to serve an entire frontend application with a single line of code.
