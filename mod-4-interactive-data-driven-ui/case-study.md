# Review Session: Recipe Browser

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/swe-casestudy-4-interactive-data-driven-uis)!
{% endhint %}

- [Setup](#setup)
- [Overview](#overview)
- [Explore the Solution](#explore-the-solution)
  - [Trace the Flow](#trace-the-flow)
  - [Guided Reading Questions](#guided-reading-questions)
- [Building from Scratch](#building-from-scratch)
  - [Step 0: Tour the starter and solution code](#step-0-tour-the-starter-and-solution-code)
  - [Step 1: Create `src/fetch-helpers.js` - fetch a list of recipes](#step-1-create-srcfetch-helpersjs---fetch-a-list-of-recipes)
  - [Step 2: Import and test in `main.js`](#step-2-import-and-test-in-mainjs)
  - [Step 3: Create `src/dom-helpers.js` - render the recipe list](#step-3-create-srcdom-helpersjs---render-the-recipe-list)
  - [Step 4: Wire up rendering in `main.js`](#step-4-wire-up-rendering-in-mainjs)
  - [Step 5: Add `getRecipeById` to `fetch-helpers.js`](#step-5-add-getrecipebyid-to-fetch-helpersjs)
  - [Step 6: Add the click handler with event delegation](#step-6-add-the-click-handler-with-event-delegation)
  - [Step 7: Add `renderRecipeDetails` to `dom-helpers.js`](#step-7-add-renderrecipedetails-to-dom-helpersjs)
  - [Step 8: Add `getRecipeBySearchTerm` with `async`/`await`](#step-8-add-getrecipebysearchterm-with-asyncawait)
  - [Step 9: Add the search form handler in `main.js`](#step-9-add-the-search-form-handler-in-mainjs)
  - [Step 10 (Bonus): Add error rendering](#step-10-bonus-add-error-rendering)
- [Concepts Checklist](#concepts-checklist)


## Setup

```sh
npm i
npm run dev
```

## Overview

This case study application displays recipes from the [https://dummyjson.com/recipes](https://dummyjson.com/recipes) API, allowing users to click on a recipe card to see more details about it. They can also search for recipes using the provided form.

This case study application demonstrates DOM manipulation, fetching with `.then()`/`.catch()`, fetching with `async`/`await`, ES Modules, event delegation, `dataset`, and form handling. The completed solution is in `src-solution/`.

## Explore the Solution

The completed solution is in `src-solution/`. Use the exercises below to investigate how the code works before building it from scratch.

### Trace the Flow

For each scenario, trace the path through the code across files. Write each function call and what it does, in order.

**Scenario 1: The page loads**

Start at `main.js` line 5. Follow `getRecipes()` into `fetch-helpers.js`, then back into `renderRecipes()` in `dom-helpers.js`.

**Scenario 2: A user clicks a recipe card**

Start at the click handler in `main.js`. Track `event.target.closest('li')`, `li.dataset.recipeId`, `getRecipeById(...)`, and `renderRecipeDetails(...)`.

**Scenario 3: A user submits the search form**

Start at the submit handler in `main.js`. Track form value extraction, `getRecipeBySearchTerm(...)`, error/no-results/success branches, optional quick filtering, and final rendering.

**Scenario 4: Search fails**

Assume `/recipes/search` fails. Follow the `error` path returned by `getRecipeBySearchTerm(...)` and explain what is rendered and why the message remains visible until a later success branch calls `hideError()`.

### Guided Reading Questions

Open each file and answer the questions.

**`index.html`**
1. What does `type="module"` on the `<script>` tag enable?
2. Find the fallback recipe card in the `ul`. What will happen to it once JavaScript loads successfully?
3. Which elements start with `class="hidden"`? Why would we hide them by default?
4. What `data-` attribute is on the fallback `<li>`? What value does it have?

**<details><summary>Answers</summary>**

1. It enables ES modules in the browser, so we can use `import` / `export` in JavaScript files.
2. It gets removed when `renderRecipes` runs, because `recipesList.innerHTML = ''` clears the list before new cards are appended.
3. `#recipe-details` and `#error-message` start hidden so details and errors only appear when relevant user actions or failures occur.
4. The fallback card has `data-recipe-id="1"`.

</details>

**`src-solution/fetch-helpers.js`**
1. What does `getRecipes` resolve to if the fetch succeeds? What does it resolve to if the fetch fails?
2. What kind of errors does checking `response.ok` handle that `.catch()` does not handle on its own?
3. The API returns an object like `{ recipes: [...], total: 50, ... }`. In `getRecipes`, where is just the recipe array extracted, and what would break if we returned the full object instead?
4. `getRecipeById` and `getRecipes` follow the same pattern. What is the one structural difference between them? Why doesn't `getRecipeById` need a second `.then()`?
5. Compare and contrast `getRecipeBySearchTerm` with the other fetch helpers. What are the benefits/tradeoffs of using `async`/`await` + `try`/`catch` and returning `{ data, error }`? Which style of handling promises do you prefer?

**<details><summary>Answers</summary>**

1. It resolves to `data.recipes` (an array) on success, and resolves to `null` on failure.
2. It handles HTTP failure responses (like 404/500) that do not reject `fetch` by default. `.catch()` alone only handles rejected Promises (network/throw errors).
3. It is extracted in the second `.then((data) => { return data.recipes; })`. If we returned the full object, code expecting an array (like `renderRecipes(recipes)` and `recipes.length` / `forEach`) would break.
4. `getRecipes` has an extra `.then` to extract `data.recipes`; `getRecipeById` does not because that endpoint already returns a single recipe object directly.
5. `async`/`await` can be easier to read and debug for sequential logic, and `{ data, error }` gives a consistent result shape. Tradeoff: it introduces a different return contract from the other helpers (`null`), so callers must handle two patterns. It is best to stick to one pattern so choose your preference!

</details>

**`src-solution/dom-helpers.js`**
1. Why does `renderRecipes` clear `#recipes-list` before rendering?
2. Why does `renderRecipeDetails` remove the `hidden` class?
3. What is the difference between `renderError` and `hideError`?
4. What does `li.dataset.recipeId = recipe.id` add to the DOM, what is that value used for later, and why store it on each card?

**<details><summary>Answers</summary>**

1. To remove old/fallback content before rendering new results and avoid duplicate cards.
2. The details section is hidden by default, so removing `hidden` makes the selected recipe details visible.
3. `renderError(msg)` shows the error element and sets text. `hideError()` clears text and hides it.
4. It adds a `data-recipe-id` attribute on each `li`. Later, the click handler reads `li.dataset.recipeId` to call `getRecipeById(...)` with the id of the clicked item. Storing it on each card keeps the card tied to its API ID for event-driven fetching.

</details>

**`src-solution/main.js`**
1. What are the three actions that can trigger a fetch in this file?
2. Where is event delegation used, and why?
3. Where is the search form handled, and why is the handler `async`?
4. Where does the quick filter (`Under 20 Minutes`) apply?
5. In which branches is `hideError()` called?

**<details><summary>Answers</summary>**

1. Initial page load (`getRecipes`), clicking a recipe card (`getRecipeById`), and submitting the search form (`getRecipeBySearchTerm`).
2. On `#recipes-list` click. One parent listener handles clicks on dynamically rendered cards, including clicks on child elements via `closest('li')`.
3. In the `submit` handler for `#search-form`; it is `async` because it awaits `getRecipeBySearchTerm(...)`.
4. In the search success branch, after fetch resolves: if `isQuick` is true, results are filtered by total prep + cook time <= 20.
5. In three success branches: after successful initial load, after successful recipe-details fetch, and after successful search before rendering results.

</details>

## Building from Scratch

So, how could you build this application from scratch?

The process for creating an interactive and data-driven user interface typically follows this order:
1. Create the HTML with `id` and `class` attributes so we can target elements. Leave empty containers for content generated with JavaScript/DOM manipulation.
2. Create fetch helper functions and test with console logs.
3. Create rendering helper functions. Data in -> DOM out.
4. Connect the data source to rendering logic. This can look like:
   - Page load -> fetch -> render
   - User click -> fetch -> render
   - Form submit -> extract form data -> fetch -> render

### Step 0: Tour the starter and solution code

We've taken care of the first step for you. Walk through the provided files before writing JavaScript. Pay attention to the empty containers and the elements with `id`s that we use in our JavaScript.

- **`index.html`** - Take note of:
  - `<script type="module" src="./src-solution/main.js">` at the bottom.
  - The app currently loads the solution by default so students can inspect working behavior first.
  - To build from scratch, switch that line to `./src/main.js`.
  - The hardcoded fallback recipe card in the `ul` (what users see before JS loads or if fetch fails).
  - The `#recipe-details` section with `class="hidden"` (hidden by default, shown on click).
  - The `#error-message` paragraph with `class="hidden"`.
  - The search form (`#search-form`) with `searchTerm` text input and `isQuick` checkbox.
- **`styles.css`** - Take note of the class `.hidden { display: none !important; }`.
- **`src/main.js`** - Starter file is empty.
- **`src-solution/main.js`** - Full implementation including initial load, click-to-details, and search.


### Step 1: Create `src/fetch-helpers.js` - fetch a list of recipes

These next 4 steps walk through implementing the first feature: fetching and rendering a list of recipes.

> **Skills:** `fetch`, `.then()`, `.catch()`, `response.ok`, named exports, returning from `.then()`

Create the file and write `getRecipes`:

```js
export const getRecipes = () => {
  return fetch('https://dummyjson.com/recipes?limit=9')
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      return response.json();
    })
    .then((data) => {
      return data.recipes;
    })
    .catch((error) => {
      console.error(error.message);
      return null;
    });
};
```

**Key Details:**
- `fetch()` returns a Promise.
- Check `response.ok` because 404/500 do not automatically reject fetch.
- `response.json()` returns a Promise and must be returned.
- We extract `data.recipes` from the API response object.
- On failure, return `null` so callers can handle errors.

### Step 2: Import and test in `main.js`

> **Skills:** named imports with `.js` extension, `.then()` on returned Promise

```js
import { getRecipes } from './fetch-helpers.js';

getRecipes().then((recipes) => {
  console.log(recipes);
});
```

You should see an array of recipe objects in the console.

### Step 3: Create `src/dom-helpers.js` - render the recipe list

> **Skills:** `document.createElement`, `append`, `dataset`, `innerHTML = ''`, named exports

```js
export const renderRecipes = (recipes) => {
  const recipesList = document.querySelector('#recipes-list');
  const recipeCount = document.querySelector('#recipe-count');

  recipesList.innerHTML = '';
  recipeCount.textContent = recipes.length;

  recipes.forEach((recipe) => {
    const li = document.createElement('li');
    li.dataset.recipeId = recipe.id;

    const img = document.createElement('img');
    img.src = recipe.image;
    img.alt = recipe.name;

    const h3 = document.createElement('h3');
    h3.textContent = recipe.name;

    const info = document.createElement('p');
    info.textContent = `${recipe.cuisine} · ${recipe.difficulty}`;

    li.append(img, h3, info);
    recipesList.append(li);
  });
};
```

### Step 4: Wire up rendering in `main.js`

> **Skills:** module imports, null checking

```js
import { getRecipes } from './fetch-helpers.js';
import { renderRecipes } from './dom-helpers.js';

getRecipes().then((recipes) => {
  if (recipes === null) {
    console.log('Failed to load recipes.');
    return;
  }
  renderRecipes(recipes);
});
```

We've now completely implemented the first feature: fetching and rendering all recipes!

### Step 5: Add `getRecipeById` to `fetch-helpers.js`

These next three steps walk through implementing the second feature: fetching recipe details when we click on a specific recipe.

> **Skills:** template literals, shared fetch pattern

```js
export const getRecipeById = (id) => {
  return fetch(`https://dummyjson.com/recipes/${id}`)
    .then((response) => {
      if (!response.ok) {
        throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
      }
      return response.json();
    })
    .catch((error) => {
      console.error(error.message);
      return null;
    });
};
```

### Step 6: Add the click handler with event delegation

> **Skills:** event delegation, `closest()`, `dataset`, second fetch + render

```js
import { getRecipes, getRecipeById } from './fetch-helpers.js';
import { renderRecipes, renderRecipeDetails } from './dom-helpers.js';

// ... existing getRecipes code ...

const recipesList = document.querySelector('#recipes-list');
recipesList.addEventListener('click', (event) => {
  const li = event.target.closest('li');
  if (!li) return;

  getRecipeById(li.dataset.recipeId).then((recipe) => {
    if (recipe === null) {
      console.log('Failed to load recipe details.');
      return;
    }
    renderRecipeDetails(recipe);
  });
});
```

### Step 7: Add `renderRecipeDetails` to `dom-helpers.js`

> **Skills:** showing hidden content, nested list rendering

```js
export const renderRecipeDetails = (recipe) => {
  const detailsSection = document.querySelector('#recipe-details');
  detailsSection.classList.remove('hidden');
  detailsSection.innerHTML = '';

  const h2 = document.createElement('h2');
  h2.textContent = recipe.name;

  const img = document.createElement('img');
  img.src = recipe.image;
  img.alt = recipe.name;

  const info = document.createElement('p');
  info.textContent = `${recipe.cuisine} · ${recipe.difficulty} · ${recipe.cookTimeMinutes + recipe.prepTimeMinutes} min · ${recipe.rating}/5`;

  const ingredientsH3 = document.createElement('h3');
  ingredientsH3.textContent = 'Ingredients';

  const ingredientsList = document.createElement('ul');
  recipe.ingredients.forEach((ingredient) => {
    const li = document.createElement('li');
    li.textContent = ingredient;
    ingredientsList.append(li);
  });

  detailsSection.append(h2, img, info, ingredientsH3, ingredientsList);
};
```

This completes the second feature: clicking on a list item to fetch its details.

### Step 8: Add `getRecipeBySearchTerm` with `async`/`await`

These next two steps walk through implementing the final feature: searching for recipes.

> **Skills:** `async`/`await`, `try`/`catch`, standardized `{ data, error }` return object

Add this new helper in `fetch-helpers.js`:

```js
export const getRecipeBySearchTerm = async (searchTerm) => {
  try {
    const response = await fetch(`https://dummyjson.com/recipes/search?q=${searchTerm}`);
    if (!response.ok) {
      throw Error(`Fetch failed. ${response.status} ${response.statusText}`);
    }
    const data = await response.json();
    return { data: data.recipes, error: null };
  } catch (error) {
    console.error(error.message);
    return { data: null, error };
  }
};
```

**Why this differs from the other fetch helpers:**
- It demonstrates the `async`/`await` + `try`/`catch` style.
- It returns an object with `{ data, error }` so callers can consistently inspect both success and failure fields (`data` and `error`).

### Step 9: Add the search form handler in `main.js`

> **Skills:** form submit handling, `.checked` for checkboxes, async event handlers, conditional filtering

```js
import { getRecipeBySearchTerm } from './fetch-helpers.js';
import { renderRecipes, renderError, hideError } from './dom-helpers.js';

const form = document.querySelector('#search-form');
form.addEventListener('submit', async (event) => {
  event.preventDefault();

  const searchTerm = form.elements.searchTerm.value;
  const isQuick = form.elements.isQuick.checked;

  // Notice that we can use object destructuring to get the object properties
  const { data, error } = await getRecipeBySearchTerm(searchTerm);

  if (error) {
    // We can now display the actual error message to the user
    renderError(`Failed to find recipes. Try again later. Error: ${error.message}`);
  } else if (data.length === 0) {
    renderError('Could not find recipes matching that search term.');
  } else {
    let recipes = data;
    if (isQuick) {
      recipes = data.filter((recipe) => (recipe.prepTimeMinutes + recipe.cookTimeMinutes) <= 20);
    }
    hideError();
    renderRecipes(recipes);
  }
});
```

### Step 10 (Bonus): Add error rendering

This last step adds useful feedback for the user when errors occur.

> **Skills:** error state rendering, explicit UI state management

Add to `dom-helpers.js`:

```js
const errorMessage = document.querySelector('#error-message');

export const renderError = (msg) => {
  errorMessage.classList.remove('hidden');
  errorMessage.textContent = msg;
};

export const hideError = () => {
  errorMessage.textContent = '';
  errorMessage.classList.add('hidden');
};
```

Then use these in `main.js`:
- Call `renderError(...)` when fetch or search fails.
- Call `hideError()` in success branches (after successful page-load fetch, recipe-details fetch, and successful search) so errors are dismissed manually when the app recovers.

For example:

```js
const { data, error } = await getRecipeBySearchTerm(searchTerm);
if (error) {
  renderError(`Failed to find recipes. Try again later. Error: ${error.message}`);
}
else if (data.length === 0) {
  renderError('Could not find recipes matching that search term.');
}
else {
  let recipes = data;
  if (isQuick) {
    recipes = data.filter((recipe) => (recipe.prepTimeMinutes + recipe.cookTimeMinutes) <= 20);
  }
  hideError();
  renderRecipes(recipes);
}
```

## Concepts Checklist

By the end of this walkthrough, you have demonstrated:

- [ ] Vite dev server and `<script type="module">`
- [ ] ES Modules: `export` and `import` with `.js` extension
- [ ] Separation of concerns: `fetch-helpers.js`, `dom-helpers.js`, `main.js`
- [ ] `fetch()` with `.then()` and `.catch()`
- [ ] `fetch()` with `async`/`await` and `try`/`catch`
- [ ] Checking `response.ok` and throwing errors
- [ ] Returning from `.then()` to chain promises
- [ ] Returning `null` on error (for list/details helpers)
- [ ] Returning `{ data, error }` for standardized search error handling
- [ ] Handling form submit with `event.preventDefault()`
- [ ] Reading checkbox state with `.checked`
- [ ] `document.createElement` + modify + `append` pattern
- [ ] `innerHTML = ''` to clear containers before re-rendering
- [ ] `dataset` to store IDs on elements
- [ ] Event delegation with a listener on a parent element
- [ ] `event.target.closest('li')` to find clicked cards
- [ ] Manual error rendering/hiding with `renderError()` and `hideError()`
