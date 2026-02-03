# Web Development Cheat Sheet

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
- [Async](#async)

## DOM (Document Object Model)

The DOM is a JavaScript representation of your HTML. It lets you select, modify, create, and delete elements on the page.

### Linking JavaScript to HTML

Add a `<script>` tag at the end of your `<body>`:

```html
<body>
  <!-- Your HTML content -->
  <script src="./index.js"></script>
</body>
```

For ES6 modules (import/export), add `type="module"`:

```html
<script type="module" src="./index.js"></script>
```

**Note:** Modules require a server (use the VS Code Live Server extension).

### Selecting Elements

Use `document.querySelector()` with CSS selector syntax:

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

```js
const allButtons = document.querySelectorAll('button');

// Loop through with forEach
allButtons.forEach((btn) => {
  console.log(btn);
});
```

### Modifying Elements

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

```js
const element = document.querySelector('.delete-me');
element.remove();
```

### Event Listeners

Listen for user interactions and run code when they happen:

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

```js
const form = event.target;
const name = form.elements.name.value;
const email = form.elements.email.value;
```

### Common DOM Patterns

**Render a list from an array:**

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

```js
button.addEventListener('click', () => {
  const menu = document.querySelector('#menu');
  menu.classList.toggle('hidden');
});
```

---

## Async

Coming soon...