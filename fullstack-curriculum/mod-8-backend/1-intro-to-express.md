# Intro to Express

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-0-0-intro-to-express)!
{% endhint %}

In this first lesson, we're going to learn the basics of Express and build and deploy a simple server application.

**Table of Contents:**

* [Terms](1-intro-to-express.md#terms)
* [Client Server Interactions](1-intro-to-express.md#client-server-interactions)
* [Express](1-intro-to-express.md#express)
* [The `app` object is the hub of the server application](1-intro-to-express.md#the-app-object-is-the-hub-of-the-server-application)
* [Endpoints](1-intro-to-express.md#endpoints)
* [Controllers](1-intro-to-express.md#controllers)
* [Query Parameters](1-intro-to-express.md#query-parameters)
  * [Accessing Query Parameter Values in a Controller](1-intro-to-express.md#accessing-query-parameter-values-in-a-controller)
  * [Challenge](1-intro-to-express.md#challenge)
* [Listening: Host & Ports](1-intro-to-express.md#listening-host--ports)

## Terms

* **Server Application** — an application that listens for requests and sends responses.
* **Host** and **Port** - the address of a server application
* **Endpoint** — a specific URL path of a server that clients can "hit" (send requests to) to create/read/update/delete data. For example: `/api/data` or `/api/users/:id`
* **Express `app`** — an object that "listens" for requests and "routes" to the appropriate controller.
* **Controller** — a callback function that parses a request and sends a response for a particular endpoint
* **Query parameters** — a portion of a URL used to filter and sort the requested data. They are appended to the end of a URL using the syntax `?queryParam=value`.

## Client Server Interactions

So how do the client and server interact?

1. A client sends a **request** to the server
2. The server receives the request and **routes** it to the proper **controller**
3. The controller parses the request and sends a **response**
4. The client receives the response and renders the data!

![express diagram](img/express-diagram-simple.svg)

<details>

<summary><strong>Q: What are the responsibilities of a client?</strong></summary>

* Rendering HTML, CSS, and JS
* Request information from a server (get requests)
* Providing information to a server (post/patch/delete requests)
* Reading data received from a server

</details>

<details>

<summary><strong>Q: What are the responsibilities of a server?</strong></summary>

* Serving static files that live on the server (HTML, CSS, and JS files)
* Fetching and serving data from a third-party API that requires an API key
* Managing and serving data from the server's own database

</details>

## Express

To build our server application, we will use Express. According to their [docs](https://expressjs.com/):

> Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.

To use express, we start by installing it

```sh
npm i express
```

The main export of Express is the `express` function which creates an object, often named `app`.

```js
// index.js
const express = require('express');
const app = express();
```

## The `app` object is the hub of the server application

This `app` object lets us define our server's behavior. It:

* **Listens** — it waits for incoming requests and...
* **Routes** — it directs each request to a **controller** based on the specific **endpoint** of the request

Here is a simple example. For now, just focus on the high-level structure of the application. Look for **controllers**, **endpoints**, and where the app "listens".

```js
const express = require('express');
const app = express();

// controllers
const serveIndex = (req, res, next) => {
  res.sendFile(__dirname + '/index.html');
}
const serveAbout = (req, res, next) => {
  res.send('<h1>About</h1>');
}
const serveHello = (req, res, next) => {
  res.send('hello');
}
const serveData = (req, res, next) => {
  const data = [{ name: 'ben' }, { name: 'zo' }, { name: 'carmen' }];
  res.send(data);
}

// endpoints
app.get('/', serveIndex);
app.get('/about', serveAbout);
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);

// listen
const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

* `const app = express()` creates the Express `app` object
* A **controller** is a callback function that parses a request and sends a response. It will be invoked by the `app` when the associated path is hit.
* `app.get(endpoint, controller)` defines which `controller` will be invoked for the specified `endpoint`.
* `app.listen(port, callback)` "starts" the server application. Since the application runs locally, it will be accessible at `http://localhost:8080`, where `8080` is the port. All of the endpoints above are extensions of this host and port.

Let's look closer at how to make a controller.

## Endpoints

An **endpoint** is a unique URL path that a server makes available for clients to send requests to using different methods like GET, POST, PUT, DELETE, etc... Examples include `/api/data` or `/api/users/:id`, where `:id` represents a dynamic path parameter.

```js
app.get('/', serveIndex);
app.get('/about', serveAbout);
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);
```

* The associated endpoints for each controller begin with `/` and are appended to the `host:port`
  * For example, sending a GET request to `http://localhost:8080/about` will invoke the `serveAbout` controller

<details>

<summary><strong>Q: What does <code>.get</code> mean? Why is it called that?</strong></summary>

These endpoints are designed to handle GET requests. If we wanted to assign controllers for endpoints that handle POST/PATCH/DELETE requests, we could use `app.post` or `app.patch` or `app.delete`.

</details>

<details>

<summary><strong>Q: Why do you think the <code>serveHello</code> and <code>serveData</code> endpoints begin with <code>/api</code> while the other two endpoints do not?</strong></summary>

Typically, endpoints that serve data will begin with `/api`, while endpoints that serve HTML do not.

</details>

### Controllers

**Controllers** in Express.js are JavaScript functions responsible for handling the logic associated with specific endpoints. When a client sends a request to a particular endpoint, Express invokes the corresponding controller to process the request and generate a response.

```js
const serveIndex = (req, res, next) => {
  res.sendFile(__dirname + '/index.html');
}
const serveAbout = (req, res, next) => {
  res.send('<h1>About</h1>');
}
const serveHello = (req, res, next) => {
  res.send('hello');
}
const serveData = (req, res, next) => {
  const data = [{ name: 'ben' }, { name: 'zo' }, { name: 'carmen' }];
  res.send(data);
}

app.get('/', serveIndex);
app.get('/about', serveAbout);
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);
```

* To keep things simple, these controllers only use the `res` object.
* The `res.send` and `res.sendFile` methods allow us to send different kinds of data. `res.sendStatus` lets us send just a status code without data.
* When sending files, the `__dirname` keyword returns the absolute path to the current file folder.

**Key points about controllers:**

* Invocation: Controllers are invoked by Express.js when a matching route (endpoint) is requested by a client.
* Responsibilities: Controllers handle various tasks, including interacting with databases, processing data, and generating responses based on the request.
* Function Parameters: Controllers typically receive three parameters:
  * `req`: An object containing information about the incoming request (the request method, URL, headers, query parameters, and body, etc...)
  * `res`: An object with functions for sending a response to the client (`res.send()`, `res.sendStatus()` etc...)
  * `next`: A function that passes control to the next middleware function in the request-response cycle. It is commonly used in middleware to delegate processing to subsequent functions.

### Query Parameters

Our controllers feel stiff. Let's make them more dynamic using query parameters!

Right now, if I request http://localhost:8080/api/hello, the `serveHello` controller is triggered which sends back the string `'hello'`. And it will _always_ send the string `'hello'`

```js
const serveHello = (req, res, next) => {
  res.send('hello');
}
```

**Query parameters** are added to the end of request URL to tell the server to modify the requested data in some way.

They are appended to the end of a request URL starting with a `?` and followed by `key=value` pairs separated by `&`s:

http://host/api/endpoint?param1=value\&param2=value

<details>

<summary><strong>Q: In the example above where we send the request to <code>http://localhost:8080/api/hello</code>, how would we add query parameters to specify a first and last name that we want the server to say hello to?</strong></summary>

If a client wants to send a request to the `/api/hello` endpoint and add query parameters for a first and last name, the full URL could be:

http://localhost:8080/api/hello?first=jane\&last=doe.

In this example, there are two parameters named `first` and `last` with the values `jane` and `doe`.

</details>

#### Accessing Query Parameter Values in a Controller

In the server code, we can access the value of query parameters using `req.query` object.

Each `key=value` query parameter provided in the request URL will show up as a property on the `req.query` object:

```js
const serveHello = (req, res, next) => {
  const first = req.query.first;
  const last = req.query.last;
  if (first && !last) {
    return res.send(`hello ${first} ${last}!`);
  }
  res.send('hello')
}

app.get('/api/hello', serveHello);
```

* In this example, we get the `first` value using `req.query.first` and the `last` value using `req.query.last`
* Before sending a response, we make sure to check that both query parameters are provided, otherwise we just send `'hello'`.
* Now,
  * http://localhost:8080/api/hello?first=ben\&last=spector will send back `hello ben spector`
  * http://localhost:8080/api/hello?first=ben will send back `hello`
  * http://localhost:8080/api/hello will send back `hello`

#### Challenge

How can we modify the `serveData` function so that it filters the array of objects by the `.name` property using a `?filter=value` query parameter?

<details>

<summary><strong>Solution</strong></summary>

```js
const serveData = (req, res, next) => {
  // if no filter is provided, req.query.filter will be undefined. use "" as a backup value
  const filterTerm = req.query.filter || "";
  // filter the gifs.data array using the title (see the gifs.json file) 
  const filteredData = gifs.data.filter((gif) => gif.title.toLowerCase().includes(filterTerm));
  // send back the filteredData
  res.send(filteredData);
}
```

</details>

### Listening: Host & Ports

The last lines of code "turn on" the server. That is, they make the server start listening for requests.

```js
const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

* The first argument defines the **port** number
* The second argument is a callback that gets executed when the server starts listening. It is often used to print out the host and port.

![host port](img/host-port.png)

The **host** is like our home address.

* `localhost` is a hostname that refers to the current device used to access it.
* `localhost` is an alias for `127.0.0.1` which is the standard address used.
* `localhost === 127.0.0.1`

Ports are the "front doors" of our application. (There are a lot of doors!)

* `:8080` is considered as a different "door" from `:5500`

Which port should you use? It doesn't really matter, but here are some ones that our instructors like to use and some standards that are used:

* `8080` (What I use)
* `4321` (Mike's favorite because it is fun)
* `3000` (What other people use)
* `5500` (What other other people use)
* `80` (Standard unencrypted HTTP port)
* `443` (Standard encrypted HTTPS port)

Just pick one that isn't being used!

> How do you know which ones aren't being used? Your computer will likely tell you if one is currently in use — just use a different one (or kill the process that is currently using that port).
