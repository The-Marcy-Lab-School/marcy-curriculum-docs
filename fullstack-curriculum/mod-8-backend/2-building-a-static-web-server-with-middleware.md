# Building a Static Web Server with Middleware

In the last lecture, we created a server that could send back HTML in a single file. But when we build our React projects, they are more than just a single file. React projects typically have HTML, CSS, and JS, and potentially images or other files. In order to serve all of these static assets, we need Express Middleware.

Let's make a static web server!

**Table of Contents:**
- [Terms](#terms)
- [Controllers Review](#controllers-review)
- [Middleware](#middleware)
- [Serving Static Assets](#serving-static-assets)
- [Summary](#summary)

## Terms

- **Middleware** - a function in express that intercepts and processes incoming HTTP requests. It can perform server-side actions such as parsing the request, modifying the response, or executing additional logic before passing control to the next middleware in the chain."
- **`path` module** - a module for creating absolute paths to static assets
- **Environment Variable** - a variable defined outside of the JavaScript execution context.
- **`__dirname`** — an environment variable that returns the path to the parent directory of the current file.
- **Static Assets** - unchanging files delivered to the client exactly as they are stored on a server. These include HTML, CSS, JavaScript files, images, videos, fonts, and documents. For React projects, we need to "build" our project to generate static assets (convert `.jsx` files to `.js` files).

## Controllers Review

Remember how the Express app works?

1. A client sends a **request** to the server.
1. The server receives the request and **routes** it to the proper **controller** based on the specific **endpoint**.
1. The controller processes the request, interacts with any necessary data or services, and generates a **response**.
1. The server sends the response back to the client.
1. The client receives the response and renders the data or take further actions based on it.
  
![](./img/express-diagram-simple.svg)

```js
// controller
const serveHello = (req, res, next) => {
  const name = req.query.name || "stranger"
  res.send(`hello ${name}`);
}

// endpoint
app.get('/api/hello', serveHello);

// A GET request to /api/hello?name=ben will send the response "hello ben"
```

- A **controller** is a callback function that parses a request and sends a response. It will be invoked by the Express `app` when the associated endpoint is sent a request.
    - The controller receives a `req` object from the Express `app` which holds data about the request, including **query parameters**.
    - It also receives a `res` object which has methods to send a response.

What about `next`?

## Middleware

A "middleware" is a type of controller that can also parse requests and perform server-side actions.

Unlike normal controllers, middleware functions **pass the request to the next function in the chain without sending a response to the client.** They sit in the "middle" of the chain of middleware/controllers.

For example, this middleware prints out information about the incoming request in a nice format:

```js
// Middleware function for logging route requests
const logRoutes = (req, res, next) => {
  const time = new Date().toLocaleString();
  console.log(`${req.method}: ${req.originalUrl} - ${time}`);
  next(); // Passes the request to the next middleware/controller
};

// Controller function for serving a hello message
const serveHello = (req, res, next) => {
  const name = req.query.name || "stranger";
  res.send(`Hello, ${name}!`);
};

// Register the logRoutes middleware globally to log all requests
app.use(logRoutes);

// Register the serveHello controller for the /api/hello route
app.get('/api/hello', serveHello);
```

- `app.use` is like `app.get` but for middleware
- When `app.use` is invoked with just the middleware function, it executes that middleware for ALL routes
- Notice that the `logRoutes` middleware controller doesn't use the `res` object at all and then invokes `next()` to pass control to the next controller in the chain.

Our diagram now looks like this:

![](./img/express-middleware.svg)


**<details><summary style="color: purple">Q: So, if a user sends a request to `http://localhost:8080/api/hello`, which controllers are invoked and in what order?</summary>**
> First the `logRoutes` middleware is invoked. The `next()` function is called which passes the request to the next controller, `serveHello`.
</details><br>

**<details><summary style="color: purple">Q: What would happen if the `logRoutes` controller DID send a response to the client? What would happen if it didn't invoke `next()`?</summary>**
> If `logRoutes` did invoke `res.send()`, the `serveHello` controller would NOT be invoked as a response has already been sent.
> If we simply didn't invoke `next()`, our server would "hang" — the response would never be completed and the client would likely receive a timeout error because the request took too long.
</details><br>

Middleware can be custom-made like this `logRoutes`. However, we can also utilize some of the out-of-the-box middleware controllers provided by Express.

## Serving Static Assets

One of the most important roles of a full stack web server is to provide a client with a frontend. Whenever you visit a website, that's what happens — go to https://google.com and the Google server sends back HTML, CSS, and JavaScript to render the user interface.

That's what static web servers like GitHub Pages do — they store **static assets** (HTML, CSS, and JS files) and then provide a URL where users can access them.

With Express, it is really quick to build your own static web server using the `express.static(filepath)` middleware function. You only need 4 lines!

Suppose we had a React project in a directory called `frontend` and it has its static assets built into a directory called `dist`:

```js
// Import the path module to construct the absolute path to the static assets folder
const path = require('path');

// Construct the absolute path to the static assets folder using the `path.join()` method
// Use '../' to navigate to a parent directory, similar to when you are using `cd`.
const pathToDistFolder = path.join(__dirname, '../../path/to/frontend/dist');

// Create the middleware function for serving static assets
const serveStatic = express.static(pathToDistFolder);

// Use the middleware function to serve static assets
app.use(serveStatic);

```

Explanation:

- The `path.join()` method constructs an absolute file path to the static assets folder, ensuring compatibility across different operating systems.
- `__dirname` provides the absolute path of the current module's parent directory.
- The `express.static()` middleware function makes static assets (such as HTML, CSS, and JS files) from the specified directory publicly available.
- The middleware function `serveStatic` is used with app.use() to enable serving static assets to clients.

Now, if you run the server and visit the `http://host:port/index.html`, the server will send you the `index.html` file! (You can also just visit `http://host:port/` and it will automatically send you the index file).

Any other files in the provided folder can also be accessed on your server. Assuming the `/dist` directory contains 2 images, `foo.jpg` and `bar.jpg` then you can simply access them at:

```
http://host:port/foo.jpg
http://host:port/bar.jpg
```

## Summary

- **Controllers:** Callback functions that handle requests by parsing them and sending responses.
- **Middleware Functions**: Functions similar to controllers but pass requests to the next middleware without sending a response. They can also be executed for all requests while controllers typically handle a single endpoint.
- **Static Assets:** Unchanging files (e.g., HTML, CSS, JS) served by a web server. For React projects, we need to "build" the project to convert "dynamic" `.jsx` files to "static" `.js` files
- **Serving Static Assets**:
  1. Construct an absolute file path to the static assets folder using `path.join()` and `__dirname`.
  2. Use `express.static(filepath)` middleware to make static assets publicly available. 
  3. Register the middleware with `app.use()`
