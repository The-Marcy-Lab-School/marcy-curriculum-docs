# Interactive & Data-Driven User Interfaces Cheat Sheet

Use this as a reference while working. **You don't need to memorize these!**

**Table of Contents:**
- [DOM (Document Object Model)](#dom-document-object-model)
  - [Linking JavaScript to HTML](#linking-javascript-to-html)
  - [Selecting Elements](#selecting-elements)
  - [Modifying Elements](#modifying-elements)
  - [Creating Elements](#creating-elements)
  - [Removing Elements](#removing-elements)
  - [Event Listeners](#event-listeners)
  - [Handling Form Submissions](#handling-form-submissions)
  - [Common DOM Patterns](#common-dom-patterns)
- [Servers, APIs, and HTTP](#servers-apis-and-http)
  - [Client, Server, and API Basics](#client-server-and-api-basics)
  - [Endpoints](#endpoints)
  - [HTTP Methods / Verbs](#http-methods--verbs)
  - [Status Codes](#status-codes)
- [Async](#async)
  - [Key Concepts](#key-concepts)
  - [`fetch()` 6-Step Pattern (`.then()` / `.catch()`)](#fetch-6-step-pattern-then--catch)
  - [`async` / `await` Version](#async--await-version)

## DOM (Document Object Model)

The DOM is a JavaScript representation of your HTML. It lets you select, modify, create, and delete elements on the page.

### Linking JavaScript to HTML

Add a `<script>` tag at the end of your `<body>`:

**Snippet:** Load your JavaScript file after the HTML so the DOM is already available.

```html
<body>
  <!-- Your HTML content -->
  <script src="./index.js"></script>
</body>
```

For ES6 modules (import/export), add `type="module"`:

**Snippet:** Enable `import` / `export` syntax in browser JavaScript.

```html
<script type="module" src="./index.js"></script>
```

**Note:** Modules require a server (use the VS Code Live Server extension).

### Selecting Elements

Use `document.querySelector()` with CSS selector syntax:

**Snippet:** Select one element using CSS selectors.

```js
// By tag name
document.querySelector('h1');

// By class (returns first match)
document.querySelector('.my-class');

// By ID
document.querySelector('#my-id');

// Complex selectors work too
document.querySelector('ul > li:first-child');
```

Use `querySelectorAll()` to get ALL matching elements (returns a NodeList):

**Snippet:** Select multiple elements and loop through them.

```js
const allButtons = document.querySelectorAll('button');

// Loop through with forEach
allButtons.forEach((btn) => {
  console.log(btn);
});
```

### Modifying Elements

**Snippet:** Update text, styles, classes, and attributes on an existing element.

```js
const heading = document.querySelector('h1');

// Change text content
heading.textContent = 'New Title';

// Change styles
heading.style.color = 'blue';
heading.style.backgroundColor = 'yellow';

// Add/remove CSS classes
heading.classList.add('highlight');
heading.classList.remove('old-class');
heading.classList.toggle('active');

// Change attributes
heading.id = 'main-heading';
heading.setAttribute('data-info', 'some value');
```

### Creating Elements

The pattern: **Create → Modify → Append**

**Snippet:** Create a new element, customize it, and append it to the page.

```js
// 1. Create
const newParagraph = document.createElement('p');

// 2. Modify
newParagraph.textContent = 'Hello, world!';
newParagraph.classList.add('intro');

// 3. Append to a parent element
const container = document.querySelector('#container');
container.append(newParagraph);
```

### Removing Elements

**Snippet:** Remove an element from the DOM.

```js
const element = document.querySelector('.delete-me');
element.remove();
```

### Event Listeners

Listen for user interactions and run code when they happen:

**Snippet:** Handle a button click and inspect the event target.

```js
const button = document.querySelector('button');

button.addEventListener('click', (event) => {
  console.log('Button was clicked!');
  console.log('Clicked element:', event.target);
});
```

| Event Type  | When It Fires            |
| ----------- | ------------------------ |
| `click`     | Element is clicked       |
| `mouseover` | Mouse enters an element  |
| `mouseout`  | Mouse leaves an element  |
| `keydown`   | A key is pressed         |
| `keyup`     | A key is released        |
| `submit`    | A form is submitted      |
| `input`     | An input's value changes |

**The `event` object** is passed to your handler and contains useful info:
- `event.target` — the element that triggered the event
- `event.preventDefault()` — stops default behavior (like form submission)

### Handling Form Submissions

**Snippet:** Prevent default submit behavior, read form values, and reset the form.

```js
const form = document.querySelector('form');

form.addEventListener('submit', (event) => {
  // 1. Prevent page reload
  event.preventDefault();

  // 2. Get form data
  const formData = new FormData(event.target);
  const data = Object.fromEntries(formData);
  console.log(data); // { name: "Ben", email: "ben@example.com" }

  // 3. Reset the form
  event.target.reset();
});
```

**Alternative:** Access inputs directly via `form.elements`:

**Snippet:** Read specific input values by `name` from `form.elements`.

```js
const form = event.target;
const name = form.elements.name.value;
const email = form.elements.email.value;
```

### Common DOM Patterns

**Render a list from an array:**

**Snippet:** Convert array items into `<li>` elements and append them to a `<ul>`.

```js
const movies = ['Inception', 'The Matrix', 'Interstellar'];
const ul = document.querySelector('#movie-list');

movies.forEach((movie) => {
  const li = document.createElement('li');
  li.textContent = movie;
  ul.append(li);
});
```

**Toggle visibility:**

**Snippet:** Toggle a CSS class to show/hide UI.

```js
button.addEventListener('click', () => {
  const menu = document.querySelector('#menu');
  menu.classList.toggle('hidden');
});
```

---

## Servers, APIs, and HTTP

### Client, Server, and API Basics

- **Client**: your frontend app running in the browser
- **Server**: a program on another computer that receives requests and sends responses
- **API (Application Programming Interface)**: an interface that lets apps request or send data

### Endpoints

An **endpoint** is a specific URL for a specific resource/action.

- `https://dog.ceo/api/breeds/image/random` -> one random dog image
- `https://jsonplaceholder.typicode.com/users` -> a collection of users
- `https://jsonplaceholder.typicode.com/users/1` -> one specific user

**Snippet:** Send a `GET` request to an endpoint and handle the parsed response.

```js
fetch('https://jsonplaceholder.typicode.com/users/1')
  .then((response) => response.json())
  .then((user) => {
    console.log(user.name);
  });
```

*Note: this snippet does NOT include any error checking or handling.*

### HTTP Methods / Verbs

| Method          | Common Use           |
| --------------- | -------------------- |
| `GET`           | Read data            |
| `POST`          | Create/send new data |
| `PATCH` / `PUT` | Update existing data |
| `DELETE`        | Remove data          |

**Snippet:** Configure a `POST` request with a JSON body.

```js
const config = {
  method: 'POST',
  body: JSON.stringify({ name: 'Ada' }),
  headers: {
    'content-type': 'application/json',
    'accept': 'application/json'
  }
};

fetch('https://formspree.io/f/FORMSPREE_URL', config);
```

### Status Codes

Status codes describe how the server handled your request:

- `2xx` -> success (`200 OK`, `201 Created`)
- `4xx` -> client/request problem (`400 Bad Request`, `401 Unauthorized`, `404 Not Found`)
- `5xx` -> server problem (`500 Internal Server Error`)

In fetch code, inspect `response.status` and `response.ok` before you parse the body.

**Snippet:** Log status information from the `Response` object.

```js
fetch('https://jsonplaceholder.typicode.com/users/1')
  .then((response) => {
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    return response.json();
  })
  .then((data) => {
    console.log(data);
  })
  .catch((error) => {
    console.error(error.message);
  });
```

---

## Async

Asynchronous code starts a process that takes time and does not block the rest of your program.

### Key Concepts

- `Promise` objects can be `pending`, `resolved`, or `rejected`
- `fetch(url, config)` returns a Promise that resolves to a `Response`
- Use `.then()` / `.catch()` or `async` / `await` with `try` / `catch`

### `fetch()` 6-Step Pattern (`.then()` / `.catch()`)

**Snippet:** Full fetch flow: (1) request, (2) handle the promise, (2) validate response, (3) parse JSON, (4) use data, (5) handle errors.

```js
// 1. send request
fetch('https://dog.ceo/api/breeds/image/random')
  .then((response) => { // 2. handle the promise
    // 3. validate the response
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    // 4. parse JSON
    return response.json();
  })
  .then((responseBody) => {
    // 5. do something with the data
    console.log(responseBody);
    const dogImageSrc = responseBody.message;
  })
  .catch((err) => { // 6. handle errors
    console.error(`An error occurred: ${err.message}`);
  });
```

### `async` / `await` Version

**Snippet:** Same fetch flow using `async` / `await` and standardized `{ data, error }` returns.

```js
const getUsers = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');

    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }

    const data = await response.json();
    return { data, error: null };
  } catch (error) {
    return { data: null, error };
  }
};
```
