# Intro to the Document Object Model

**Table of Contents**

- [The Chrome Developer Tools](#the-chrome-developer-tools)
- [Linking JS files to HTML](#linking-js-files-to-html)
- [What is the DOM?](#what-is-the-dom)
- [The `document` object](#the-document-object)
  - [Selecting Single Elements in the DOM (Read)](#selecting-single-elements-in-the-dom-read)
  - [Modifiying Elements in the DOM (Update / Delete)](#modifiying-elements-in-the-dom-update--delete)
  - [Selecting Multiple Elements (Read)](#selecting-multiple-elements-read)
  - [Creating Elements (Create)](#creating-elements-create)

An intro to the DOM

[Slides](https://docs.google.com/presentation/d/1_4N1KPajA6HE1EPrmQ5n8ruOYVSNBp5WYl3BcPClc-U/edit?usp=sharing)


## The Chrome Developer Tools

Open with <kbd>f12</kbd> or by right-clicking and selecting _inspect_.

The Chrome Developer Tools allow us to interact with the source code of the page. We can
* Use the _Elements_ tab to view and manipulate the DOM
* Use the _Console_ tab to view `console.log` messages printed from the JS
* Use the _Network_ tab to view loaded assets

## Linking JS files to HTML

There are lots of ways to add a script we'll go over later, for now this is easiest

```html
<body>
  <!-- other html code -->
  <!-- Put this just before the end of the body -->
  <script src="./index.js"></script>
</body>
```

In that `index.js` file, place a `console.log` statement and view it in the _Console_ tab of the Chrome Developer Tools each time you reload the webpage.

Try the following code:

```js
const fruits = [
  { name: 'apple', color: 'red' },
  { name: 'banana', color: 'yellow' },
  { name: 'mango', color: 'orange' },
];

// Use the arrows to pop open objects
console.log('click to expand!', fruits);

// can do tricks the node console can
console.table(fruits);
```

**Q: Why does the `script` tag need to go at the end of the body?**

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

The DOM would take the elements of this HTML structure and turn them into objects! (*This is not actually happening in your code. This is just to demonstrate the idea.*)

```js
const h3 = { 
  id: "main-list-heading", 
  textContent: "Wow a list!" 
}

const ul = {
  id: "my-list",
  children: [
    {
      class: 'special-item',
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

Let's dive in.

## The `document` object

The `document` object _is_ the DOM packaged in an object. The properties of the object allow us to access various elements of the DOM. Each element is a **node** in the document tree and each node has a `.children` array.

```js
console.log(document);
console.log(document.body);
console.log(document.body.children);
console.log(document.body.children[0]);
```

The `document` object also has methods that allow us to perform CRUD operations on the DOM:
* **C**reate new elements (create new "nodes" in the tree)
* **R**ead (find or "query for") existing elements
* **U**pdate existing elements
* **D**elete existing elements

### Selecting Single Elements in the DOM (Read)

There are many ways to find an Element in the DOM, but `querySelector` is the most flexible. It uses CSS selector syntax

```js
// returns the first p tag Element in the document
const firstP = document.querySelector('p');

// get the first Element belonging to the special-item class
const special = document.querySelector('.special-item');

// get the Element with the id main-list-heading
const heading = document.querySelector('#main-list-heading');

// get the first li Element inside the ol
const firstOrderedListItem = document.querySelector("ol > li:nth-child(1)")
```

More Reading: [w3Schools](https://www.w3schools.com/js/js_htmldom_elements.asp)

### Modifiying Elements in the DOM (Update / Delete)

Once an Element is grabbed from the DOM, we can modify it, and even delete it!

```js
const heading = document.querySelector('#main-list-heading');
heading.innerText = 'hello world!';
heading.id = 'blahblah';
heading.classList = "vivid purple";

// we don't always need to store the element in a variable to do something with it
document.querySelector("ol > li:nth-child(1)").remove();
```

More Reading: [w3Schools](https://www.w3schools.com/js/js_htmldom_html.asp)

### Selecting Multiple Elements (Read)

Sometimes we may want to apply a change to multiple elements at once. Use `document.querySelectorAll()` to grab multiple elements

```js
// get all li Elements in a NodeList
const listItems = document.querySelectorAll('li');

// get all Elements with the class special-item
const specialItems = document.querySelectorAll('.special-item');
```

`querySelectorAll` returns a `NodeList` which is NOT an array. You can use bracket notation but you can't use normal Array methods.

```js
// forEach does work...
const listItems = document.querySelectorAll('#recipe-list > li');
listItems.forEach((listItem) => {
  listItem.classList.add('recipe-step')
})

// But other methods dont... --> ERROR: listItems.slice is not a function
listItems.slice(0, 2);

// Spread into an Array first!
const firstThree = [...listItems].slice(0,2)
```

### Creating Elements (Create)

The pattern:
1. **Create**: `const newEl = document.createElement('div')`
2. **Modify**: add an id, class, and text, whatever
3. **Add**: `parentEl.append(newEl)`

```js
// 1. Create
const newLi = document.createElement('li');

// 2. Modify
newLi.innerText = 'i love coding!';
newLi.classList.add('special-item');

// 3. Add
const ul = document.querySelector('ul'); // the parent
ul.append(newLi);
```

You can also insert HTML directly using `.innerHTML` but you should be very careful about doing this. Only ever do this if the content you are adding is hard-coded (not user-generated).

```js
const ul = document.querySelector('ul');

ul.innerHTML = `
  <li>coding</li>
  <li>basketball</li>
  <li>soccer</li>
`;
```
