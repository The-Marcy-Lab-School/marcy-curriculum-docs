# Cheat Sheet — Servers

- [How the Internet Works](#how-the-internet-works)
  - [Internet Basics](#internet-basics)
  - [HTTP: The Request-Response Cycle](#http-the-request-response-cycle)
  - [HTTP Status Codes](#http-status-codes)
- [Server Basics](#server-basics)
  - [Host, Port, and Localhost](#host-port-and-localhost)
  - [A `node:http` Server](#a-nodehttp-server)
- [Express](#express)
  - [Express Basics](#express-basics)
  - [Middleware](#middleware)
  - [Serving Static Assets](#serving-static-assets)
  - [Environment Variables](#environment-variables)
  - [Sending Frontend-to-Backend Requests (Vite Proxy)](#sending-frontend-to-backend-requests-vite-proxy)
- [RESTful CRUD API](#restful-crud-api)
  - [REST Principles](#rest-principles)
  - [CRUD and HTTP Methods](#crud-and-http-methods)
  - [Route Parameters](#route-parameters)
  - [Handling POST and PATCH Request Bodies](#handling-post-and-patch-request-bodies)
- [Model-View-Controller (MVC)](#model-view-controller-mvc)
  - [MVC Architecture](#mvc-architecture)
  - [Model Example](#model-example)

![](./img/cheatsheet/client-server-database.svg)

## How the Internet Works

### Internet Basics

* **Internet** — a global network of interconnected computers that communicate using standardized protocols.
* **Protocol** — an agreed-upon set of rules for how data should be formatted, transmitted, and received.
* **HTTP (HyperText Transfer Protocol)** — the protocol that governs how clients and servers communicate on the web.
* **Client** — any device or program that sends requests to a server (a browser, a mobile app, a `fetch()` call).
* **Server** — a computer or program that receives requests and sends back responses.
* **IP Address** — a unique numerical label assigned to each device on a network (e.g., `142.250.80.46`).
* **Domain Name** — a human-readable name (e.g., `google.com`) that maps to an IP address.
* **DNS (Domain Name System)** — the system that translates domain names into IP addresses.
* **Packet** — a small unit of data. Long messages are broken into packets, routed independently, and reassembled at the destination.

### HTTP: The Request-Response Cycle

Every HTTP interaction follows the same four-step pattern:

```
CLIENT                          SERVER
  |                               |
  |  ── HTTP Request ──────────→  |
  |    Method: GET                |
  |    URL: /api/posts            |
  |    Headers: {...}             |
  |                               |
  |  ←──── HTTP Response ──────── |
  |    Status: 200 OK             |
  |    Headers: {...}             |
  |    Body: [{...}, {...}]       |
```

An HTTP **request** contains:
* **Method** — the type of action (`GET`, `POST`, `PATCH`, `DELETE`)
* **URL** — the address of the resource
* **Headers** — metadata (e.g., `Content-Type: application/json`)
* **Body** *(optional)* — data sent with `POST` and `PATCH` requests

An HTTP **response** contains:
* **Status Code** — a number indicating the result
* **Headers** — metadata about the response
* **Body** *(optional)* — the data being sent back (JSON, HTML, etc.)

### HTTP Status Codes

| Code  | Name                  | When you'll use it                              |
| ----- | --------------------- | ----------------------------------------------- |
| `200` | OK                    | Successful `GET`, `PATCH`, or `DELETE`          |
| `201` | Created               | Successful `POST` (resource was created)        |
| `204` | No Content            | Success but no data to return                   |
| `400` | Bad Request           | Client sent invalid or malformed data           |
| `401` | Unauthorized          | Authentication required (not logged in)         |
| `403` | Forbidden             | Authenticated but not permitted                 |
| `404` | Not Found             | The requested resource doesn't exist            |
| `500` | Internal Server Error | Something went wrong on the server              |
| `503` | Service Unavailable   | A dependency (e.g., third-party API) is down    |

## Server Basics

### Host, Port, and Localhost

* **Listening** — a server is "listening" when it is actively waiting for incoming requests on a specified port.
* **Host** — the network address of the machine running the server.
* **Port** — a number identifying a specific process on a host. Different applications run on different ports.
* **`localhost`** — a hostname that always refers to your own computer (`127.0.0.1`). Used during development.

```
http://localhost:8080
         ↑         ↑
       host      port
```

Two applications cannot listen on the same port simultaneously.

### A `node:http` Server

Node's built-in `http` module lets you create a server without any npm packages:

```js
const http = require('node:http');

const server = http.createServer((req, res) => {
  console.log(req.method); // 'GET', 'POST', etc.
  console.log(req.url);    // '/api/todos'
  console.log(req.headers);

  res.writeHead(200, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ message: 'Hello World' }));
});

server.listen(8080, () => console.log('Listening on http://localhost:8080'));
```

* `http.createServer(callback)` — the callback runs for **every** incoming request.
* `req.method` / `req.url` / `req.headers` — inspect the incoming request.
* `res.writeHead(statusCode, headers)` — set status and headers.
* `res.end(body)` — send the response body (must be a string or Buffer).
* **Routing** with `node:http` requires manual `if/else` on `req.method` and `req.url`. Express handles this for you.

## Express

![](./img/cheatsheet/express-middleware-model.svg)

### Express Basics

* **Express `app`** — an object that listens for requests and routes them to the appropriate controller.
* **Endpoint** — a specific URL path clients can send requests to (e.g., `/api/users/:id`).
* **Controller** — a named callback function that parses a request and sends a response.
* **`req.query`** — an object containing query parameters (e.g., `?name=ben` → `{ name: 'ben' }`).

```js
const express = require('express');
const app = express();

const serveHello = (req, res, next) => {
  const { first, last } = req.query;
  if (!first || !last) return res.send('hello stranger!');
  res.send(`hello ${first} ${last}!`);
};

app.get('/api/hello', serveHello);

const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`));
```

### Middleware

* **Middleware** — a function that intercepts incoming requests before the final controller runs. It receives `(req, res, next)` and calls `next()` to pass the request forward.

```js
// Custom middleware — logs every request
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next(); // pass to the next middleware/controller
};

// Register middleware for ALL requests (before controllers)
app.use(logRoutes);
```

* Use `app.use(fn)` to run middleware on every request.
* Middleware must either call `next()` or send a response — otherwise the request hangs.
* Order matters: middleware registered before controllers runs first.

### Serving Static Assets

* **Static Assets** — files (HTML, CSS, JS, images) served unchanged. A React project's `dist/` folder contains static assets.
* **`path` module** — Node built-in for building reliable absolute file paths.
* **`__dirname`** — the absolute path of the current file's directory.

```js
const path = require('path');

// Generate middleware that serves every file in the dist folder
const serveStatic = express.static(path.join(__dirname, '../../path/to/frontend/dist'));

app.use(serveStatic); // register before API controllers
```

`express.static()` checks each request against the files in the folder. If a match is found it sends the file; otherwise it calls `next()`.

### Environment Variables

* **API Key** — a secret code that verifies your identity with an external API. Never expose in frontend code.
* **Environment Variable** — a variable stored outside your code, accessible in Node via `process.env`.
* **`.env` file** — stores secrets locally. Add to `.gitignore` — never commit it.
* **`dotenv`** — npm package that loads `.env` into `process.env`.

```sh
npm install dotenv
```

```
# .env
API_KEY=abc123
```

```js
// index.js
const dotenv = require('dotenv');
dotenv.config(); // load .env into process.env

const serveStories = async (req, res, next) => {
  const url = `https://api.example.com/stories?api-key=${process.env.API_KEY}`;
  try {
    const response = await fetch(url);
    const data = await response.json();
    res.send(data);
  } catch (error) {
    res.status(503).send(error);
  }
};

app.get('/api/stories', serveStories);
```

**Why use the server as a middleman?** Client-side code is visible in the browser's Network tab — API keys embedded in frontend requests are publicly exposed. The server keeps the API key hidden.

![](./img/cheatsheet/express-api-middleman.svg)

### Sending Frontend-to-Backend Requests (Vite Proxy)

* **Same-origin request** — a request made from a page to the same host and port that served it.
* **Cross-origin request** — a request made from one origin to a different origin.

When the frontend is served by the backend at `localhost:8080`, use relative URLs:

```js
// React adapter — same-origin, no host needed
const response = await fetch('/api/stories');
```

During development, Vite runs on `localhost:5173` (a different origin from your backend on `:8080`). Configure Vite to **proxy** `/api` requests to the backend:

```js
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

const SERVER_PORT = 8080;

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: `http://localhost:${SERVER_PORT}`,
        changeOrigin: true,
      },
    },
  },
});
```

## RESTful CRUD API

### REST Principles

**REST (Representational State Transfer)** is an architectural style for designing web APIs. A RESTful API follows these guidelines:

1. **Endpoints describe resources, not actions** — use plural nouns, not verbs:
   * `/api/fellows` ✓ vs. `/api/getFellows` ✗
2. **HTTP methods communicate the action**:
   * `GET` – Read  |  `POST` – Create  |  `PATCH`/`PUT` – Update  |  `DELETE` – Remove
3. **Use IDs to target individual resources**: `GET /api/fellows/3`
4. **Nest resources to indicate ownership**: `POST /api/fellows/3/posts`
5. **Use proper status codes**: `201` for created, `404` for not found, etc.

### CRUD and HTTP Methods

| CRUD   | HTTP Method | Endpoint            | Status on success |
| ------ | ----------- | ------------------- | ----------------- |
| Create | `POST`      | `/api/fellows`      | `201 Created`     |
| Read   | `GET`       | `/api/fellows`      | `200 OK`          |
| Read   | `GET`       | `/api/fellows/:id`  | `200 OK`          |
| Update | `PATCH`     | `/api/fellows/:id`  | `200 OK`          |
| Delete | `DELETE`    | `/api/fellows/:id`  | `204 No Content`  |

### Route Parameters

**Route parameters** are named placeholders in a URL, defined with `:`. Their values are available on `req.params`.

```js
// GET /api/fellows/3  →  req.params.id === '3'
const findFellow = (req, res) => {
  const { id } = req.params; // route parameters are strings
  const fellow = Fellow.find(Number(id));

  if (!fellow) return res.status(404).send({ message: `No fellow with id ${id}` });
  res.send(fellow);
};

app.get('/api/fellows/:id', findFellow);
```

```js
// PATCH /api/fellows/3
const updateFellow = (req, res) => {
  const { id } = req.params;
  const { fellowName } = req.body;
  if (!fellowName) return res.status(400).send({ message: 'Invalid Name' });

  const updated = Fellow.editName(Number(id), fellowName);
  if (!updated) return res.status(404).send({ message: `No fellow with id ${id}` });
  res.send(updated);
};

app.patch('/api/fellows/:id', updateFellow);
```

```js
// DELETE /api/fellows/3
const deleteFellow = (req, res) => {
  const { id } = req.params;
  const deleted = Fellow.delete(Number(id));
  if (!deleted) return res.status(404).send({ message: `No fellow with id ${id}` });
  res.sendStatus(204);
};

app.delete('/api/fellows/:id', deleteFellow);
```

### Handling POST and PATCH Request Bodies

Use the `express.json()` middleware to parse incoming JSON bodies into `req.body`:

```js
// Register before controllers that need req.body
app.use(express.json());

const createFellow = (req, res) => {
  const { fellowName } = req.body; // parsed from the JSON body

  if (!fellowName) return res.status(400).send({ message: 'Invalid Name' });

  const newFellow = Fellow.create(fellowName);
  res.status(201).send(newFellow);
};

app.post('/api/fellows', createFellow);
```

**Frontend fetch** — include `method`, `headers`, and a stringified `body`:

```js
// POST
const createFellow = async (fellowName) => {
  const options = {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ fellowName }),
  };
  const response = await fetch('/api/fellows', options);
  return response.json();
};

// PATCH
const updateFellowName = async (id, fellowName) => {
  const options = {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ fellowName }),
  };
  const response = await fetch(`/api/fellows/${id}`, options);
  return response.json();
};

// DELETE
const deleteFellow = async (id) => {
  await fetch(`/api/fellows/${id}`, { method: 'DELETE' });
};
```

## Model-View-Controller (MVC)

### MVC Architecture

**MVC** separates an application into three distinct layers:

* **Model** — manages the data. Provides methods (an interface) for creating, reading, updating, and deleting data. Does not know about `req` or `res`.
* **View** — renders the UI. In a PERN app, this is the React frontend.
* **Controller** — the middleman. Parses requests, calls model methods, and sends responses. Does not directly touch the data.

![](./img/cheatsheet/express-middleware-model.svg)

**Recommended server folder structure:**

```
server/
├── index.js                  ← app setup, middleware, route registration
├── controllers/
│   └── fellowControllers.js  ← parses req, calls model, sends res
└── models/
    └── Fellow.js             ← manages data, no req/res
```

### Model Example

A model uses a class with `static` methods. The data (the "database") lives inside the model — controllers never access it directly.

```js
// models/Fellow.js
const fellows = [
  { name: 'Carmen', id: 1 },
  { name: 'Reuben', id: 2 },
  { name: 'Maya', id: 3 },
];
let nextId = 4;

class Fellow {
  static create(name) {
    const newFellow = { name, id: nextId++ };
    fellows.push(newFellow);
    return newFellow;
  }

  static list() {
    return [...fellows];
  }

  static find(id) {
    return fellows.find((f) => f.id === id) || null;
  }

  static editName(id, newName) {
    const fellow = Fellow.find(id);
    if (!fellow) return null;
    fellow.name = newName;
    return fellow;
  }

  static delete(id) {
    const idx = fellows.findIndex((f) => f.id === id);
    if (idx < 0) return null;
    return fellows.splice(idx, 1)[0];
  }
}

module.exports = Fellow;
```

Controllers stay focused on HTTP concerns only:

```js
// controllers/fellowControllers.js
const Fellow = require('../models/Fellow');

const listFellows = (req, res) => {
  res.send(Fellow.list());
};

const createFellow = (req, res) => {
  const { fellowName } = req.body;
  if (!fellowName) return res.status(400).send({ message: 'Invalid Name' });
  res.status(201).send(Fellow.create(fellowName));
};

module.exports = { listFellows, createFellow };
```
