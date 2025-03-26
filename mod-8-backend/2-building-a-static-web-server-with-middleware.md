# Building a Static Web Server with Middleware

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-0-1-express-middleware)!
{% endhint %}

In the last lecture, we created a server that could send back HTML in a single file. But when we build our React projects, they are more than just a single file. React projects typically have HTML, CSS, and JS, and potentially images or other files. In order to serve all of these static assets, we need Express Middleware.

Let's make a static web server!

**Table of Contents:**

- [Terms](#terms)
- [Controllers Review](#controllers-review)
- [Middleware](#middleware)
- [Serving Static Assets](#serving-static-assets)
  - [Try it Yourself!](#try-it-yourself)
- [Summary](#summary)

## Terms

* **Middleware** - a function in express that intercepts and processes incoming HTTP requests. It can perform server-side actions such as parsing the request, modifying the response, or executing additional logic before passing control to the next middleware in the chain."
* **`path` module** - a module for creating absolute paths to static assets
* **Environment Variable** - a variable defined outside of the JavaScript execution context.
* **`__dirname`** — an environment variable that returns the path to the parent directory of the current file.
* **Static Assets** - unchanging files delivered to the client exactly as they are stored on a server. These include HTML, CSS, JavaScript files, images, videos, fonts, and documents. For React projects, we need to "build" our project to generate static assets (convert `.jsx` files to `.js` files).

## Controllers Review

Remember how the Express app works?

![](img/express-diagram-simple.svg)

1. A client sends a **request** to the server.
2. The server receives the request and **routes** it to the proper **controller** based on the specific **endpoint**.
3. The controller processes the request, interacts with any necessary data or services, and generates a **response**.
4. The server sends the response back to the client.
5. The client is now free to do what it likes with the response.

And here is how we can create a server with a single endpoint: `/api/hello`

```js
const express = require('express');
const app = express();

// When triggered, this controller will send a response
const serveHello = (req, res, next) => {
  const name = req.query.name || "stranger"
  res.send(`hello ${name}`);
}

// A GET request to /api/hello?name=ben will trigger serveHello
app.get('/api/hello', serveHello);


// Listen for requests on port 8080
const port = 8080;
app.listen(port, () => console.log(`listening at http://localhost:${port}`)); 
```

Remember, a **controller** is a callback function that parses a request and sends a response. It will be invoked by the Express `app` when the associated endpoint is sent a request.

Every controller is invoked with three values:
  * A `req` object which holds data related to the request, including **query parameters**.
  * A `res` object which has methods to send a response.
  * A `next` function, typically only used by "Middleware".

## Middleware

Middleware in Express is a function that intercepts and processes incoming HTTP requests before they reach the final controller or response handler.

For example, this `logRoutes` middleware prints out information about all incoming requests before passing the request to the final controller `serveHello`:

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

* `app.use` is like `app.get` but is used for middleware
* The `logRoutes` middleware will be invoked for ALL incoming requests, regardless of the endpoint (this behavior can be changed if desired)
* Notice that the `logRoutes` middleware controller doesn't use the `res` object at all. Instead, it invokes `next()`, passing control to  `serveHello` to complete the request-response cycle and send a response.

Our diagram now looks like this:

![](img/express-middleware.svg)

<details>

<summary><strong>Q: So, if a user sends a request to <code>http://localhost:8080/api/hello</code>, which functions are invoked and in what order?</strong></summary>

First the `logRoutes` middleware is invoked. The `next()` function is called which passes the request to the next controller, `serveHello`.

</details>

<details>

<summary><strong>Q: What would happen if the <code>logRoutes</code> controller DID send a response to the client? What would happen if it didn't invoke <code>next()</code>?</strong></summary>

If `logRoutes` did invoke `res.send()`, the `serveHello` controller would NOT be invoked as a response has already been sent. If we simply didn't invoke `next()`, our server would "hang" — the response would never be completed and the client would likely receive a timeout error because the request took too long.

</details>

Middleware can be custom-made like this `logRoutes`. However, we can also utilize some of the out-of-the-box middleware controllers provided by Express.

## Serving Static Assets

One of the most important roles of a full stack web server is to provide a client with a frontend. Whenever you visit a website, that's what happens — go to https://google.com and the Google server sends back HTML, CSS, and JavaScript to render the user interface.

That's what **static web servers** like GitHub Pages do — they store **static assets** (HTML, CSS, and JS files) and then provide a server application that serves those assets when requested.

With Express, it is really quick to build your own static web server using the `express.static(filepath)` middleware function. You only need 4 lines!

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

* The `path.join()` method constructs an absolute file path to the static assets folder, ensuring compatibility across different operating systems.
* `__dirname` provides the absolute path of the current module's parent directory.
* The `express.static()` middleware function makes static assets (such as HTML, CSS, and JS files) from the specified directory publicly available.
* The middleware function `serveStatic` is used with app.use() to enable serving static assets to clients.

### Try it Yourself!

To experiment with this, use Vite to create a React project called `frontend`. Install dependencies dependencies and then build the project with `npm run build`. Note that static assets are in the `dist` folder. 

Then, we can add the code above to your `server/index.js` file. Make sure to modify the `pathToDistFolder` variable and run the server!

Now, if you run the server and visit the `http://host:port/index.html`, the server will send you the `index.html` file! (You can also just visit `http://host:port/` and it will automatically send you the index file).

{% hint style="info" %}

Any other files in the provided folder can also be accessed on your server. Assuming the `/dist` directory contains 2 images, `foo.jpg` and `bar.jpg` then you can simply access them at:

```
http://host:port/foo.jpg
http://host:port/bar.jpg
```

{% endhint %}

## Summary

* **Controllers:** Callback functions that handle requests by parsing them and sending responses.
* **Middleware Functions**: Functions similar to controllers but pass requests to the next middleware without sending a response. They can also be executed for all requests while controllers typically handle a single endpoint.
* **Static Assets:** Unchanging files (e.g., HTML, CSS, JS) served by a web server. For React projects, we need to "build" the project to convert "dynamic" `.jsx` files to "static" `.js` files
* **Serving Static Assets**:
  1. Construct an absolute file path to the static assets folder using `path.join()` and `__dirname`.
  2. Use `express.static(filepath)` middleware to make static assets publicly available.
  3. Register the middleware with `app.use()`
