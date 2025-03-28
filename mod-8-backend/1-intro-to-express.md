# Intro to Express

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-0-0-intro-to-express)!
{% endhint %}

In this first lesson, we're going to learn the basics of Express and build and deploy a simple server application.

**Table of Contents:**

- [Terms](#terms)
- [What is an HTTP Server Application?](#what-is-an-http-server-application)
  - [Client Server Interactions](#client-server-interactions)
- [Express](#express)
  - [The `app` object is the hub of the server application](#the-app-object-is-the-hub-of-the-server-application)
- [Endpoints and Controllers](#endpoints-and-controllers)
  - [Endpoints](#endpoints)
  - [Controllers](#controllers)
  - [Query Parameters](#query-parameters)
  - [Using `req.query`](#using-reqquery)
  - [Query Parameter Challenge](#query-parameter-challenge)
- [Listening: Host and Ports](#listening-host-and-ports)

## Terms

* **Server / Client** — a server is a computer that provides a service to another computer and its user, called the client.
* **HTTP Server Application** — a computer program that enables a computer to listen for and respond to HTTP requests.
* **Express** — the most popular Node framework for building HTTP server applications
* **Host** and **Port** - the address of a server application
* **Endpoint** — a specific URL path of a server that clients can "hit" (send requests to) to create/read/update/delete data. For example: `/api/data` or `/api/users/:id`
* **Express `app`** — an object generated by Express that configures the http server application. 
* **Controller** — a callback function invoked asynchronously when the associated endpoint is targeted. It parses the request and sends back a response.
* **Query parameters** — a portion of a URL used to filter and sort the requested data. 
  * Query Parameters are appended to the end of a URL with a `?` and separated with `&`
  * e.g. `/api/clothing/search?category=tops&sort=ascending`

## What is an HTTP Server Application?

A **server** is a computer that provides a service to another computer and its user, called the **client**. Every website that you visit and every web API that you've used is powered by a server and your computer is the client! 

To handle incoming requests, web servers run a **HTTP server application** — a computer program that enables the computer to listen for and respond to HTTP requests coming from clients.

Today, we'll learn about **Express**, the most popular Node framework for building HTTP server applications.

### Client Server Interactions

So how do the client and server interact?

1. A client sends a **request** to the server
2. The server receives the request and the HTTP server application **routes** it to the proper **controller**
3. The controller parses the request and sends a **response**
4. The client receives the response and does something using the data!

![express diagram](img/express-diagram-simple.svg)

<details>

<summary><strong>Q: What are the responsibilities of a client application?</strong></summary>

* Rendering HTML, CSS, and JS
* Request information from a server (get requests)
* Providing information to a server (post/patch/delete requests)
* Reading data received from a server

</details>

<details>

<summary><strong>Q: What are the responsibilities of a server application?</strong></summary>

* Serving static files that live on the server (HTML, CSS, and JS files)
* Fetching and serving data from a third-party API that requires an API key
* Managing and serving data from the server's own database

</details>

## Express

To build our HTTP server application, we will use Express. Express is the most popular Node framework for building HTTP server applications.

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

### The `app` object is the hub of the server application

This `app` object lets us configure our server's behavior. It:

* Defines the **endpoints** and **controllers** for the HTTP server application.
* Defines where the server will "listen" for incoming requests.

Here is a simple example. For now, just focus on the high-level structure of the application. Look for **controllers**, **endpoints**, and where the app "listens".

```js
const express = require('express');
const app = express();

// controllers
const serveHello = (req, res, next) => {
  res.send('hello');
}
const serveData = (req, res, next) => {
  const data = [{ name: 'ben' }, { name: 'zo' }, { name: 'carmen' }];
  res.send(data);
}
const serveStatus = (req, res, next) => {
  res.sendStatus(200);
}

// endpoints
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);
app.get('/api/ping', serveStatus);

// listen
const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

* **Endpoints** are unique URL paths that a server makes available for clients to send requests to. 
* **Controllers** are callback functions invoked asynchronously when the associated endpoint is targeted. They parse requests and send back a response.
* `app.listen(port, callback)` defines the port of the current host (`localhost` when running locally) where incoming requests will be accessible.
  * In this example, the server is accessible at `http://localhost:8080` during development.

## Endpoints and Controllers

Let's look closer at how to make endpoints and controllers.

### Endpoints

Endpoints in Express are created using the `app.get(endpoint, controller)` method. The endpoint typically begins with `/api`:

```js
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);
```

All endpoints are appended to the host and port.
* When the server is listening at `http://localhost:8080`, the `/api/hello` endpoint is accessible at `http://localhost:8080/api/hello`

<details>

<summary><strong>Q: What does <code>.get</code> mean? Why is it called that?</strong></summary>

Each endpoint accepts requests for specific HTTP methods like GET, POST, PUT, DELETE, etc... 

These endpoints are designed to handle GET requests. If we wanted to assign controllers for endpoints that handle POST/PATCH/DELETE requests, we could use `app.post` or `app.patch` or `app.delete`.

</details>

<details>

<summary><strong>Q: Why do you the endpoints begin with <code>/api</code>?</strong></summary>

It is common for endpoints that serve data to begin with `/api` to help distinguish API endpoints from the parts of the server that serve static assets.

</details>

### Controllers

A **Controller** is a callback function invoked asynchronously when the associated endpoint is targeted. It parses the request and sends back a response.

Every controller is invoked with three values (`req`, `res`, and `next`) though all three aren't always used by the controller:

```js
const serveHello = (req, res, next) => {
  res.send('hello');
}
const serveData = (req, res, next) => {
  const data = [{ name: 'ben' }, { name: 'zo' }, { name: 'carmen' }];
  res.send(data);
}
const serveStatus = (req, res, next) => {
  res.sendStatus(200);
}

app.get('/api/hello', serveHello);
app.get('/api/data', serveData);
app.get('/api/ping', serveStatus);
```

The `res` parameter is an object with methods for configuring the response we want to send to the client. 
* The `res.send` method allows us to send data to the client. 
* The `res.sendStatus` method lets us send just a status code without data.

These controllers are quite simple as they just send back a pre-defined response, but controllers can do quite complex tasks!

The `next` parameter will be discussed in the next lesson, but what about the `req` parameter?

### Query Parameters

The `req` parameter is an object containing information about the incoming request. One way we can make use of the request object `req` is by accepting query parameters.

**Query parameters** are added to the end of request URL to tell the server to modify the requested data in some way. They are often used for search queries or to filter results. Google adds a `q` query parameter when you provide a search query:

[https://www.google.com/search?q=express+node](https://www.google.com/search?q=express%20node)

Query parameters are added in `key=value` pairs to the end of a URL following a `?`. In the example above `q` is the name of the query parameter with the value `express+node` (the `+` is just used to indicate a space).

When multiple query parameters are needed, they are each separated by an ampersand `&`. For example, if we wanted to include a `first` and `last` name to the `/api/hello` endpoint, we could write: 

[localhost:8080/api/hello?first=ben&last=spector](localhost:8080/api/hello?first=ben&last=spector)

### Using `req.query`

In the controller code, each `key=value` query parameter will show up in the `req.query` object with a string value. 

So, we can modify our `serveHello` controller like so:

```js
const serveHello = (req, res, next) => {
  console.log(req.query); // { first: "ben", last: "spector" }

  const { first, last} = req.query;
  if (!first || !last) {
    return res.send(`hello stranger!`);
  }
  res.send(`hello ${first} ${last}!`);
}

app.get('/api/hello', serveHello);
```

* In this example, we get the `first` and `last` values by destructuring the `req.query` object.
* Before sending a response, we make sure to check that both query parameters are provided, otherwise we just send `'hello stranger!'`.


### Query Parameter Challenge

How can we modify the `serveData` function so that it filters the array of objects by the `.name` property using a `?filter=value` query parameter?

<details>

<summary><strong>Solution</strong></summary>

```js
const serveData = (req, res, next) => {
  const filter = req.query.filter
  const data = [{ name: 'ben' }, { name: 'zo' }, { name: 'carmen' }];

  if (!filter) res.send(data);
  const filteredData = data.filter((item) => item.name === filter);
  res.send(filteredData);
}
```

</details>

## Listening: Host and Ports

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
