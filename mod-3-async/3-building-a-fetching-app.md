# Building a Fetching App

## Video

Watch the full build tutorial here!

{% embed url="https://youtu.be/2wDeDaZ8C7c?si=_M6wCuncNeyNj1aY" %}

## Setup

Create a new repository and clone it down. CD and then:

```sh
`npm create vite`
# name: app
# Framework: Vanilla
# Variant: JavaScript
```

cd into `app` and `npm i`

Remove the starter code.

## Plan

Before you build anything, draw a wireframe:

![](../fullstack-curriculum/mod-3-async/pokedex-wireframe.svg)

Then plan out the logic:

* When the page loads:
  * fetch pikachu (make sure to catch any errors)
  * render pikachu data to the screen
  * add an event listener for the form
* When the form is submitted:
  * grab data from the form
  * use that data to fetch a new pokemon (make sure to catch any errors)
  * render that pokemon to the screen

## HTML

First, we'll create the structure. We want:

1. An `h1` heading with some title
2. A `form` to search for a pokemon. It should have:
   1. An `h2` heading with a form title
   2. A `label` and `input:text` combo for the pokemon name.
   3. A `button` to submit the form
3. A `section` to display pokemon information. It will have two sections and we'll make it `display: flex` to put them side by side:
   1. A `div` to display the pokemon picture and name
   2. A `div` with a `ul` to display the pokemon stats

Here's the HTML we came up with, added to the `div#app` container:

```html
<div id="app">
  <h1>Pokedex</h1>
  <form id="pokemon-form">
    <h2>Search for a Pokemon</h2>
    <label for="pokemon-name">Name</label>
    <input type="text" id="pokemon-name" name="pokemonName">
    <button>Search</button>
  </form>
  <section id="pokemon-info-container">
    <div id="pokemon-picture">
      <img src="" alt="">
      <p id="pokemon-picture-name">Pokemon loading...</p>
    </div>
    <div id="pokemon-stats">
      <h2>Pokemon Stats</h2>
      <ul id="pokemon-stats-list">
        <!-- dynamically added list items go here -->
      </ul>
    </div>
  </section>
</div>
```

## JavaScript Separation of Concerns

Rather than writing all of your code in one place, separate your code into three files:

* `src/fetching-helpers.js` - exports functions related to fetching data from specific Web APIs
* `src/dom-helpers.js` - exports functions related to dom manipulation
* `src/main.js` - pulls together functions from helper files and invokes them. Defines event handlers if needed.

After creating these files, we can put the outline of some functions inside:

```js
// fetching-helpers.js
export const fetchPokemon = (pokemonName) => {
  console.log("fetching pokemon: " + pokemonName);
}

// dom-helpers.js
export const showPokemon = (pokemonData) => {
  console.log("rendering pokemon: " + pokemonData)
}

// main.js
import { fetchPokemon } from ./fetching-helpers.js
import { showPokemon } from ./dom-helpers.js

const main = () => {
  fetchPokemon("pikachu");
};

main();
```

Start with fetching data from the API since that the data we get back will tell us what information we can render. We'll use `pikachu` as the test input for now.

### Fetching

In `fetching-helpers.js`, we can create a `fetchPokemon` function that takes in a `pokemonName` and searches the PokeAPI for that pokemon:

```js
export const fetchPokemon = (pokemonName) => {
  console.log('fetching pokemon ' + pokemonName)

  // 2. Define promise handlers with .then and .catch
  return fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonName}`)
    .then((response) => {
      // 3. Check that the response is ok. If it isn't throw a useful error.
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`)
      }

      // 4. Start reading the response body's ReadableStream and return the promise
      return response.json();
    })
    .then((pokemonData) => {
      console.log(pokemonData);
      return pokemonData;
    })
    .catch((err) => {
      // 6. Handle Errors
      console.error(err);
    })
};
```

This file only cares about fetching the `pokemonData` from the response body's `ReadableStream`. Once we get that data, this function has done its job. It will return `pokemonData` wrapped in a Promise.

### Putting it Together

Remember how we set up `showPokemon` in `dom-helpers.js`?

```js
// dom-helpers.js
export const showPokemon = (pokemonData) => {
  console.log("rendering pokemon: " + pokemonData)
}
```

Because of this forward thinking, in `main.js`, we can already link up `fetchPokemon` with `showPokemon` by invoking `showPokemon` when the promise returned by `fetchPokemon` resolves.

```js
// main.js
import { fetchPokemon } from ./fetching-helpers.js
import { showPokemon } from ./dom-helpers.js

const main = () => {
  fetchPokemon("pikachu")
    .then((pokemonData) => {
      showPokemon(pokemonData)
    });
  
  /* or in one line if you want to be fancy:

  fetchPokemon("pikachu").then(showPokemon);

  */
};

main();
```

The `pokemonData` that the `fetchPokemon` promise resolves to will be passed to and printed out by showPokemon.

### Rendering

In `dom-helpers.js`, the final step is rendering the pokemon! This requires us to look at the API more closely to see the structure of the data we're dealing with.

Printing out the `pokemonData` is a helpful way to see the data structure first. We can see that we care about the following data:

* `pokemonData.sprites` — an object of pokemon images
* `pokemonData.name` — the name of the pokemon

```js
export const showPokemon = (pokemonData) => {
  console.log("rendering pokemon: " + pokemonData)
  
  const img = document.querySelector('#pokemon-picture>img')
  const nameP = document.querySelector('#pokemon-picture-name');

  img.src = pokemonData.sprites.front_default;
  nameP.textContent = pokemonData.name
}
```

## Adding Form Handling

Because of the beautiful separation of concerns, adding in form functionality is easy. We just grab the `pokemonName` data from the form, pass it to `fetchPokemon`, and `showPokemon` when the promise resolves!

```js
const handleSubmit = (e) => {
  e.preventDefault();
  const form = e.target;
  const pokemonName = form.pokemonName.value;

  fetchPokemon(pokemonName).then(showPokemon);
  
  form.reset();
}

const main = () => {
  fetchPokemon('pikachu').then(showPokemon);

  document
    .querySelector('form')
    .addEventListener('submit', handleSubmit)
}

main();
```
