# The Document Object Model (DOM) API

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-2-0-dom)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [What is the DOM?](#what-is-the-dom)
- [The Chrome Developer Tools](#the-chrome-developer-tools)
- [The `document` object](#the-document-object)
  - [Selecting Elements in the DOM (Read)](#selecting-elements-in-the-dom-read)
  - [Modifying Elements in the DOM (Update / Delete)](#modifying-elements-in-the-dom-update--delete)
  - [Creating Elements (Create)](#creating-elements-create)
- [Linking JS files to HTML](#linking-js-files-to-html)
  - [Variables are Added to the Global Namespace](#variables-are-added-to-the-global-namespace)
    - [Option 1 — Use an IIFE (the old way)](#option-1--use-an-iife-the-old-way)
    - [Option 2 — Use Modules (the modern way)](#option-2--use-modules-the-modern-way)

## Key Concepts

* **DOM (Document Object Model)** — a representation of the HTML structure of a website in a JavaScript object format. The browser automatically generates the DOM when loading a webpage.
* **Chrome Developer Tools** — tools accessible via F12 or right-click > Inspect that allow us to interact with the source code of the page.
  * The **Elements** tab lets you view and manually manipulate the DOM.
  * The **Console** tab lets you view `console.log` messages and dynamically manipulate the DOM.
* **`document` object** — the DOM packaged in an object. Its properties allow us to access various elements of the DOM.
* **Node** — each element in the document tree. Each node has a `.children` array.
* **CRUD operations** — the four types of operations we can perform on the DOM:
  * **Create** — add new elements using `document.createElement()`
  * **Read** — find existing elements using `querySelector` or `querySelectorAll`
  * **Update** — modify existing elements (e.g., change `textContent`, `id`, `classList`)
  * **Delete** — remove elements using `.remove()`
* **Modules** — JavaScript files loaded with `type="module"` that enable ES6 `import`/`export` syntax and do not add variables to the global namespace.

## What is the DOM?

The DOM is the **Document Object Model**. It is representation of the HTML structure of your website in a JavaScript object format.

For example, consider this HTML:

```html
<h3 id="main-list-heading">Wow a list!</h3>
<ul id='my-list'>
  <li class="special-item">Here's an item in the main list</li>
  <li>Oh wow another one in main list</li>
  <li>And main list item</li>
</ul>
```

The DOM would take the elements of this HTML structure and turn them into objects! (_This is not actually happening in your code. This is just to demonstrate the idea._)

```js
// <h3 id="main-list-heading">Wow a list!</h3>
const h3 = { 
  id: "main-list-heading", 
  textContent: "Wow a list!" 
}

/* 
<ul id='my-list'>
  <li class="special-item">Here's an item in the main list</li>
  <li>Oh wow another one in main list</li>
  <li>And main list item</li>
</ul>
*/
const ul = {
  id: "my-list",
  children: [
    {
      className: 'special-item',
      textContent: "here's an item in the main list"
    },
    {
      textContent: "Oh wow another one in main list"
    },
    {
      textContent: "And main list item"
    }
  ]
}
```

The browser automatically generates this document object model for us! As web developers, we can use this DOM to do so much!

## The Chrome Developer Tools

Open with f12 or by right-clicking and selecting _inspect_.

The Chrome Developer Tools allow us to interact with the source code of the page.

* Use the _Elements_ tab to view and manually manipulate the DOM
* Use the _Console_ tab to view `console.log` messages printed from the JS and to dynamically manipulate the DOM

## The `document` object

The `document` object _is_ the DOM packaged in an object. The properties of the object allow us to access various elements of the DOM. Each element is a **node** in the document tree and each node has a `.children` array.

Open the Console tab for this page and enter these expressions to see the objects returned:

```js
document
document.children // [<html>]
document.children[0]              
document.children[0].children // [<head>, <body>]
document.children[0].children[0]  
document.children[0].children[1]
```

The `document` object also has methods that allow us to perform CRUD operations on the DOM:

* **C**reate new elements (create new "nodes" in the tree)
* **R**ead (find or "query for") existing elements
* **U**pdate existing elements
* **D**elete existing elements

### Selecting Elements in the DOM (Read)

There are many ways to find an Element in the DOM, but `querySelector` is the most flexible. It uses CSS selector syntax

```js
// returns the first h2 tag Element in the document
document.querySelector('h2');

// returns the all h2 tag Element in the document
document.querySelectorAll('h2');

// returns the first Element with the class font-semibold
document.querySelector('.font-semibold');

// returns all Elements with the class font-semibold
document.querySelectorAll('.font-semibold');

// get the Element with the id what-is-the-dom
document.querySelector('#what-is-the-dom');

// get the second span Element inside a code Element
document.querySelector("code > span:nth-child(2)")
```

It is important to note that `querySelectorAll` returns a `NodeList` which is NOT an array. You can use bracket notation but you can't use normal Array methods.

```js
// codeBlocks is a `NodeList` which is like an array in many ways
const codeBlocks = document.querySelectorAll('code');

// We can access values with bracket notation
codeBlocks[0]

// forEach works too!
codeBlocks.forEach((block) => {
  block.style.backgroundColor = 'lightblue';
})

// But other methods dont... --> ERROR: codeBlocks.slice is not a function
codeBlocks.slice(0, 2);

// We can spread the contents into an Array first
[...codeBlocks].slice(0,5).forEach((block) => {
  block.style.backgroundColor = 'lightgreen';
})
```

More Reading: [w3Schools](https://www.w3schools.com/js/js_htmldom_elements.asp)

### Modifying Elements in the DOM (Update / Delete)

Once an Element is grabbed from the DOM, we can modify it, and even delete it!

```js
const heading = document.querySelector('h1');
heading.textContent = 'The DOOOOM!';
heading.id = 'blahblah';
heading.classList.add('hello');
heading.classList.remove('font-bold')

// we don't always need to store the element in a variable to do something with it
document.querySelector("h1").remove();
```

More Reading: [w3Schools](https://www.w3schools.com/js/js_htmldom_html.asp)

### Creating Elements (Create)

Using the DOM API to dynamically create elements is one of the most powerful ways we can use it!

The pattern:

1. **Create**: `const newEl = document.createElement('div')`
2. **Modify**: add an id, class, and text, whatever
3. **Add**: `parentEl.append(newEl)`

```js
// 1. Create
const newP = document.createElement('p');

// 2. Modify
newP.innerText = 'i love coding!';
newP.style.backgroundColor = 'orange';
newP.classList.add('font-bold');

// 3. Add
const body = document.querySelector('body'); // the parent
body.append(newP);
```

You can also insert HTML directly using `.innerHTML` but you should be very careful about doing this. Only ever do this if the content you are adding is hard-coded (not user-generated).

```js
document.querySelector('body').innerHTML = `
  <li>coding</li>
  <li>basketball</li>
  <li>soccer</li>
`;
```

## Linking JS files to HTML

Playing around with the `document` object in the Console shows us the power of the DOM API.

If we want to utilize this functionality in our own websites, we need to link a `.js` file to our HTML.

The most straightforward way to do this is to put a `script` tag at the bottom of your HTML document, typically at the end of the `body`, after the Elements of the document have been added to the page.

```html
<body>
  <!-- Other html code must be added to the page before our script -->
  <!-- Put this just before the end of the body -->
  <script src="./index.js"></script>
</body>
```

In that `index.js` file, place a `console.log` statement and view it in the _Console_ tab of the Chrome Developer Tools each time you reload the webpage.

Try the following code:

```js
document.querySelector("#main-heading").textContent = "Hey!!!"

const body = document.querySelector('body')
body.style.backgroundColor = 'gold';
body.style.color = 'royalblue';
```

**Q: Why does the `script` tag need to go at the end of the body?**

This lets us do some really cool stuff, like dynamically creating elements from an array of data!

```js
const addMovies = (movies) => {
  const moviesList = document.querySelector('#movies-list')

  movies.forEach((movie) => {
    const li = document.createElement('li');
    const thumbnailImg = document.createElement('img');
    const titleYearH3 = document.createElement('h3');
    const genresP = document.createElement('p');

    thumbnailImg.src = movie.thumbnail;
    thumbnailImg.alt = `Movie poster for ${movie.title}`;
    titleYearH3.textContent = `${movie.title} (${movie.year})`
    genresP.textContent = movie.genres.join(', ');

    li.append(thumbnailImg, titleYearH3, genresP);
    moviesList.append(li);
  });
}
```

The code above, creates the following `li` structure for every `movie` in the `movies` array:

```html
<li>
  <h3>The Grudge (2020)</h3>
  <img src="thumbnail.jpg" alt="Movie poster for The Grudge">
  <p>Horror, Supernatural</p>
</li>
```

### Variables are Added to the Global Namespace

When loading multiple `.js` files with `script` tags, variables declared are added to the **global namespace**. This just means that they are available in all subsequent `.js` files.

```html
<body>
  <!-- if movies.js declares a variable called movies -->
  <script src="./movies.js"></script>
  <!-- that value is accessible inside of index.js -->
  <script src="./index.js"></script>
</body>
```

Back in the day, this was a useful feature as it let us keep our files separate but still be able to interact with each other since exporting and importing values wasn't invented yet.

<details>

<summary><strong>Q: What are the risks of adding variables to the global namespace?</strong></summary>

Adding variables to the global namespace is not ideal as it limits our ability to keep our files modular, leads to unexpected behavior, and makes debugging incredibly difficult.

Imagine you have an array stored in a variable named `data` in one file. In another file you write `data.sort()`, thinking that you are modifying a local variable but instead you end up modifying the `data` variable in the global namespace. In the first file, all of a sudden, your data is sorted unexpectedly without any logic that would explicitly do so in that same file.

</details>

To avoid adding variables to the global namespace, there are a couple of things we can do.

#### Option 1 — Use an IIFE (the old way)

One option is to always wrap the entire file in a top-level Immediately Invoked Function Expression (IIFE). An IIFE is an anonymous function that is immediately invoked. Since it is anonymous, there is no function name added to the global namespace. It is immediately invoked allowing the code inside to be executed.

```js
(() => {
  const data = [];
})()
```

But then we lose the ability to share variables across files. If we want to share values across files, we need to be able to export and import values.

#### Option 2 — Use Modules (the modern way)

To enable exporting and importing, we can turn our file into a module by adding the `type="module"` attribute to the `script` tag:

```html
<body>
  <script type="module" src="./movies.js"></script>
  <script type="module" src="./index.js"></script>
</body>
```

This enables ES6 importing and exporting syntax:

{% code title="movies.js" %}
```javascript
const movies = [
  {},
  {},
  {},
]
export default movies;
```
{% endcode %}

Above, we use the `export default` syntax to export the `movies` variable from `movies.js`. Below, we import that value using the `import...from` syntax

{% code title="index.js" %}
```javascript
import movies from './movies.js';
```
{% endcode %}

If we try this, we will end up with an error in our Console:

```
Access to script at 'file:///home/file/path/to/your/director/movies.js' from origin 'null' has been blocked
by CORS policy: Cross origin requests are only supported for protocol schemes: http, data, chrome,
chrome-extension, chrome-untrusted, https.
```

This is because of an unfortunate fact: **browsers simply do not allow modules to be used over the file:// protocol**. In order to use modules, we need to serve the modules via the `http://` protocol.

**Doing so means we need to turn our computer into a server!**

But have no fear, this is actually quite easy. Just download the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) VS Code extension. A "Go Live" button will appear in the bottom right hand corner of your IDE. Just open the file you want to serve and click "Go Live" and voila — the file will be served via the `http://` protocol instead of `file://`!

![The Live Server extension makes it easy to serve your HTML files via HTTP](../.gitbook/assets/live-server.png)
