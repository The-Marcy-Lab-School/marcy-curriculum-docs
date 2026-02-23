# 4. RESTful CRUD API

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-1-0-express-rest-api-model)!
{% endhint %}

You now have the skills to build a simple server application. But it can only perform one of the four CRUD capabilities.

In this lesson, you will learn how to add crate, update, and delete endpoints and best practices for creating a "RESTful API".

**Table of Contents:**

- [Terms](#terms)
- [Introduction: Client → Server → Client → Server](#introduction-client--server--client--server)
- [CRUD Applications](#crud-applications)
  - [Creating Data with POST Requests and Endpoints](#creating-data-with-post-requests-and-endpoints)
- [Making A RESTful API](#making-a-restful-api)
  - [Core Principles of REST](#core-principles-of-rest)
- [Route Parameters](#route-parameters)
  - [Find By, Update, and Delete](#find-by-update-and-delete)


## Terms

* **RESTful API** — an API that conforms to the design principles of representational state transfer (REST).
* **`express.json()` Middleware** — parses JSON data from the request and adds it to `req.body` in the controller.
* **Route Parameters** — named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the `req.params` object.
* **HTTP Response Codes**:
  * `201` — Success: Created
  * `204` — Success: No Content
  * `400` — Client Error: Invalid Request
  * `404` — Client Error: Not Found
  * `500` — Server Error: Internal Server Error
  * `503` — Server Error: Unavailable

## Introduction: Client → Server → Client → Server

Clone down the provided repository and `cd` into the starter folder. There, you will see a `frontend` and `server` provided for you. Run both applications and you'll see it is a simple application that shows a list of fellows and their unique IDs.

![An application that renders a list of fellows fetched from the server](../.gitbook/assets/4-fellow-app-start.png)

This application represents an essential pattern that we've seen a few times now:

* The server sends the client a frontend application
* The frontend application provides the user with buttons or forms that send requests back to the server
* The server responds with data
* The frontend renders that data

This back and forth pattern between a server application and the frontend application (both from the same origin) underlies virtually every web application that you've ever used!

## CRUD Applications

This example application is quite limited in its capabilities. It only interact with its server to read data using `GET` HTTP requests.

Take a look at the frontend adapters and the server endpoints/controllers and you'll see familiar code:

{% tabs %}
{% tab title="Client" %}
{% code title="frontend/src/adapters/fellowAdapters.js" %}
```javascript
// The Home component uses this adapter to render allFellows
export const getAllFellows = async () => {
  const [allFellows, error] = await handleFetch('/api/fellows')
  return [allFellows, error];
}
```
{% endcode %}
{% endtab %}

{% tab title="Server" %}
{% code title="server/index.js" %}
```javascript
// A mock "database" of fellows
const fellows = [
  { name: 'Carmen', id: getId() },
  { name: 'Reuben', id: getId() },
  { name: 'Maya', id: getId() },
];

const serveFellows = (req, res) => {
  res.send(fellows);
}

app.get('/api/fellows', serveFellows);
```
{% endcode %}
{% endtab %}
{% endtabs %}

But think about an application like Google Calendar where you can create an account, create new events, edit events, and delete them too! Applications like these perform all of the CRUD operations.

![In Google Calendar, the application provides a button for the user to press that sends a PATCH request to the server.](../.gitbook/assets/4-google-calendar-edit-event.png)

That is, both their server and frontend are set up in such a way that enable users to create, read, update, and delete data via HTTP requests:

* Create — `POST` HTTP requests
* Read — `GET` HTTP requests
* Update — `PATCH`/`PUT` HTTP requests (`PUT` means replace the whole resource, `PATCH` means change just a part of the resource)
* Delete — `DELETE` HTTP requests

Let's look at how we can add create, update, and delete capabilities to our application!

### Creating Data with POST Requests and Endpoints

To enable our frontend application to _create_ data, we will add `POST` endpoints on our server and send `POST` requests from the frontend, often triggered by a form.

{% tabs %}
{% tab title="Client" %}
Since `POST` requests are requests to create data, we need to send data in the `body` of the request. On the frontend, this means including an `options` object with our `fetch`.

{% code title="frontend/src/adapters/fellowAdapters.js" %}
```javascript
export const createFellow = async (fellowName) => {
  const options = {
    method: "POST",
    headers: { "Content-type": "application/json" },
    body: JSON.stringify({ fellowName }) // make sure this object matches req.body on the server
  }

  const [newFellow, error] = await handleFetch(`/api/fellows/`, options);
  return [newFellow, error];
}
```
{% endcode %}
{% endtab %}

{% tab title="Server" %}
On our server, we expect JSON data to be included with the request body. The `express.json()` middleware parses JSON data from the request and adds it to `req.body` in the controller. With the data retrieved, we can create a new fellow object, add it to our "database" and send a response (or an error).

To register the controller to respond to `POST` requests, we use the `app.post` method instead of `app.get`

{% code title="server/index.js" %}
```javascript
// Middleware parses JSON from the request and adds it to req.body
app.use(express.json());

const createFellow = (req, res) => {
  // make sure this object matches the options.body on the frontend
  const { fellowName } = req.body;

  if (!fellowName) {
    // 400 means "invalid request"
    return res.status(400).send({ message: "Invalid Name"});
  }

  const newFellow = {
    name: fellowName, 
    id: getId()
  }
  fellows.push(newFellow)

  // 201 means "resource created successfully"
  res.status(201).send(newFellow);
}

// Use app.post instead of app.get
app.post('/api/fellows', createFellow);
```
{% endcode %}
{% endtab %}
{% endtabs %}

With the server controllers built on the backend and the adapter built on the frontend, we can easily test our code by hard-coding a call to the adapter:

{% code title="frontend/src/adapters/fellowAdapters.js" %}
```js
export const createFellow = async (fellowName) => {
  const options = {
    method: "POST",
    headers: { "Content-type": "application/json" },
    body: JSON.stringify({ fellowName });
  }

  const [newFellow, error] = await handleFetch(`/api/fellows/`, options);
  return [newFellow, error];
}

// Just invoke the function to test it out! You should see the server receive the request
createFellow('Winnie the Pooh');
```
{% endcode %}

With this code fully tested from the frontend and backend, we can now incorporate it into our React components! See if you can figure out how to trigger the `createFellow` adapter within the `Home` component.

<details>

<summary><strong>Solution</strong></summary>

The `Home` component already includes a **contolled form** that updates the `newFellowName` state value. When handling the form submission, we can use that state value to send our `createFellow` request. Then, to update the list with the new data in our "database", we send another `getAllFellows()` request and update the `fellows` state.

{% code title="frontend/src/pages/Home.jsx" %}
```js
const handleCreateFellow = async (e) => {
  e.preventDefault();
  console.log(`Creating fellow: ${newFellowName}`);

  // use the createFellow adapter with the form input
  createFellow(newFellowName);
  
  // re-fetch all the fellows and update the state to re-render the list
  const [allFellows, error] = await getAllFellows();
  setFellows(allFellows);

  setNewFellowName('');
}
```
{% endcode %}

</details>

## Making A RESTful API

We now have an application that can create new fellows and read the full list of fellows that have been created! Try clicking on a fellow in the list and, thanks to React Router, we are redirected to the `FellowDetails` page.

Now, we now want to add the following features to the `FellowDetails` page

* View the details (name and id) of the chosen fellow
* Update the name of the chosen fellow
* Delete the chosen fellow from the database

Before we go into the code for adding these features, we need to talk about designing our endpoints according to **REST** (**Re**presentational **S**tate **T**ransfer).

**Pop Quiz: Guess the action that each of these requests will perform!**

<details>

<summary><strong><code>GET /api/fellows/3</code></strong></summary>

Get the fellow with the id `3`

</details>

<details>

<summary><strong><code>PATCH /api/fellows/1</code></strong></summary>

Update the fellow with the id `1`

</details>

<details>

<summary><strong><code>DELETE /api/fellows/2</code></strong></summary>

Delete the fellow with the id `2`

</details>

<details>

<summary><strong><code>POST /api/fellows/2/bio</code></strong></summary>

Create a bio for the fellow with the id `2`

</details>

If you guessed these correctly, there is a good reason why! The endpoints are intuitive to understand thanks to the best practices encouraged by REST.

### Core Principles of REST

[restfulapi.net](https://restfulapi.net/) puts it best:

> REST is an acronym for REpresentational State Transfer and an architectural style for distributed hypermedia systems. Roy Fielding first presented it in 2000 in his famous dissertation. Since then, it has become one of the most widely used approaches for building web-based APIs (Application Programming Interfaces).
>
> REST is not a protocol or a standard, it is an architectural style. During the development phase, API developers can implement REST in a variety of ways.
>
> Like the other architectural styles, REST also has its guiding principles and constraints. These principles must be satisfied if a service interface is to be referred to as RESTful.

The guiding principles of REST can be found in detail in the website above. Here, we've provided a few easy ways to ensure you are building a RESTful API:

1. **Requests Should be Statelessness**: Each request should contain all the information needed by the client for the current. The server doesn't store the current client state.
   * For example, if a user selects a filter that is applied to a `GET` request, that filter must be provided with every request. The server should not be expected to remember the client's state.
2. **Endpoints Describe Resources, Not Actions**: Use plural nouns (e.g., `/api/users`, `/api/posts`) instead of verbs (e.g., `/api/getUser`, `/api/createPost`) for endpoint URLs.
   * A common exception to this rule is `/api/login` and `/api/logout`
3. **HTTP Methods Matter**:
   * `GET` – Retrieve data
   * `POST` – Create data
   * `PUT / PATCH` – Update data
   * `DELETE` – Remove data
4. **Endpoints Should Use Proper Status Codes** (e.g., 200 OK, 201 Created, 400 Bad Request, 404 Not Found, 500 Internal Server Error)
5. **URLs Should Indicate a Clear Hierarchy of Resources:**
   * Use IDs to get individual resources: `/api/users/123`
   * Nest resources to indicate ownership: `/api/users/123/posts/456`
   * Avoid deep nesting that becomes hard to manage.

These principles help our API become "RESTful". But keep in mind that these are just guidelines that help make an API more intuitive and predictable for the client. Providing clear documentation will always be the best way to ensure your API is used properly.

## Route Parameters

In the request `GET /api/fellows/3`, the value `3` indicates that I want to get the fellow with the ID `3`. To generalize the `id` value in this request endpoint, we define the endpoint like so:

```
GET /api/fellows/:id
```

In this more generalized endpoint URL, the `:id` portion is called a **route parameter**—a placeholder in the endpoint for a value provided by the client.

### Find By, Update, and Delete

With route parameters, we can now add these endpoints to our server

* `GET /api/fellows/:id` using `app.get()`
* `PATCH /api/fellows/:id` using `app.patch()`
* `DELETE /api/fellows/:id` using `app.delete()`

In each of the corresponding controllers, we can access the `id` route parameter's value via the `req.params` object.

{% tabs %}
{% tab title="GET /api/fellows/:id" %}
When fetching a single fellow, we use the `id` route parameter to find the fellow with the matching `id` in our "database":

```javascript
const serveFellow = (req, res) => {
  // Make sure the property name matches the route parameter below
  const { id } = req.params;

  // Keep in mind, route parameters are stored as strings.
  const fellow = fellows.find(fellow => fellow.id === Number(id));

  if (!fellow) {
    // 404 means "Resource Not Found"
    return res.status(404).send({ 
      message: `No fellow with the id ${id}`
    });
  }

  res.send(fellow);
};

// req.params.id will hold the value of the requested id
app.get('/api/fellows/:id', serverFellow)
```
{% endtab %}

{% tab title="PATCH /api/fellows/:id" %}
A `PATCH` request is similar to a `POST` request in that we expect the request body to include JSON data. Here, we expect a new `fellowName` to be provided. We again use the `id` route parameter to find the fellow with the matching `id` in our "database" and then update the `name` property.

```javascript
const updateFellow = (req, res) => {
  const { fellowName } = req.body;

  if (!fellowName) {
    return res.status(400).send({ message: "Invalid Name" });
  }

  const { id } = req.params;
  const updatedFellow = fellows.find(fellow => fellow.id === Number(id));

  if (!updatedFellow) {
    return res.status(404).send({ 
      message: `No fellow with the id ${id}` 
    });
  }

  updatedFellow.name = fellowName;
  res.send(updatedFellow);
}

app.patch('/api/fellows/:id', updateFellow);
```
{% endtab %}

{% tab title="DELETE /api/fellows/:id" %}
Here, we use the `id` route parameter to find the index of the fellow with the matching `id` in our "database" so that we can splice that index out of the array.

```javascript
const deleteFellow = (req, res) => {
  const { id } = req.params;

  const fellowIndex = fellows.findIndex((fellow) => fellow.id === Number(id));
  if (fellowIndex < 0) {
    return res.status(404).send({ 
      message: `No fellow with the id ${id}` 
    });
  }

  fellows.splice(fellowIndex, 1);
  // 204 means "no content" - the request was successful but there's no content to send back
  res.sendStatus(204);
}

app.delete('/api/fellows/:id', deleteFellow);
```
{% endtab %}
{% endtabs %}

In our frontend, we can create adapters for each endpoint:

{% tabs %}
{% tab title="GET /api/fellows/:id" %}
Sending a `GET /api/fellows:id` request is straightforward!

```js
export const getFellowById = async (id) => {
  const [fellow, error] = await handleFetch(`/api/fellows/${id}`);
  return [fellow, error];
}
```
{% endtab %}

{% tab title="PATCH /api/fellows/:id" %}
Sending a `PATCH /api/fellows:id` request requires us to include the `id` in the endpoint URL and the new `fellowName` in the `options.body`, along with the appropriate `options.method` and `options.headers` values.

```javascript
export const updateFellowName = async (id, fellowName) => {
  const options = {
    method: "PATCH",
    headers: { "Content-type": "application/json" },
    body: JSON.stringify({ fellowName })
  };

  const [updatedFellow, error] = await handleFetch(`/api/fellows/${id}`, options);
  return [updatedFellow, error];
}
```
{% endtab %}

{% tab title="DELETE /api/fellows/:id" %}
Sending a `DELETE /api/fellows:id` request requires us to include the `id` in the endpoint URL and the appropriate `options.method` value.

```javascript
export const deleteFellow = async (id) => {
  const options = {
    method: "DELETE",
  };
  const [success, error] = await handleFetch(`/api/fellows/${id}`, options);
  return [success, error];
}
```
{% endtab %}
{% endtabs %}
