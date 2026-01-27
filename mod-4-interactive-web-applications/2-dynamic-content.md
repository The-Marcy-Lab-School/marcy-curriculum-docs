# Dynamic Content

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-2-dynamic-content)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Why Create Elements Dynamically?](#why-create-elements-dynamically)
- [The Create, Modify, Append Pattern](#the-create-modify-append-pattern)
  - [Rendering Data from Arrays](#rendering-data-from-arrays)
- [Making Dynamic Content Interactive](#making-dynamic-content-interactive)
  - [The Problem: Adding Listeners to Dynamic Elements](#the-problem-adding-listeners-to-dynamic-elements)
  - [Event Bubbling (Propagation)](#event-bubbling-propagation)
  - [Event Delegation: The Solution](#event-delegation-the-solution)
  - [Challenge: Card Flip Game](#challenge-card-flip-game)
- [Additional Reading](#additional-reading)
  - [Selecting Multiple Elements with querySelectorAll](#selecting-multiple-elements-with-queryselectorall)
  - [innerHTML: A Shortcut with Risks](#innerhtml-a-shortcut-with-risks)
  - [Stopping Event Propagation](#stopping-event-propagation)
  - [Removing Event Listeners](#removing-event-listeners)

## Key Concepts

**Creating Elements**
* **`document.createElement(tagName)`** — creates a new HTML element of the specified type (e.g., `'div'`, `'li'`, `'img'`). The element is not yet on the page.
* **`parentElement.append(childElement)`** — adds an element as the last child of the parent element. You can append multiple elements at once: `parent.append(el1, el2, el3)`.

**Event Propagation & Delegation**
* **Event propagation (bubbling)** — when an event occurs on an element, it "bubbles up" through all of its ancestors, allowing them to also detect the event.
* **`event.currentTarget`** — the element that the event listener is attached to (the one handling the event).
* **Event delegation** — a pattern where a parent element handles events for its children, using `event.target` to determine which child triggered the event.
* **`element.matches(selector)`** — returns `true` if the element matches the given CSS selector. Useful for filtering events in delegation.
* **`element.closest(selector)`** — returns the nearest ancestor (or itself) that matches the selector. Useful for finding a parent container from a clicked child.

**Additional Topics**
* **`document.querySelectorAll(selector)`** — returns a `NodeList` of all elements matching the CSS selector.
* **`NodeList`** — an array-like collection of elements. Supports `forEach` and bracket notation, but not all array methods like `map` or `filter`. Use `[...nodeList]` to convert to a true array.
* **`element.innerHTML`** — a property that gets or sets the HTML content inside an element. Convenient but risky with user-generated content (XSS vulnerability).
* **`event.stopPropagation()`** — prevents an event from bubbling up to parent elements.
* **`element.removeEventListener(eventType, handler)`** — removes an event listener. Requires a reference to the original handler function.

## Why Create Elements Dynamically?

In the last lesson, we learned how to select existing elements and make them interactive. But what if the elements don't exist yet?

Consider these real-world scenarios:
- A todo list where users can add new items
- A shopping cart that shows items the user has added
- A search results page that displays matches based on user input
- A social media feed that loads new posts as you scroll

In all these cases, we can't hardcode the HTML—the content depends on **data** that may change. We need to create elements dynamically using JavaScript.

In this lesson, we'll first learn how to create individual elements using JavaScript, then we'll

## The Create, Modify, Append Pattern

To add new elements to the page, follow this three-step pattern:

1. **Create** — use `document.createElement(tagName)` to make a new element
2. **Modify** — set its properties (`textContent`, `id`, `classList`, `src`, etc.)
3. **Append** — use `parentElement.append(newElement)` to add it to the page

```js
// 1. Create a new paragraph element
const newParagraph = document.createElement('p');

// 2. Modify its properties
newParagraph.textContent = 'This paragraph was created with JavaScript!';
newParagraph.classList.add('highlight');
newParagraph.id = 'dynamic-paragraph';

// 3. Append it to a parent element
const container = document.querySelector('#container');
container.append(newParagraph);
```

The element doesn't appear on the page until you append it. Until then, it only exists in memory.

You can also append multiple elements at once using `append`:

```js
const ul = document.querySelector('#my-list');
const li1 = document.createElement('li');
const li2 = document.createElement('li');
const li3 = document.createElement('li');

li1.textContent = '1';
li2.textContent = '2';
li3.textContent = '3';

ul.append(li1, li2, li3);  // Add all three at once
```

**Challenge:** Add a fourth list item that is highlighted.

**<details><summary>Solution</summary>**

```js
count = ul.children.length + 1;

const addItemBtn = document.querySelector("#add-item-btn");
addItemBtn.addEventListener("click", () => {
  const li = document.createElement("li");
  li.textContent = count;
  count++;
  ul.append(li);
});
```

</details>

### Rendering Data from Arrays

We can see the power of this create-modify-append pattern when we have an array of data that we want to generate our HTML content from.

Consider this array:

```js
const friends = ['Alice', 'Bob', 'Carol', 'David'];
```

How would you use it to create this HTML structure?

```html
<ul id="friends-list">
  <li>Alice</li>
  <li>Bob</li>
  <li>Carol</li>
  <li>David</li>
</ul>
```

First grab the `ul` using `querySelector`. Then, iterate through the list and for each item use the create-modify-append pattern:

```js
const friends = ['Alice', 'Bob', 'Carol', 'David'];
const ul = document.querySelector('#friends-list');

friends.forEach((friendName) => {
  const li = document.createElement('li');
  li.textContent = friendName;
  ul.append(li);
});
```

Now consider a more complex array, like this array of movies:

```js
const movies = [
  { 
    title: 'The Matrix',
    year: 1999, 
    genres: ['Sci-Fi', 'Action'],
    src: 'https://upload.wikimedia.org/wikipedia/en/d/db/The_Matrix.png'
  },
  { 
    title: 'Inception',
    year: 2010, 
    genres: ['Sci-Fi', 'Thriller'],
    src: 'https://upload.wikimedia.org/wikipedia/en/2/2e/Inception_%282010%29_theatrical_poster.jpg'
  },
  { 
    title: 'Parasite',
    year: 2019, 
    genres: ['Thriller', 'Drama'],
    src: 'https://upload.wikimedia.org/wikipedia/en/5/53/Parasite_%282019_film%29.png'
  },
];
```

How would you use this data to create the following HTML? Hint: you will need to create the image, heading, and paragraph elements AND the list item to contain them.

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

**<details><summary>Solution</summary>**

The pattern is the same—grab the parent, iterate through the data, and for each item create-modify-append:

```js
const movies = [
  /* data... */
]

// Grab the movies list
const moviesList = document.querySelector('#movies-list');

movies.forEach((movie) => {
  // Create elements
  const img = document.createElement('img');
  const titleH3 = document.createElement('h3');
  const genresP = document.createElement('p');

  img.src = movie.src;
  img.alt = `Movie poster for ${movie.title}`;
  titleH3.textContent = `${movie.title} (${movie.year})`;
  genresP.textContent = movie.genres.join(', ');

  // Create the li and append the content to it
  const li = document.createElement('li');
  li.append(img, titleH3, genresP);

  // Append the li to the movies list
  moviesList.append(li);
});
```

</details>

## Making Dynamic Content Interactive

Now that we can create elements, how do we make them interactive? Let's say we want each movie to highlight when clicked.

### The Problem: Adding Listeners to Dynamic Elements

One approach is to add an event listener to each element as we create it:

```js
const moviesList = document.querySelector('#movies-list');

movies.forEach((movie) => {
  const img = document.createElement('img');
  const titleH3 = document.createElement('h3');
  const genresP = document.createElement('p');

  img.src = movie.src;
  img.alt = `Movie poster for ${movie.title}`;
  titleH3.textContent = `${movie.title} (${movie.year})`;
  genresP.textContent = movie.genres.join(', ');

  const li = document.createElement('li');
  li.append(img, titleH3, genresP);

  // Add a click listener to each li
  li.addEventListener('click', () => {
    li.classList.toggle('highlight');
  });

  moviesList.append(li);
});
```

This works, but it is inefficient: every element has its own listener (we have three list items which means we have three separate listeners).

It would be much more efficient to have a single event listener on the *parent* element (the `ul`) and have it handle click events on any of its children.

This is possible through **event bubbling (propagation)** and **event delegation**

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

Event delegation uses bubbling to our advantage. Instead of adding listeners to each child element, we add **one listener to the parent** and use `event.target` to determine which child was clicked:

```js
// Render the movies WITHOUT any event listeners
const moviesList = document.querySelector('#movies-list');
movies.forEach((movie) => {
  const img = document.createElement('img');
  const titleH3 = document.createElement('h3');
  const genresP = document.createElement('p');

  img.src = movie.src;
  img.alt = `Movie poster for ${movie.title}`;
  titleH3.textContent = `${movie.title} (${movie.year})`;
  genresP.textContent = movie.genres.join(', ');

  const li = document.createElement('li');
  li.append(img, titleH3, genresP);

  moviesList.append(li);
});

// ONE event listener on the parent handles all clicks
const moviesList = document.querySelector('#movies-list');
moviesList.addEventListener('click', (event) => {
  // Find the li that was clicked (or contains the clicked element)
  const clickedLi = event.target.closest('li');
  
  // Guard clause: if they didn't click inside an li, do nothing
  if (!clickedLi) return;
  
  clickedLi.classList.toggle('highlight');
});

renderMovies(movies);
```

The `closest()` method is key here—it finds the nearest ancestor (or the element itself) that matches the selector. This handles cases where the user clicks on the `<h3>` or `<p>` inside the `<li>`.

**Benefits of event delegation:**
- **Less memory** — one listener instead of many
- **Cleaner code** — event handling is separate from element creation
- **Works for new elements** — any new `<li>` added to the list automatically works

You can also use `element.matches(selector)` to filter which elements you care about:

```js
moviesList.addEventListener('click', (event) => {
  // The user MUST click on the h3 element
  if (!event.target.matches('h3')) return;
  
  console.log('Movie title clicked:', event.target.textContent);
});
```

### Challenge: Card Flip Game

Build a card flip game that lets users click cards to reveal hidden emojis. HTML and CSS has been provided for you.

**Requirements:**
1. Render cards from an array of emojis (the emojis are duplicated for matching pairs)
2. Each card should start face-down (emoji hidden) and flip to reveal the emoji when clicked
3. Use CSS to hide/show the emoji (the class `.card` when face-down, and `.card.flipped` when flipped)
4. Use event delegation—one click listener on the parent container handles all card clicks

When you're done rendering and adding your event listener, the card grid should look like this after clicking on two of the cards:

```html
<div id="card-grid">
  <div class="card">🐶</div>
  <div class="card">🐱</div>
  <div class="card">🐱</div>
  <div class="card flipped">🐶</div>
  <div class="card">🐸</div>
  <div class="card flipped">🦊</div>
  <div class="card">🦊</div>
  <div class="card">🐸</div>
</div>
```

**<details><summary>Solution</summary>**

```js
const emojis = ['🐶', '🐱', '🐸', '🦊', '🐶', '🐱', '🐸', '🦊'];

// Shuffle the emojis
emojis.sort(() => Math.random() - 0.5);

// Render the cards
const cardGrid = document.querySelector('#card-grid');

emojis.forEach((emoji) => {
  const card = document.createElement('div');
  card.classList.add('card');
  card.textContent = emoji;
  cardGrid.append(card);
});

// Event delegation: one listener handles all card clicks
cardGrid.addEventListener('click', (event) => {
  if (!event.target.matches('.card')) return;

  event.target.classList.toggle('flipped');
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
