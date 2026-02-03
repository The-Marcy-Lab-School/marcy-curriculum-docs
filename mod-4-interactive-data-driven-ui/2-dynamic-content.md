# Dynamic Content

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-2-dynamic-content)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Why Create Elements Dynamically?](#why-create-elements-dynamically)
- [The Create, Modify, Append Pattern](#the-create-modify-append-pattern)
  - [Appending Multiple Elements At Once](#appending-multiple-elements-at-once)
  - [Challenge: Add a list item when clicking a button](#challenge-add-a-list-item-when-clicking-a-button)
- [Rendering Data from Arrays](#rendering-data-from-arrays)
  - [Challenge: Rendering a list of movies](#challenge-rendering-a-list-of-movies)
- [Making Dynamic Content Interactive](#making-dynamic-content-interactive)
  - [The Problem: Adding Listeners to Dynamic Elements](#the-problem-adding-listeners-to-dynamic-elements)
  - [Event Bubbling (Propagation)](#event-bubbling-propagation)
  - [Event Delegation: The Solution](#event-delegation-the-solution)
  - [`element.closest`](#elementclosest)
- [Dataset: Storing Data On Elements](#dataset-storing-data-on-elements)
  - [Problem: Using the textContent to find the movie](#problem-using-the-textcontent-to-find-the-movie)
  - [Solution: `data-` Attributes and `dataset`](#solution-data--attributes-and-dataset)
  - [Challenge: Card Flip Game](#challenge-card-flip-game)
- [Additional Reading](#additional-reading)
  - [Selecting Multiple Elements with querySelectorAll](#selecting-multiple-elements-with-queryselectorall)
  - [innerHTML: A Shortcut with Risks](#innerhtml-a-shortcut-with-risks)
  - [`element.matches`](#elementmatches)
  - [Stopping Event Propagation](#stopping-event-propagation)
  - [Removing Event Listeners](#removing-event-listeners)

## Key Concepts

* **Create, Modify, Append** — a pattern for dynamically creating content using the `document.createElement()` and `element.append` methods.
* **Event propagation (bubbling)** — when an event occurs on an element, it "bubbles up" through all of its ancestors, allowing them to also detect the event.
* **Event delegation** — a pattern where a parent element handles events for its children, using `event.target` to determine which child triggered the event.

**Key Syntax**
* **`document.createElement(tagName)`** — creates a new HTML element of the specified type (e.g., `'div'`, `'li'`, `'img'`). The element is not yet on the page.
* **`parentElement.append(childElement)`** — adds an element as the last child of the parent element. You can append multiple elements at once: `parent.append(el1, el2, el3)`.
* **`event.currentTarget`** — the element that the event listener is attached to (the one handling the event).
* **`event.stopPropagation()`** — prevents an event from bubbling up to parent elements.
* **`element.matches(selector)`** — returns `true` if the element matches the given CSS selector. Useful for filtering events in delegation.
* **`element.closest(selector)`** — returns the nearest ancestor (or itself) that matches the selector. Useful for finding a parent container from a clicked child.
* **`document.querySelectorAll(selector)`** — returns a `NodeList` of all elements matching the CSS selector.
* **`NodeList`** — an array-like collection of elements. Supports `forEach` and bracket notation, but not all array methods like `map` or `filter`. Use `[...nodeList]` to convert to a true array.
* **`element.innerHTML`** — a property that gets or sets the HTML content inside an element. Convenient but risky with user-generated content (XSS vulnerability).
* **`element.removeEventListener(eventType, handler)`** — removes an event listener. Requires a reference to the original handler function.

## Why Create Elements Dynamically?

In the last lesson, we learned how to select existing elements and make them interactive. But what if the elements don't exist yet?

Consider these real-world scenarios:
- A todo list where users can add new items
- A shopping cart that shows items the user has added
- A search results page that displays matches based on user input
- A social media feed that loads new posts as you scroll

In all these cases, we can't hardcode the HTML—the content depends on **data** that may change. We need to create elements dynamically using JavaScript.

## The Create, Modify, Append Pattern

To add new elements to the page, follow this three-step pattern:

1. **Create** — use `document.createElement(tagName)` to make a new element
2. **Modify** — set its properties (`textContent`, `id`, `classList`, `src`, etc.)
3. **Append** — use `parentElement.append(newElement)` to add it to the page

```js
// 1. Create a new paragraph element
const newParagraph = document.createElement('p');

// 2. Modify its properties
newParagraph.textContent = 'A new paragraph!';
newParagraph.classList.add('highlight');
newParagraph.id = 'dynamic-paragraph';

// 3. Append it to a parent element
const container = document.querySelector('#container');
container.append(newParagraph);
```

**The element doesn't appear on the page until you append it.** Until then, it only exists in memory.

### Appending Multiple Elements At Once
You can also append multiple elements at once using `append`:

```js
// Grab the parent element
const ul = document.querySelector('#my-list');

// Create the list item elements
const li1 = document.createElement('li');
const li2 = document.createElement('li');
const li3 = document.createElement('li');

// Modify them
li1.textContent = '1';
li2.textContent = '2';
li3.textContent = '3';

// Append
ul.append(li1, li2, li3);
```

### Challenge: Add a list item when clicking a button

In the `index.html` file is a button above the list:

```html
<button id="add-item-btn">Add an Item</button>
<ul id="my-list">
  <!-- List items will be added here -->
</ul>
```

Make the button interactive such that when you click on it, a new list item is generated with the next number in the sequence (if the last number is 3, the next number should be 4, then 5 and so on).

**<details><summary>Solution</summary>**

```js
// Make a variable for the count (count = 0 if you're starting with an empty list)
let count = ul.children.length;

// Grab the button
const addItemBtn = document.querySelector("#add-item-btn");

// Add a click event listener
addItemBtn.addEventListener("click", () => {
  const li = document.createElement("li");  // Create
  count++;
  li.textContent = count; // Modify
  ul.append(li); // Append
});
```

</details>

## Rendering Data from Arrays

We can see the power of this create-modify-append pattern when we have an array of data that we want to generate our HTML content from.

Consider this array:

```js
const friends = ['Alice', 'Bob', 'Carol', 'David'];
```

**Q: How would you use it to create this HTML structure?**

```html
<ul id="friends-list">
  <li>Alice</li>
  <li>Bob</li>
  <li>Carol</li>
  <li>David</li>
</ul>
```

We just use a loop to repeat the create → modify → append pattern:

```js
const friends = ['Alice', 'Bob', 'Carol', 'David'];

// 1. Grab the parent element
const ul = document.querySelector('#friends-list');

// 2. For each friend, create → modify → append
friends.forEach((friend) => {
  const li = document.createElement('li');
  li.textContent = friend;
  ul.append(li);
});
```

### Challenge: Rendering a list of movies

Now consider a more complex array, like an array of movies:

```js
const movies = [
  {
    title: 'The Matrix',
    year: 1999,
    genres: ['Sci-Fi', 'Action'],
    src: 'https://upload.wikimedia.org/wikipedia/en/d/db/The_Matrix.png',
    funFact: 'The film popularized the "bullet time" visual effect.'
  },
  // more movie objects...
];
```

**Q: How would you use this data to create the following HTML?**

```html
<ul id="movies-list">
  <li>
    <img src="https://upload.wikimedia.org/..." alt="Movie poster for The Matrix">
    <h3>The Matrix (1999)</h3>
    <p>Sci-Fi, Action</p>
  </li>
  <!-- more list items... -->
</ul>
```

Ask yourself:
* What is the parent element?
* What elements do I need to **create**?
* How will I **modify** those elements?
* What elements will I need to **append**?

Note: we aren't using the fun fact yet!

**<details><summary>Solution</summary>**

The pattern is the same:
1. Grab the parent ul
2. Create the needed elements (`li`, `img`, `h3`, `p`)
3. Modify them
4. Append to assemble the elements

```js
const movies = [
  {
    title: 'The Matrix',
    year: 1999,
    genres: ['Sci-Fi', 'Action'],
    src: 'https://upload.wikimedia.org/wikipedia/en/d/db/The_Matrix.png',
    funFact: 'The film popularized the "bullet time" visual effect.'
  },
  /* more movies... */
]

// Grab the movies list
const moviesList = document.querySelector('#movies-list');

movies.forEach((movie) => {
  // Create the list item and its content (no fun fact yet)
  const li = document.createElement('li');
  const img = document.createElement('img');
  const titleH3 = document.createElement('h3');
  const genresP = document.createElement('p');

  // Modify
  img.src = movie.src;
  img.alt = `Movie poster for ${movie.title}`;
  titleH3.textContent = `${movie.title} (${movie.year})`;
  genresP.textContent = movie.genres.join(', ');

  // Append the content to the li and the li to the moviesList
  li.append(img, titleH3, genresP);
  moviesList.append(li);
});
```

</details>

## Making Dynamic Content Interactive

We saw how to make a button interactive such that it can dynamically create an element. 

But what about also making the new elements that we create interactive? For example, let's say we want to highlight a movie when clicked.

### The Problem: Adding Listeners to Dynamic Elements

One approach is to add an event listener to each element as we create it. When that `li` is clicked, we'll toggle on/off the `"highlight"` class:

```js
// Grab the parent
const moviesList = document.querySelector('#movies-list');

movies.forEach((movie) => {
  // Create the list item and its content
  const li = document.createElement('li');
  const img = document.createElement('img');
  const titleH3 = document.createElement('h3');
  const genresP = document.createElement('p');

  // Modify
  img.src = movie.src;
  img.alt = `Movie poster for ${movie.title}`;
  titleH3.textContent = `${movie.title} (${movie.year})`;
  genresP.textContent = movie.genres.join(', ');

  // Append the content to the li and the li to the moviesList
  li.append(img, titleH3, genresP);
  moviesList.append(li);

  // Add a click listener to each li that we create
  li.addEventListener('click', () => {
    li.classList.toggle('highlight');
  });
});
```

This works, but it is inefficient. Why? Consider this question:

**<details><summary>Q: How many event listeners have we created?</summary>**

Every element has its own listener:
* If we have three list items, we will have three separate event listeners.
* If we have a million list items, we will have a million event listeners.

It is more efficient to have a single event listener on the *parent* element (the `ul`) and have it handle click events on any of its children.

This is possible through **event bubbling (propagation)** and **event delegation**
</details>

### Event Bubbling (Propagation)

When an event occurs on an element, it doesn't just fire on that element—it "bubbles up" through all of its ancestors:

```html
<div id="outer">
  <div id="inner">
    <button id="btn">Click me</button>
  </div>
</div>
```

```js
document.querySelector('#btn').addEventListener('click', () => {
  console.log('Button clicked!');
});

document.querySelector('#inner').addEventListener('click', () => {
  console.log('Inner div heard the click!');
});

document.querySelector('#outer').addEventListener('click', () => {
  console.log('Outer div heard the click!');
});
```

If you click the button, **all three handlers fire** (from innermost to outermost):

```
Button clicked!
Inner div heard the click!
Outer div heard the click!
```

The event "bubbles up" from the button, through `#inner`, to `#outer`.

When handling a bubbled event, you can use two properties to understand what happened:
- **`event.target`** — the element that originally triggered the event (the button)
- **`event.currentTarget`** — the element handling the event (where the listener is attached)

```js
document.querySelector('#outer').addEventListener('click', (event) => {
  console.log('Outer div heard the click!');
  console.log('Target:', event.target.id);         // "btn"
  console.log('CurrentTarget:', event.currentTarget.id);  // "outer"
});
```

### Event Delegation: The Solution

Event delegation uses bubbling to our advantage. Instead of adding listeners to each child element, we add **one listener to the parent** and use the `event.target` to tell us which child was clicked:

```js
// The parent (moviesList) handles all clicks that bubble up from the children
moviesList.addEventListener('click', (event) => {
  // event.target will be the child that was clicked
  event.target.classList.toggle('highlight');
});
```

`event.target` will be the element inside of our movies list that was clicked, but that could be *any* element: the `p`, the `h3`, the `li`, or even the `ul` itself. Whatever we click on will be highlighted.

### `element.closest`

Instead, we want to highlight the entire `li` element that was clicked. This is where the `element.closest()` method comes in:

```js
// The parent (moviesList) handles all clicks that bubble up from the children
moviesList.addEventListener('click', (event) => {
  // Find the li closest to the element that was clicked
  const clickedLi = event.target.closest('li');
  
  clickedLi.classList.toggle('highlight');
});
```

The `closest()` method is key here—it finds the nearest ancestor (or the element itself) that matches the selector. This handles cases where the user clicks on the `<h3>` or `<p>` inside the `<li>`.

## Dataset: Storing Data On Elements

Look in the HTML `1-rendering-data/index.html` file and you'll see an empty `#fun-fact` paragraph element.

```html
<section>
  <h2>Movies List</h2>
  <ul id="movies-list">
    <!-- Movie items will be rendered here -->
  </ul>
  <p id="fun-fact">
    <!-- Fun fact will be dynamically set here -->
  </p>
</section>
```

Each movie object in the `movies` array has a fun fact. Suppose that when a user clicks on a movie list item, we want to display the corresponding movie's fun fact in that element.

```js
const movies = [
  {
    title: 'The Matrix',
    year: 1999,
    genres: ['Sci-Fi', 'Action'],
    src: 'https://upload.wikimedia.org/wikipedia/en/d/db/The_Matrix.png',
    funFact: 'The film popularized the "bullet time" visual effect.'
  },
  // more movies...
]
```

### Problem: Using the textContent to find the movie

Our event handler has no easy way to access that data. We'd have to jump through quite a few hoops, using the text content of the elements inside the movie list item to find the right object:

```js
// 1. Grab the #fun-fact element
const funFact = document.querySelector("#fun-fact");

moviesList.addEventListener('click', (event) => {
  const clickedLi = event.target.closest('li');
  clickedLi.classList.toggle('highlight');

  // 2. look inside the clickedLi for the h3 that has the movie title and year
  const movieTitleAndYear = clickedLi.querySelector('h3').textContent;

  // 3. find the movie object with the corresponding title
  const movieObject = movies.find((movie) => movieTitleAndYear.includes(movie.title))

  // 4. display the fun fact
  funFact.textContent = `${movieObject.title} fun fact: ${movieObject.funFact}`;
});
```

### Solution: `data-` Attributes and `dataset`

`data-` is an attribute that can be added to any element to store custom data on the element:

```html
<li data-fun-fact='The film popularized the "bullet time" visual effect.' data-index="0">
  <img src="https://upload.wikimedia.org/..." alt="Movie poster for The Matrix">
  <h3>The Matrix (1999)</h3>
  <p>Sci-Fi, Action</p>
</li>
```

When creating elements dynamically, you can set these `data-` attributes (and any attribute really) using the `dataset` property:

```js
const li = document.createElement('li');
// Note: camelCase, not kebab-case
li.dataset.funFact = 'The film popularized the "bullet time" visual effect.';  
li.dataset.index = '0';
```

{% hint style="info" %}
**Important:** When using `dataset`, use **camelCase** (e.g., `funFact`), but the actual HTML attribute will be **kebab-case** (`data-fun-fact`). JavaScript automatically converts between them.
{% endhint %}

In an event handler, you can then read the stored data using the `dataset` property:

```js
const funFact = document.querySelector("#fun-fact");

moviesList.addEventListener('click', (event) => {
  const clickedLi = event.target.closest('li');
  clickedLi.classList.toggle('highlight');

  // Access the stored data with dataset
  funFact.textContent = clickedLi.dataset.funFact;
});
```

Note that we stored the index. This allows us to easily find the entire movie object for more data access.

```js
const funFact = document.querySelector("#fun-fact");

moviesList.addEventListener('click', (event) => {
  const clickedLi = event.target.closest('li');
  clickedLi.classList.toggle('highlight');

  // By storing the index, we can easily find the entire object
  const movieObject = movies[clickedLi.dataset.index];
  funFact.textContent = `${movieObject.title} fun fact: ${movieObject.funFact}`;
});
```

### Challenge: Card Flip Game

Build a card flip game that lets users click cards to reveal hidden emojis. HTML and CSS has been provided for you as well as a set of shuffled emojis:

**index.html**

```html
<ul id="card-grid">
  <!-- render emoji cards here -->
</ul>
```

**index.js**
```js
const emojis = ['🐶', '🐱', '🐸', '🦊', '🐶', '🐱', '🐸', '🦊'];

// Shuffle the emojis
emojis.sort(() => Math.random() - 0.5);
```

**Requirements:**
1. Render cards from an array of emojis (the emojis are duplicated for matching pairs)
2. Each card should start face-down (emoji hidden) and flip to reveal the emoji when clicked
3. Use CSS to hide/show the emoji (the class `.card` when face-down, and `.card.flipped` when flipped)
4. Use event delegation—one click listener on the parent container handles all card clicks

When you're done rendering and adding your event listener, the card grid should look like this after clicking on two of the cards:

```html
<ul id="card-grid">
  <li data-emoji="🐶" class="card"></li>
  <li data-emoji="🐱" class="card"></li>
  <li data-emoji="🐱" class="card"></li>
  <!-- the flipped class and the text content are set when clicked -->
  <li data-emoji="🐶" class="card flipped">🐶</li>
  <li data-emoji="🐸" class="card"></li>
  <li data-emoji="🦊" class="card"></li>
  <li data-emoji="🦊" class="card flipped">🦊</li>
  <li data-emoji="🐸" class="card"></li>
</ul>
```

**<details><summary>Solution</summary>**

```js
const emojis = ['🐶', '🐱', '🐸', '🦊', '🐶', '🐱', '🐸', '🦊'];

// Shuffle the emojis
emojis.sort(() => Math.random() - 0.5);

// Render the cards
const cardGrid = document.querySelector('#card-grid');

emojis.forEach((emoji) => {
  const card = document.createElement('li');
  card.classList.add('card');
  card.dataset.emoji = emoji;
  cardGrid.append(card);
});

// Event delegation: one listener handles all card clicks
cardGrid.addEventListener('click', (event) => {
  // closest isn't really needed here since there is nothing in the li
  // but it is a good pattern to show:
  const clickedCard = event.target.closest('li');

  clickedCard.classList.toggle('flipped');

  if (clickedCard.textContent === "") {
    clickedCard.textContent = clickedCard.dataset.emoji;
  } else {
    clickedCard.textContent = "";
  }
});
```

</details>

**Bonus Challenges:**
- Only allow 2 cards to be flipped at a time. If they match, keep them flipped. If not, flip them back after 1 second.
- Add a move counter that increments each time the user flips a card
- Add a win condition that displays a message when all pairs are matched
- Add a "Reset" button that shuffles and resets all cards

---

## Additional Reading

### Selecting Multiple Elements with querySelectorAll

While `querySelector` returns the **first** matching element, `querySelectorAll` returns **all** matching elements as a `NodeList`:

```js
// Get ALL list items on the page
const allListItems = document.querySelectorAll('li');

// Get all elements with the class "card"
const allCards = document.querySelectorAll('.card');

// Get all images inside the gallery
const galleryImages = document.querySelectorAll('#gallery img');
```

A `NodeList` is array-like but not a true array. You can access elements by index and use `forEach`:

```js
const items = document.querySelectorAll('li');

// Access by index
console.log(items[0]);
console.log(items.length);

// Use forEach
items.forEach((item) => {
  item.classList.add('highlight');
});
```

But you cannot use `map`, `filter`, or other array methods directly:

```js
// This will throw an error!
items.map((item) => item.textContent);  // TypeError: items.map is not a function
```

To use array methods, spread the `NodeList` into an array first:

```js
// Convert to array, then use array methods
const itemTexts = [...items].map((item) => item.textContent);
const longItems = [...items].filter((item) => item.textContent.length > 10);
```

### innerHTML: A Shortcut with Risks

Instead of creating elements one by one, you can inject HTML directly using `innerHTML`:

```js
const ul = document.querySelector('#friends-list');
ul.innerHTML = `
  <li>Alice</li>
  <li>Bob</li>
  <li>Carol</li>
`;
```

This is more concise, but comes with **serious security risks** if used with user-generated content:

```js
// DANGEROUS: If userName contains malicious code, it will execute!
const userName = '<img src="x" onerror="alert(\'Hacked!\')">';
container.innerHTML = `<p>Welcome, ${userName}!</p>`;
```

This is called a **Cross-Site Scripting (XSS)** attack. The malicious code runs in the user's browser.

**Rules for innerHTML:**
- **Safe:** hardcoded strings that you control
- **Dangerous:** any data from users, APIs, or databases

When in doubt, use `createElement` and `textContent`—they're safer because `textContent` escapes HTML characters automatically.

### `element.matches`

With event delegation, you can use `element.matches(selector)` to handle events for specific targets:

```js
// The parent (moviesList) handles all clicks that bubble up from the children
moviesList.addEventListener('click', (event) => {
  // Find the li closest to the element that was clicked
  const clickedLi = event.target.closest('li');
  
  clickedLi.classList.toggle('highlight');

  // Do this only if the clicked target was an image.
  if (event.target.matches('img')) {
    event.target.style.border = "2px dashed red";
  }
});
```

### Stopping Event Propagation

Sometimes you don't want an event to bubble up to parent elements. You can stop propagation using `event.stopPropagation()`:

```html
<div id="outer">
  <button id="inner">Click me</button>
</div>
```

```js
document.querySelector('#inner').addEventListener('click', (event) => {
  console.log('Button clicked!');
  event.stopPropagation();  // Stop the event from bubbling
});

document.querySelector('#outer').addEventListener('click', () => {
  console.log('Outer div clicked!');  // This won't run when button is clicked
});
```

**When to use it:**
- When a child element should handle an event exclusively
- When you have nested interactive elements and don't want clicks to trigger multiple handlers

**When NOT to use it:**
- Don't use it "just in case"—it can break event delegation and other patterns that rely on bubbling
- If you're using event delegation, you typically don't need `stopPropagation`

### Removing Event Listeners

You can remove event listeners using `removeEventListener`. This requires a reference to the original handler function:

```js
const handleClick = (event) => {
  console.log('Clicked!');
};

const button = document.querySelector('#my-button');

// Add the listener
button.addEventListener('click', handleClick);

// Later, remove it
button.removeEventListener('click', handleClick);
```

**Important:** You cannot remove an anonymous function because you have no reference to it:

```js
// This CANNOT be removed later
button.addEventListener('click', () => {
  console.log('Clicked!');
});

// This CAN be removed because we have a reference to handleClick
button.addEventListener('click', handleClick);
```

**When to remove listeners:**
- When an element is being removed from the page (though modern browsers handle this automatically)
- When you want to disable interactivity temporarily
- When implementing "one-time" actions (though you can also use `{ once: true }` option)

```js
// This listener automatically removes itself after firing once
button.addEventListener('click', handleClick, { once: true });
```
