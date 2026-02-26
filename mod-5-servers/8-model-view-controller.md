# 5. Model-View-Controller Architecture

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-5-rest-api-model)!
{% endhint %}

As your application grows in scale and scope, it is important to have a consistent approach for organizing its many components.

In this lesson, we'll learn how to implement one of the most popular patterns called the **Model-View-Controller Architecture**.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Organization and Separation of Concerns](#organization-and-separation-of-concerns)
  - [The Model-View-Controller (MVC) Architecture](#the-model-view-controller-mvc-architecture)
- [Implementing a Model for MVC](#implementing-a-model-for-mvc)
  - [Server Organization](#server-organization)
  - [Build a Model](#build-a-model)
- [Challenge](#challenge)


## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is the MVC architecture? What is the responsibility of each layer (Model, View, Controller)?
2. What is "separation of concerns" and why does it matter as an application grows?
3. In the context of an Express server, what logic belongs in a model vs. a controller?
4. How do you structure a server directory to implement MVC?
5. Why does a Model use `static` methods instead of instance methods?

## Organization and Separation of Concerns

In the last lesson, we built a RESTful API that lets users manage a list of fellows. They can:

* (Create) Add a new fellow to the list
* (Read) Get all fellows
* (Read) Get a single fellow
* (Update) Change the name of a fellow
* (Delete) Remove a fellow from the list

In that application, all of the logic was built into the `server/index.js` file. While we have some organization within the file, separating the concerns of one file into multiple files will enable our application to scale without becoming a "monolith"

{% hint style="info" %}
In software development, a "code monolith" refers to a single, large, and typically tightly coupled codebase that contains all the application's components, often making it difficult to scale, maintain, and deploy
{% endhint %}

### The Model-View-Controller (MVC) Architecture

While there are many approaches for organization and separation of concerns, one highly popular approach is called the **Model-View-Controller (MVC) Architecture**.

![The view sends requests with user inputs to the controller with updates the model. New data is returned to the controller which sends that data in a response to the view.](./img/8-model-view-controller/mvc-diagram.png)

This architecture pattern organizes our code into three distinct pieces:

* The **Models** are responsible for storing and managing the data of an application. They provide an interface (a set of methods) for interacting with that data in a predictable manner.
* The **Views** are responsible for rendering the data of an application. They provide user-interfaces with buttons, forms, and other components that allow the user to see and request changes to the data.
* The **Controllers** are responsible for managing interactions between the views and models. They process user inputs from the views, invoke the appropriate methods of the models, and send response back to the views to be updated.

{% hint style="info" %}
Often times, it can be hard to implement your application such that it strictly adheres to any one framework or architecture. Keep in mind that architectures like MVC present an ideal to strive for, not a strict pattern that must be followed at all times.
{% endhint %}

<details>

<summary><strong>What separate components of this architecture does our application already have?</strong></summary>

We have a Vanilla JS frontend application acting as the view component.

We have the Express server application acting as the controllers AND as the model. We need to separate the controller from the model.

</details>

## Implementing a Model for MVC

With our current application structure, we already have clear separation between the views (our frontend Vanilla JS application) and the controllers (our Express endpoints/controllers).

However, our controllers and model are intertwined since our controllers directly interact with the database (the `fellows` array). That is, they both handle the logic of parsing request inputs AND handle the logic of managing the data.

```js
// Our "Database"
const fellows = [
  { name: 'Carmen', id: getId() },
  { name: 'Reuben', id: getId() },
  { name: 'Maya', id: getId() },
];

// Our Controllers
const createFellow = (req, res) => {
  // Parse Inputs
  const { fellowName } = req.body;

  // Send Error Response
  if (!fellowName) {
    return res.status(400).send({ message: "Invalid Name" });
  }
  
  // Create a new fellow and add it to the "database"
  const newFellow = {
    name: fellowName,
    id: getId()
  }
  fellows.push(newFellow)

  // Send Response
  res.status(201).send(newFellow);
}

const findFellow = (req, res) => {
  // Parse Inputs
  const { id } = req.params;

  // Find the fellow in the "database"
  const fellow = fellows.find(fellow => fellow.id === Number(id));

  // Send Response
  if (!fellow) {
    return res.status(404).send({ 
      message: `No fellow with the id ${id}`
    });
  }

  res.send(fellow);
};

// ... and more...
```

We need to create a separate model that focuses solely on managing the `friends` database and provides methods for our controllers to use.

![Controllers now use the Fellow Model interface to update the "database" before sending a response back to the client.](./img/8-model-view-controller/express-middleware-model.svg)

### Server Organization

To create an MVC architecture, take a moment and build the following file structure for your server application.

```
server/
├── index.js
├── controllers/
│   └── fellowControllers.js
└── models/
    └── Fellow.js
 
```

* `index.js` builds the `app`, configures middleware, and sets the endpoints. However, the controllers are now imported.
* `controllers/fellowControllers.js` defines all of the controllers for endpoints relating to fellow data. Each set of data should have its own controllers file.
* `models/Fellow.js` defines a model for managing fellow data. This model is used exclusively by the fellow controllers. Each set of data managed by the server should have its own model.

By separating our code in this way, we show the separate "layers" of the application.

{% hint style="success" %}
Before we build a model, do the following:

* Move all of your controller functions into the `controllers/fellowControllers.js` file
* Export them as an object using `module.exports = { /* list controller methods*/ }`
* Import the collection of controllers into `server/index.js` using `require(./controllers/fellowControllers.js)`
{% endhint %}

### Build a Model

To build a separate model layer to handle only data management logic, we will:

* Make a `models/Fellow.js` file.
* Inside, export a `Fellow` class with `static` methods for each action needed by the controllers.
* Move the `fellows` "database" inside so the only way to access its data is through the model's interface.

{% hint style="info" %}
Note that there is no `constructor()` function here! This may seem odd at first, but consider interfaces like `Math`. These interfaces just provide static methods and properties like `Math.random()` and `Math.PI`. You never invoke `new Math()` to generate an instance of `Math`
{% endhint %}

{% code title="server/models/Fellow.js" %}
```js
// Auto-incrementing ID generator
const getId = ((id = 0) => () => ++id)();

// Restrict access to our mock "database" to just the Model
const fellows = [
  { name: 'Carmen', id: getId() },
  { name: 'Reuben', id: getId() },
  { name: 'Maya', id: getId() },
];

class Fellow {
  // Create and add the new fellow to the "database" (the fellows array)
  static create(name) {
    const newFellow = {
      name,
      id: getId()
    }
    fellows.push(newFellow);
    return newFellow;
  }

  // Get all values from the "database"
  static getAll() {
    return [...fellows];
  }

  // Get one value from the "database"
  static find(id) {
    return fellows.find((fellow) => fellow.id === id);
  }

  // Update one value from the "database"
  static editName(id, newName) {
    const fellow = Fellow.find(id);
    if (!fellow) return null;
    fellow.name = newName;
    return fellow;
  }

  // Delete one value from the "database"
  static delete(id) {
    const fellowIndex = fellows.findIndex((fellow) => fellow.id === id);
    if (fellowIndex < 0) return false;

    fellows.splice(fellowIndex, 1);
    return true;
  }
}

module.exports = Fellow;
```
{% endcode %}

We are now leaving it entirely to the `Fellow` model to manage the data and the controllers just focus on parsing the request and sending the response.

{% code title="server/controllers/fellowControllers.js" %}
```js
const Fellow = require('../models/Fellow.js');)

const createFellow = (req, res) => {
  const { fellowName } = req.body;
  if (!fellowName) {
    return res.status(400).send({ message: "Invalid Name" });
  }

  const newFellow = Fellow.create(fellowName);
  res.send(newFellow);
};

// Use `Fellow.find` to get the desired fellow
const findFellow = (req, res) => {
  const { id } = req.params;
  const fellow = Fellow.find(Number(id));

  if (!fellow) {
    return res.status(404).send({ 
      message: `No fellow with the id ${id}`
    });
  }
  res.send(fellow);
};
```
{% endcode %}

## Challenge

Build a `Song` model and a server application for maintaining a playlist. Each song should have an `id`, a `title`, and an `artist` (at minimum). The model should provide an interface for:

* Creating a new song
* Getting all songs
* Getting a single song
* Updating the title or artist of a song
* Deleting a song

Then, create an endpoint and a controller for each of these pieces of functionality. The endpoints should follow REST conventions and should all begin with `/api`

Finally, build a Vanilla JS frontend application that can interact with the songs API that you've built. It should be able to:

* Create: Add a new song to the list using a form.
* Read: Display a list of all songs.
* Update: Update a single song's title or artist using an inline edit form.
* Delete: Delete a single song using a delete button.

Structure your frontend using the same file organization as the fellow tracker:

* `index.html` — static shell with the form and list
* `src/fetch-helpers.js` — one exported function per API endpoint
* `src/dom-helpers.js` — `renderSongs()` and `renderError()`
* `src/main.js` — event listeners and handlers that call fetch helpers and re-render
