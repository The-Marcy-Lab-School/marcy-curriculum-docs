# Cheat Sheet Backend

- [Express](#express)
  - [Express Basics](#express-basics)
  - [Log Routes and Serve Static Middleware](#log-routes-and-serve-static-middleware)
  - [Express API as a Middleman](#express-api-as-a-middleman)
  - [Sending frontend to backend requests (Vite Config)](#sending-frontend-to-backend-requests-vite-config)
  - [Handling POST requests with a JSON body](#handling-post-requests-with-a-json-body)

![](./img/cheatsheet/client-server-database.svg)

## Express

![](./img/cheatsheet/express-middleware-model.svg)

### Express Basics

* **Server Application** — an application that listens for requests and sends responses.
* **Host** and **Port** - the address of a server application
* **Endpoint** — a specific URL path of a server that clients can "hit" (send requests to) to create/read/update/delete data. For example: `/api/data` or `/api/users/:id`
* **Express `app`** — an object that "listens" for requests and "routes" to the appropriate controller.
* **Controller** — a callback function that parses a request and sends a response for a particular endpoint
* **Query parameters** — a portion of a URL used to filter and sort the requested data. They are appended to the end of a URL using the syntax `?queryParam=value`.

```js
const express = require('express');
const app = express();

const serveHello = (req, res, next) => {
  const name = req.query.name || "stranger";
  res.send(`hello ${name}`);
}
app.get('/api/hello', serveHello);

const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

### Log Routes and Serve Static Middleware

- **Middleware** - a function in express that intercepts and processes incoming HTTP requests. It can perform server-side actions such as parsing the request, modifying the response, or executing additional logic before passing control to the next middleware in the chain."
- **`path` module** - a module for creating absolute paths to static assets
- **Environment Variable** - a variable defined outside of the JavaScript execution context.
- **`__dirname`** — an environment variable that returns the path to the parent directory of the current file.
- **Static Assets** - unchanging files delivered to the client exactly as they are stored on a server. These include HTML, CSS, JavaScript files, images, videos, fonts, and documents. For React projects, we need to "build" our project to generate static assets (convert `.jsx` files to `.js` files).

```js
const path = require('path');

// Express Middleware for serving static assets
const serveStatic = express.static(path.join(__dirname, '../../path/to/frontend/dist'));

// Custom Middleware function for logging route requests
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next(); // Passes the request to the next middleware/controller
};

// Register the middleware for all incoming requests
app.use(serveStatic);
app.use(logRoutes);
```

### Express API as a Middleman

* **API Key** - a secret code that verifies your identity as a developer using an API's limited resources. Do not share these!
* **Environment Variable** — a hidden variable stored on the host's machine (your laptop or Render.com) and accessible in Node through the `process.env` object
* **`.env` file** - a file to store hidden variables like API keys. Ignored by GitHub and uploaded to Render for deployment.
* **`dotenv` module** - an npm package for importing `.env` files


If we want to avoid making our API keys public, we need to be careful with how we use them in our code. **Our frontend can't safely make requests using the API key** — anyone using our deployed application can just look at the Network tab to see the API keys in the request URL.

**So, we have to make the requests using the API keys in our backend.** 

![](./img/cheatsheet/express-api-middleman.svg)

### Sending frontend to backend requests (Vite Config)

* **Cross-origin requests** - HTTP requests made from one server to another.
* **Same-origin requests** - HTTP requests made from a server to itself.
* **Request Proxy In Development** — faking the origin of the request in a frontend development server to match the origin of the backend server

When frontend code is served by a backend server, and the frontend wants to send a request to the same backend server, the `fetch` URL should omit the host and port. The frontend will send the request to the same origin.
```js
// React Fetch
const response = await fetch('/api/gifs');
```

However, the frontend development server provided by Vite runs on `localhost:5173` while the backend server you build likely runs on a different port (`localhost:8080` or something). Since these are *different* origins, we need to re-direct same-origin requests from port 5173 to port 8080 by modifying the `vite.config.js` of our frontend project.

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

const SERVER_PORT = 8080;

// https://vitejs.dev/config/
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

### Handling POST requests with a JSON body

```js
// Express Middleware for parsing incoming POST/PATCH requests with JSON bodies
const parseJSON = express.json();
app.use(parseJSON);

const createFellow = (req, res) => {
  // We expect the request body to be an object: `{ fellowName: 'name' }`
  const { fellowName } = req.body; 

  // The Fellow model will handle creating the fellow
  const newFellow = Fellow.create(fellowName);
  res.send(newFellow);
};

app.post('/api/fellows', createFellow)
```
