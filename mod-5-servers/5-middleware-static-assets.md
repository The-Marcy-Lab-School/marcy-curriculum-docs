# 2. Building a Static Web Server with Middleware

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-3-express-middleware)!
{% endhint %}

In the last lecture, we learned about the basics of Express: endpoints and controllers. Today, we'll learn about a new kind of controller that will expand our server's capabilities: middleware.

**Table of Contents:**

- [Terms](#terms)
- [Express Review](#express-review)
- [Middleware and `next()`](#middleware-and-next)
- [Static Web Servers](#static-web-servers)
  - [Serving Vite Static Assets](#serving-vite-static-assets)
  - [`express.static()` Middleware](#expressstatic-middleware)
  - [Best Practice — Build Vite Project](#best-practice--build-vite-project)
- [Summary](#summary)

## Terms

* **Middleware** - a function in express that intercepts and processes incoming HTTP requests. It can perform server-side actions such as parsing the request, modifying the response, or executing additional logic before passing control to the next middleware in the chain."
* **`path` module** - a module for creating absolute paths to static assets
* **`__dirname`** — an environment variable that returns the path to the parent directory of the current file.
* **Static Assets** - unchanging files delivered to the client exactly as they are stored on a server. These include HTML, CSS, JavaScript files, images, videos, fonts, and documents. Vite projects can be "built" to bundle and minify source files into an optimized `dist` folder — this is not strictly required for Vanilla JS (browsers support ES modules natively) but is recommended for performance. It is required for React projects.

## Express Review

Remember how the Express app works?

![Express Diagram](./img/4-intro-to-express/express-diagram-simple.svg)

1. A client sends a **request** to the server.
2. The server receives the request and **routes** it to the proper **controller** based on the specific **endpoint**.
3. The controller processes the request, interacts with any necessary data or services, and generates a **response**.
4. The server sends the response back to the client.
5. The client is now free to do what it likes with the response.

And here is how we can create a server with two endpoints: `/api/hello` and `/api/data`

```js
const express = require('express');
const app = express();

// When the endpoint is requested, controllers will send a response
const serveHello = (req, res, next) => {
  const name = req.query.name || "stranger"
  res.send(`hello ${name}`);
}
const serveData = (req, res, next) => {
  const data = [{ name: 'Carmen' }, { name: 'Maya' }, { name: 'Reuben' }];
  res.send(data)
}

// Define the method, endpoint URL, and controller
app.get('/api/hello', serveHello);
app.get('/api/data', serveData);

// Listen for requests on port 8080
const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

A **controller** is a callback function that parses a request and sends a response. It will be invoked asynchronously when the associated endpoint is sent a request.

Every controller is invoked with three values:

* A `req` object which holds data related to the request, including **query parameters**.
* A `res` object which has methods to send a response.
* A `next` function, typically only used by "Middleware".

Now its time to learn about that `next` method!

## Middleware and `next()`

When a server receives an HTTP request, it can do more than just send back a response. Often, the server will perform a number of server-side actions _before_ a response is sent.

For example, suppose that you wanted the server to keep track of every request that is sent to it by printing out some information like:

* the endpoint that was requested (`/api/hello` or `/api/data`, etc...)
* the request method (`GET` or `POST`, etc...)
* the time the request was received

<details>

<summary><strong>Q: Why would it be helpful to log information about every incoming request?</strong></summary>

Logging incoming HTTP requests can be incredibly helpful for debugging purposes.

Say you have 3 endpoints and one of them has a bug that causes the server to crash when an unexpected request is sent to it. If we print out every request that comes in to the server, we can simply look at the most recent request in the logs and know where to start debugging.

</details>

We can add this functionality to our `serveHello` controller, before we send the response, we can just add a few lines of code:

{% hint style="danger" %}
```js
const serveHello = (req, res, next) => {
  // print the current time and request information 
  const timeOfRequest = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${timeOfRequest}`);
  
  // then send the response
  const name = req.query.name || "stranger"
  res.send(`hello ${name}`);
}
```
{% endhint %}

However, now we also need to add this code to `serveData`. If we had more controllers, this would quickly become very repetitive.

Instead, we can use a **middleware**. Middleware in Express is a controller that can be invoked for all incoming requests before the final controller sends a response.

![Middleware in Express is a controller that can be invoked for all incoming requests before the final controller sends a response.](./img/5-middleware-static-assets/express-middleware.svg)

In many ways, middleware is like a controller. It receives the `req`, `res`, and `next` values. There are two key differences:

* We use `app.use` to register the middleware which means the function is invoked for ALL endpoints
* We use `next()` instead of `res.send()` (or other response methods).
  * `next()` invokes the next middleware / controller registered to handle the current request.

{% hint style="success" %}
```js
// Middleware function for logging route requests
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next(); // Passes the request to the next middleware/controller
};
// Register the logRoutes middleware globally to log all requests
app.use(logRoutes);

// Other endpoints and controllers
```
{% endhint %}

* We first create the `logRoutes` function to print out information about the request
* At the end, we invoke `next()`, passing along the request to one of our controllers to send a response.
* We register `logRoutes` using `app.use()` which causes it to be invoked for ALL endpoints.
* Order matters! Middleware should be defined before controllers to ensure that it is invoked before the response is sent to the client.

With this middleware, we do not need to add this logging logic to every controller. Instead, this middleware will automatically be invoked for every incoming request before the final controller sends a response.

{% hint style="info" %}
Sometimes, middleware can invoke `res.send()` if we want to interrupt the response cycle and send a response before it reaches the intended controller. **In this way, middleware behaves like a guard clause.** Most of the time, it won't send a response, but it can if needed.

Examples of this include:

* Static asset middleware like `express.static()` (which you'll learn about next!)
* Rate limiter middleware like [`express-rate-limit`](https://www.npmjs.com/package/express-rate-limit)
* Error handling middleware like [`errorhandler`](https://expressjs.com/en/resources/middleware/errorhandler.html)
{% endhint %}

<details>

<summary><strong>Q: So, if a user sends a request to <code>http://localhost:8080/api/hello</code>, which functions are invoked and in what order?</strong></summary>

First the `logRoutes` middleware is invoked. The `next()` function is called which passes the request to the next controller, `serveHello`.

</details>

<details>

<summary><strong>Q: What would happen if the <code>logRoutes</code> controller DID send a response to the client? What would happen if it didn't invoke <code>next()</code>?</strong></summary>

If `logRoutes` did invoke `res.send()`, the `serveHello` controller would NOT be invoked as a response has already been sent.

If we simply didn't invoke `next()`, our server would "hang" — the response would never be completed and the client would likely receive a timeout error because the request took too long.

</details>

Middleware can be custom-made like this `logRoutes`. However, we can also utilize some of the out-of-the-box middleware controllers provided by Express.

## Static Web Servers

When you visit a website, like [https://google.com](https://google.com), you are immediately presented with a rendered website. What's happening there?

![Google's Homepage](./img/5-middleware-static-assets/google-homepage.png)

Now, imagine that the website is just the "static assets" of a Vite project deployed on GitHub pages! But instead of using GitHub pages, Google has its own servers to store those files and serve them to visiting users.

We call these **static web servers** because they store **static assets** (HTML, CSS, and JS files) and then provide a server application that serves those assets when requested.

Let's look at how we can serve the static assets of a Vite project from our server.

{% hint style="info" %}
HTML, CSS, and JavaScript files are considered "static" because their content remains unchanged when being transferred from server to client.

APIs on the other hand serve dynamic content that changes depending on parameters of the request.
{% endhint %}

### Serving Vite Static Assets

When a user visits the server's homepage `/`, we want to send the `index.html` file.

Back in the server, we could add the following endpoint and controller:

```js
// The path module is useful for constructing relative filepaths
const path = require('path');

const serveIndexHTML = (req, res, next) => {
  // `path.join()` constructs an absolute file from the arguments
  // `__dirname` provides the absolute path of the current module's parent directory.
  const filepath = path.join(__dirname, '../vite-project/index.html');
  res.sendFile(filepath);
};

app.get('/', serveIndexHTML);
```

This code works does the job of serving the `index.html` file, but it needs access to `/src/main.js` and `/src/style.css`. So we need two more controllers:

{% hint style="danger" %}
```js
const serveIndexHTML = (req, res, next) => {
  const filepath = path.join(__dirname, '../vite-project/index.html');
  res.sendFile(filepath);
}

const serveJS = (req, res, next) => {
  const filepath = path.join(__dirname, '../vite-project/src/main.js');
  res.sendFile(filepath)
};

const serveCSS = (req, res, next) => {
  const filepath = path.join(__dirname, '../vite-project/src/style.css');
  res.sendFile(filepath)
};

app.get('/', serveIndexHTML);
app.get('/src/main.js', serveJS);
app.get('/src/style.css', serveCSS);
```
{% endhint %}

Seems like a lot of code, right? Now, imagine that your application has hundreds of static assets! You would need an endpoint and controller for every file you'd want to serve.

### `express.static()` Middleware

Rather than defining endpoints for every single static asset that you wish to serve, we can use the `express.static()` middleware generator included with Express.

`express.static()` is not middleware itself. Instead, invoking this function will generate a middleware function that we can use. We just need to provide a filepath to the folder containing our static assets:

{% hint style="success" %}
```js
// The path module is useful for constructing relative filepaths
const path = require('path');

// the filepath is to the entire folder
const filepath = path.join(__dirname, '../vite-project');

// generate middleware using the filepath
const serveStatic = express.static(filepath);

// Register the serveStatic middleware before the remaining controllers
app.use(serveStatic);

// other controllers 
```
{% endhint %}

Explanation:

* Now, we just make a filepath to the entire dist folder and pass the filepath to `express.static()` which returns a middleware function which we call `serveStatic`
* `app.use(serveStatic)` will checks all incoming requests to see if they match files in the provided folder. if they do, they will be sent to the client
* Order matters! Remember to add this before the rest of your controllers.

Like `logRoutes`, this middleware intercepts incoming requests before they reach the controllers. Unlike `logRoutes`, the middleware generated by `express.static()` can send a response to the client if a matching file is found. If not, it will pass the request to the controllers.

### Best Practice — Build Vite Project

For Vite projects, running `npm run build` bundles and minifies the static asset files into an optimized `dist` folder. All JavaScript and CSS is condensed into one file each. This means fewer requests, smaller files, and [hashed filenames for cache-busting](https://www.keycdn.com/support/what-is-cache-busting). 

In the provided repo, build the `dist` folder and note the file structure:

```
dist/
  - index.html
  - vite.svg
  - assets/
      - index-ABC123.js
      - index-ABC123.css
```

Now, we just update the filepath to reference the `dist` folder!

```js
const filepath = path.join(__dirname, '../vite-project/dist');
```

## Summary

* **Controllers:** Callback functions that handle requests by parsing them and sending responses.
* **Middleware Functions**: Functions similar to controllers but pass requests to the next middleware without sending a response. They can also be executed for all requests while controllers typically handle a single endpoint.
* **Static Assets:** Unchanging files (e.g., HTML, CSS, JS) served by a web server. For Vanilla JS Vite projects, building is optional but recommended — it bundles, minifies, and adds cache-busting hashes to the output files.
* **Serving Static Assets**:
  1. Construct an absolute file path to the static assets folder using `path.join()` and `__dirname`.
  2. Use `express.static(filepath)` middleware to make static assets publicly available.
  3. Register the middleware with `app.use()`
