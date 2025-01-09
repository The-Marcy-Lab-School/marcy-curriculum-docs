# Securing API Keys with Environment Variables

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-0-2-express-api-middleman)!
{% endhint %}

Serving static assets is a core functionality of a server application.

The second use case for a server is to perform 3rd-party API requests that require an API key in a manner that protects the developer's API key.

Let's do it!

* [Terms](3-securing-api-keys-with-environment-variables.md#terms)
* [Setup](3-securing-api-keys-with-environment-variables.md#setup)
* [API Keys](3-securing-api-keys-with-environment-variables.md#api-keys)
  * [Why We Make API Requests from the Server](3-securing-api-keys-with-environment-variables.md#why-we-make-api-requests-from-the-server)
* [Environment Variables](3-securing-api-keys-with-environment-variables.md#environment-variables)
* [Making an API request from the frontend](3-securing-api-keys-with-environment-variables.md#making-an-api-request-from-the-frontend)
* [Development Frontend Request Proxy (if time)](3-securing-api-keys-with-environment-variables.md#development-frontend-request-proxy-if-time)
  * [Two Version of the Frontend](3-securing-api-keys-with-environment-variables.md#two-version-of-the-frontend)
  * [Proxy Requests](3-securing-api-keys-with-environment-variables.md#proxy-requests)

## Terms

* **API Key** - a secret code that verifies your identity as a developer using an API's limited resources. Do not share these!
* **Environment Variable** — a hidden variable stored on the host's machine (your laptop or Render.com) and accessible in Node through the `process.env` object
* **`.env` file** - a file to store hidden variables like API keys. Ignored by GitHub and uploaded to Render for deployment.
* **`dotenv` module** - an npm package for importing `.env` files
* **Cross-origin requests** - HTTP requests made from one origin to another.
* **Request Proxy In Development** — faking the origin of the request in a frontend development server to match the origin of the backend server

## Setup

* Fork this repo
* `cd` into the `server` folder and run `npm i`
* Start the server application with `nodemon index.js`
* In a separate terminal, `cd` into the `frontend` and run `npm i`
* Start the frontend application with `npm run dev`
* Note that there is a `utils/fetchData` helper function provided for you in both the `server` and the `frontend`.

## API Keys

For this lecture, you'll need an **API key** - a secret code that verifies your identity as a developer using an API's limited resources. Do not share these!

We'll use the Giphy API because the API is quite friendly to use.

If you haven't already, make an account on the [Giphy Developers Page](https://developers.giphy.com/dashboard/)

Then:

* Go to your dashboard
* Create an App.
* Select API (it is free) and give your app a name (e.g. "giphy search") and confirm to get your API key.
* Copy the API key
* Open the `frontend/src/App.jsx` file and paste your API key into the string assigned to `API_KEY`
* If your frontend folder is running, you should see the top 3 trending gifs appear.

<details>

<summary><strong>Q: Why is it not a good idea to share your API key? What really could go wrong?</strong></summary>

The API key is a way to verify your identity as a developer. Some APIs will charge you for each request that you make using your API key and if someone else gets a hold of your API key, they could steal your request resources.

</details>


### Why We Make API Requests from the Server

If we want to avoid making our API keys public, we need to be careful with how we use them in our code. **Our frontend can't safely make requests using the API key** — anyone using our deployed application can just look at the Network tab to see the API keys in the request URL.

**So, we have to make the requests using the API keys in our backend.**

![](img/express-api-middleman.svg)

But even to deploy our server code, we'll need to store the API key on GitHub. Or do we?

## Environment Variables

**Environment Variables** are hidden variable stored in a `.env` file that lives on the host (your laptop or a deployment host like Render)

They are great for storing API keys:

```
API_KEY="abc123"
```

We can import the environment variables from `.env` using the `dotenv` module from npm.

```
npm i dotenv
```

In our server JavaScript, we can write:

```js
const dotenv = require('dotenv');
dotenv.config();

// or just 

require('dotenv').config();

// we can access the value using process.env

console.log(process.env.API_KEY); // abc123

// and then make a controller

const serveGifs = async (req, res, next) => {
  const API_URL = `https://api.giphy.com/v1/gifs/trending?api_key=${process.env.API_KEY}&limit=3&rating=g`;
  const [data, error] = await fetchData(API_URL);
  if (error) {
    console.log(error.message);
    return res.status(404).send(error);
  }
  res.send(data);
}


// and route it to an endpoint

app.get('/api/gifs', serveGifs)
```

With this endpoint set up, visit http://localhost:8080/api/gifs and see the fetched data! Again, the client has no vision of the API key!

The beauty of environment variables is that we can hide them from our GitHub repository using a `.gitignore` file and then upload them directly to the deployment host (e.g. Render).

## Making an API request from the frontend

Now our server can perform a 3rd-party API request using a protected API key. However, we still have a frontend React application that is using the Giphy API directly and exposing our API key!

Instead of sending the fetch request to the Giphy API, the frontend should send the request to the server.

![](img/express-api-middleman.svg)

Normally when sending requests to 3rd-party APIs, we include the full URL: `http://someapi.com/endpoint`. These kinds of requests are **cross-origin** requests because the origin (`http://someapi.com`) is different from the origin of our server (`http://localhost` or wherever the app is deployed). Our server can safely do this but as we've seen, our front-ends should not.

However, we want to send a **same-origin** request — the origin of the request is the same as the origin of the response (we got the frontend application from the same server that we are now requesting data from).

So, our request URL will be: `/api/gifs`. When we leave the host out of the URL, our browser will assume the request is a same-origin request.

```js
// The App component

useEffect(() => {
  const doFetch = async () => {
    const API_URL = `/api/gifs`;
    try {
      const [data, error] = await fetchData(API_URL);
      if (data) setGifs(data.data);
    } catch (error) {
      console.log(error.message)
    }
  }
  doFetch();
}, [])
```

To test this out we should:

* Re-run `npm run build` to re-build our frontend application's static assets in the `dist/` folder.
* Re-run the server which will serve our updated frontend static assets.
* Visit the server http://localhost:8080 to see the updated frontend!

## Development Frontend Request Proxy (if time)

> _tldr: copy and paste the code snippet below into your `vite.config.js` file to make your development frontend application play nicely with your server and avoid cross-origin resource sharing (CORS) issues._

### Two Version of the Frontend

We could totally stop here and everything would be great. We can now build server applications that provide frontends that utilize 3rd-party APIs without exposing API keys by utilizing environment variables!

However, we need to remember that we have two different versions of our frontend:

1. The development version (which lives in the `frontend/src` folder)
2. The built "production/distribution" version (which lives in the `frontend/dist` folder)

<details>

<summary><strong>Q: Which version does the server serve?</strong></summary>

The built "production/distribution" version in the `frontend/dist` folder!

Remember, the server can only serve static assets. The development version is not "static" because it contains dynamic JSX React code. It must be compiled into plain JS first.

</details>


### Proxy Requests

This is where things may get confusing... Bear with me...

In that last step, we tested the frontend changes by running `npm run build` to update our frontend `dist` folder, and by opening the static frontend application served by our backend at http://localhost:8080.

If we instead tested the frontend changes by running `npm run dev` and viewing the app through the Vite development server, the app would break. In fact, if you do that now, it will still break. This is because `npm run dev` will serve our frontend at http://localhost:5173/ which is a different origin from our server: http://localhost:8080.

When the frontend makes a request to `/api/gifs` from http://localhost:5173/, the request is going to http://localhost:5173/api/gifs which doesn't provide the resources we are looking for.

To enable the development version of our frontend to send same-origin requests, we need to "trick" the Vite development server into sending "same-origin" requests to http://localhost:8080 instead of http://localhost:5173.

To do this, copy and paste the code below into your `frontend/vite.config.js` file. This is called a **proxy**.

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

const PORT = 8080;

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: `http://localhost:${PORT}`,
        changeOrigin: true,
      },
    },
  },
});
```
