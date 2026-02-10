# 1. The DOM & Event Handling

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-1-interactive-pages)!
{% endhint %}

**Table of Contents**

* [Key Concepts](1-intro-to-interactive-pages.md#key-concepts)
* [What Can JavaScript Do?](1-intro-to-interactive-pages.md#what-can-javascript-do)
* [The Document Object Model (DOM)](1-intro-to-interactive-pages.md#the-document-object-model-dom)
  * [Selecting Elements with querySelector](1-intro-to-interactive-pages.md#selecting-elements-with-queryselector)
* [Linking JavaScript to HTML](1-intro-to-interactive-pages.md#linking-javascript-to-html)
* [Events](1-intro-to-interactive-pages.md#events)
  * [addEventListener: Listen and Handle](1-intro-to-interactive-pages.md#addeventlistener-listen-and-handle)
  * [Event Types and Handling Multiple Events](1-intro-to-interactive-pages.md#event-types-and-handling-multiple-events)
  * [The Event Object](1-intro-to-interactive-pages.md#the-event-object)
  * [Event Bubbling (Propagation) and `currentTarget`](1-intro-to-interactive-pages.md#event-bubbling-propagation-and-currenttarget)
* [Challenge: Put It All Together](1-intro-to-interactive-pages.md#challenge-put-it-all-together)

## Key Concepts

* **DOM (Document Object Model)** — the tree-like structure of JavaScript objects that the browser creates when it loads an HTML page. It maps the hierarchical relationships between elements.
* **`<script src="file.js">`** — links a JavaScript file to an HTML page. It should be placed at the end of the `<body>` to ensure all elements exist before the script runs.
* **Event** — a user interaction that the browser can detect, such as a click, mouse movement, key press, or form submission. **Event Bubbling (Propagaion)** — an event that occurs on an element can heard by all ancestors of that element.

**Key Syntax**

* **`document` object** — the root object at the top of the DOM tree; our entry point for accessing and manipulating elements.
* **`document.querySelector(selector)`** — returns the first element matching the CSS selector (or `null` if none).
* **`document.body`** — a shortcut reference to the `<body>` element.
* **`element.textContent`** — gets or sets the text content of an element (no HTML, just text).
* **`element.style.property`** — gets or sets an inline style on an element (e.g. `element.style.backgroundColor = 'aqua'`).
* **`element.classList.add(className)`** — adds a CSS class to an element.
* **`element.classList.remove(className)`** — removes a CSS class from an element.
* **`element.classList.toggle(className)`** — adds the class if it’s missing, removes it if it’s present.
* **`element.remove()`** — removes the element from the DOM.
* **`element.addEventListener(eventType, handler)`** — registers an event handler function for a given event type on an element.
* **`event` object** — an object automatically passed to event handlers containing information about the event.
* **`event.target`** — the element that originally triggered the event.
* **`event.currentTarget`** — the element that is currently handling the event (the one the listener is attached to).
* **`event.type`** — the type of event that occurred (e.g. `'click'`, `'keydown'`).
* **`event.key`** — for keyboard events, the actual key pressed (e.g. `'a'`, `' '`).
* **`event.code`** — for keyboard events, the physical key on the keyboard (e.g. `'Space'`, `'KeyA'`).

## What Can JavaScript Do?

You've spent the last few weeks building beautiful, well-structured websites with HTML and CSS. But so far, your pages have been **static**—they look great, but they don't _do_ anything when users interact with them.

**JavaScript changes everything.**

With JavaScript, you can make your pages respond to user actions:

* Click a button → something happens
* Type in a search box → results filter in real-time
* Hover over an image → it zooms in
* Submit a form → data gets saved

This is what we mean by **interactive web applications**. Let's see how it works.

In this lesson we'll first learn how to use JavaScript to manually manipulate HTML elements before learning how to make elements interactive.

## The Document Object Model (DOM)

Consider this HTML structure:

{% code title="0-balloon-pop/index.html" overflow="wrap" lineNumbers="true" %}
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Balloon Pop</title>
  </head>
  <body>
    <button id="blow-btn">BLOW 💨</button>
    <p id="balloon">🎈</p>
  </body>
</html>
```
{% endcode %}

When a browser loads an HTML page, it converts every single HTML element into a JavaScript object and organizes them into a structure called the **Document Object Model (DOM)**. The DOM is a "tree-like" structure which maps the hierarchical relationship between elements.

![The DOM turns your HTML elements into a tree-like structure of JavaScript objects](../.gitbook/assets/the-dom.png)

The key insight: **if elements are objects, we can modify and interact with them using JavaScript!** We can change their text, their styles, their classes—anything.

The object at the top of this tree, `document`, is our entry point to interacting with these objects.

### Selecting Elements with querySelector

**TODO:** Open the `0-balloon-pop/index.html` file in your browser and open the Chrome DevTools. In the DevTools console, we can write JavaScript code that will interact directly with the DOM.

To grab a specific HTML element object from the DOM, we use the `document.querySelector()` method. It uses the same CSS selector syntax you already know:

```js
// Find the first element with id="blow-button"
document.querySelector('#blow-button');

// Find the first button element
document.querySelector('button');

// Find the first element with class="highlight"
document.querySelector('.highlight');

// Find the first button inside of body
document.querySelector('body button');
```

Open your browser's DevTools (F12 or right-click → Inspect), go to the Console tab, and try selecting elements on any webpage using this method.

Once you have a reference to an element, you can read and modify its properties. You can even completely remove the element from the DOM:

```js
// Store the object in a variable to reference it multiple times
const balloon = document.querySelector('#balloon');

// Read properties
console.log(balloon.textContent);  // 🎈
console.log(balloon.id);           // "balloon"

// Modify properties
balloon.textContent = "💥";               // Change the text
balloon.style.backgroundColor = "aqua";   // Change the background color
balloon.style.fontSize = "32px";          // Change the font size
balloon.classList.add("heading");         // Add a CSS class

// Removing the element completely
balloon.remove();
```

Try these modifications one line at a time in your DevTools console!

## Linking JavaScript to HTML

Using JavaScript in the DevTools console is great for testing. But every time we refresh the page, all of our work is lost and we have to write it again.

Instead, we can make a JavaScript file and have our HTML pages automatically run that file when the page loads.

For example, the `0-balloon-pop/index.js` file contains the following code:

{% code title="0-balloon-pop/index.js" lineNumbers="true" %}
```js
const balloon = document.querySelector("#balloon");

balloon.style.fontSize = "16px"

console.log(`The fontSize is ${balloon.style.fontSize}`);
```
{% endcode %}

To run this file when our HTML page loads, add a `<script src="index.js">` tag at the **end of the body**:

{% code title="0-balloon-pop/index.html" lineNumbers="true" %}
```html
<body>
  <h1 class="heading">Button Pop</h1>
  <button id="blow-btn">BLOW 💨</button>
  <p id="balloon">🎈</p>

  <!-- TODO: Add your script at the end of the body -->
  <script src="index.js"></script>
</body>
```
{% endcode %}

Now, when your browser loads this HTML page, it will automatically run the JavaScript file after the body content has loaded.

<details>

<summary><strong>Q: Why does the <code>&#x3C;script></code> tag need to go at the end of the body?</strong></summary>

JavaScript runs as soon as it loads. If your script tries to select an element that hasn't been added to the page yet, `querySelector` will return `null` and your code will break.

Try it out! The script will try to reference the balloon element before it exists.

By placing the `<script>` tag at the end of the body, you ensure all HTML elements have been loaded into the DOM before your JavaScript tries to access them.

</details>

## Events

The most exciting part of making web applications is when we start adding interactivity by responding to **events**. An event is something that the user does on the page:

1. clicking on a button
2. hovering over an element
3. scrolling on the page
4. pressing a key on their keyboard
5. submitting a form.

Using JavaScript, we can decide how the page will react in response to these events occurring.

### addEventListener: Listen and Handle

Adding interactivity requires telling our browser three things:

1. The **element** we want to be interactive
2. The event it will **"listen"** for
3. What to do to **"handle"** that event

We do this all with the with the `addEventListener()` method:

```js
// 1. Select the button to make it interactive
const button = document.querySelector('#blow-btn');

// 2. Listen for "click" events
button.addEventListener('click', () => {
  // 3. Handle the event
  balloon.style.fontSize = "32px";
});
```

Let's break the `addEventListener` method down:

```js
element.addEventListener(eventType, handlerFunction);
```

1. **`element`** — The HTML element that we want to be interactive
2. **`eventType`** — A string like `'click'`, `'mouseover'`, `'keydown'`
3. **`handlerFunction`** — A callback that runs when the event "fires" (when it occurs)

<details>

<summary><strong>Challenge 1: How can we double the font size every time we click the button?</strong></summary>

```js
const button = document.querySelector('#blow-btn');
const balloon = document.querySelector('#balloon');

let fontSize = 16;
button.addEventListener('click', () => {
  fontSize *= 2;
  balloon.style.fontSize = `${fontSize}px`;
});
```

</details>

<details>

<summary><strong>Challenge 2: How can make the balloon "pop" 💥 when the font size gets above 750px?</strong></summary>

```js
const button = document.querySelector('#blow-btn');
const balloon = document.querySelector('#balloon');

let fontSize = 16;
button.addEventListener('click', () => {
  fontSize *= 2;
  balloon.style.fontSize = `${fontSize}px`;
  if (fontSize > 750) {
    balloon.textContent = '💥';
    balloon.style.fontSize = '0px';
  }
});
```

</details>

### Event Types and Handling Multiple Events

There are many event types you can listen for:

| Event Type  | Triggered When...              |
| ----------- | ------------------------------ |
| `click`     | Element is clicked             |
| `dblclick`  | Element is double-clicked      |
| `mouseover` | Mouse moves onto element       |
| `mouseout`  | Mouse leaves element           |
| `mousemove` | Mouse moves while over element |
| `keydown`   | A key is pressed               |
| `keyup`     | A key is released              |
| `submit`    | A form is submitted            |
| `input`     | An input's value changes       |

You can find a complete list of events on [MDN](https://developer.mozilla.org/en-US/docs/Web/Events).

A single element can even have multiple event listeners for different types of events:

```js
// document.body is a shortcut to get the body element without querySelector
document.body.addEventListener('click', () => {
  console.log('The body was clicked!');
});

document.body.addEventListener('keydown', () => {
  console.log('A key was pressed!');
});
```

**Challenge: Add an event listener to the body that reacts whenever the mouse moves**

<details>

<summary><strong>Solution</strong></summary>

```js
document.body.addEventListener('mousemove', () => {
  console.log('The mouse moved!');
});
```

</details>

### The Event Object

When an event handler is called, it automatically receives an **event object** with information about what happened:

```js
// Add the event parameter to the event handler
document.body.addEventListener('click', (event) => {
  console.log(event.type);    // "click"
  console.log(event.target);  // The element that was clicked
  console.log('The body was clicked!');
});
```

The two most important properties are:

* **`event.type`** — The type of event that occurred (`'click'`, `'keydown'`, etc.)
* **`event.target`** — The element that triggered the event.

The `event` object has a ton of information that is specific to each type of event. It can be useful to print out the entire `event` object to see what is available.

**Q: Add the `event` parameter to the keydown listener and print it out. What properties tell us information about which key was pressed?**

```js
document.body.addEventListener('keydown', (event) => {
  console.log('A key was pressed!');
  console.log(event);
});
```

<details>

<summary><strong>Answer</strong></summary>

```js
document.body.addEventListener('keydown', (event) => {
  console.log(`You typed: ${event.key}`);
  console.log(`Key code: ${event.code}`);
});
```

* `event.key` (Recommended): Returns the string value of the character pressed, taking into account keyboard layout and modifier keys (e.g., "a", "A", "@").
* `event.code`: Identifies the physical key on the keyboard, regardless of language or modifier status (e.g., "KeyQ" for a QWERTY or AZERTY layout).

</details>

{% hint style="info" %}
**Tip:** When trying a new event type, always log the `event` object to see what properties are available!
{% endhint %}

### Event Bubbling (Propagation) and `currentTarget`

Take another look at this event listener.

```js
// Add the event parameter to the event handler
document.body.addEventListener('click', (event) => {
  console.log(event.type);            // "click"
  console.log(event.target);          // The element that was clicked
  console.log('The body was clicked!');
});
```

The `body` is the element that is listening for click events, right? Did you notice that you can click on elements _inside_ the body rather than the body itself and the event handler will still fire. The value of `event.target` will tell us exactly which element you clicked on.

This is called **event bubbling** (a.k.a. **event propagation**) and it is a feature of the DOM whereby an event that occurs on an element can heard by all ancestors of that element.

If you ever need to distinguish the element that triggered the event from the element that is listening for the event, you can use `event.currentTarget`:

```js
// Add the event parameter to the event handler
document.body.addEventListener('click', (event) => {
  console.log(event.type);            // "click"
  console.log(event.target);          // The element that was clicked
  console.log(event.currentTarget);   // The element listening for the event
  if (event.target === event.currentTarget) {
    console.log('The body was clicked!');
  }
});
```

## Challenge: Put It All Together

In `1-random-colors`, open the `index.html` file

{% code title="1-random-colors/index.html" %}
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Random Colors</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header>
    <h1>Random Colors</h1>
  </header>
  <main>
    <section id="instructions">
      <p>Press the Spacebar to change the colors!</p>
      <p>Press the buttons below to increase or decrease the text size</p>
    </section>
    <section id="buttons">
      <button id="increase-button">+</button>
      <button id="decrease-button">-</button>
    </section>
  </main>
  <footer>
    <p>Made with ❤️ at <a href="https://marcylabschool.org">Marcy Lab School</a></p>
  </footer>
  <!-- TODO: Load the index.js file with a script tag -->
</body>
</html>
```
{% endcode %}

First, load the script file in `index.js`.

Inside of `index.js` is a function that generates a random RGB color string:

{% code title="1-random-colors/index.js" overflow="wrap" lineNumbers="true" %}
```js
const generateRandomColor = () => {
  const r = Math.floor(Math.random() * 255);
  const g = Math.floor(Math.random() * 255);
  const b = Math.floor(Math.random() * 255);

  return `rgb(${r}, ${g}, ${b})`;
}

console.log(generateRandomColor());

document.body.style.backgroundColor = generateRandomColor();
```
{% endcode %}

Your task is to do the following:

1. Add an event listener that changes the background color of the body whenever the "space" key is pressed
2. Add an event listener to the `+` button that increases the font size of the `"#instructions"` section by `1px`.
3. Add an event listener to the `-` button that decreases the font size of the `"#instructions"` section by `1px`.

<details>

<summary><strong>Solution</strong></summary>

{% code title="1-random-colors/index.js" %}
```javascript
const generateRandomColor = () => {
  const colorOptions = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];
  const index = Math.floor(Math.random() * colorOptions.length);
  return colorOptions[index];
}

// add a keydown event listener to the body
document.body.addEventListener('keydown', (event) => {
  if (event.code === "Space") {
    document.body.style.backgroundColor = generateRandomColor();
  }
});

const increaseButton = document.querySelector('#increase-button');
const decreaseButton = document.querySelector('#decrease-button');
const instructions = document.querySelector('#instructions');

let fontSize = 16;
increaseButton.addEventListener('click', () => {
  fontSize += 1;
  instructions.style.fontSize = `${fontSize}px`;
})

decreaseButton.addEventListener('click', () => {
  fontSize -= 1;
  instructions.style.fontSize = `${fontSize}px`;
})
```
{% endcode %}

</details>
