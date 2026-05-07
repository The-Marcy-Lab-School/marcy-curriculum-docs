# 3. Fetching with useEffect

{% hint style="info" %}
Follow along with code examples in the lecture [repo](https://github.com/The-Marcy-Lab-School/7-3-useEffect)!
{% endhint %}

We've already learned about `useState`. Time for another hook! In this lesson, we'll learn how to use the `useEffect` hook to connect our React frontend to a real backend API.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [The Todo API](#the-todo-api)
  - [API Fetching Layer](#api-fetching-layer)
- [The Fetching Pattern: Load → Mutate → Reload](#the-fetching-pattern-load--mutate--reload)
- [Fetch on Load with useEffect](#fetch-on-load-with-useeffect)
  - [useEffect Syntax](#useeffect-syntax)
  - [The Dependency Array](#the-dependency-array)
  - [Async Effect Callback](#async-effect-callback)
  - [Dog App Challenge](#dog-app-challenge)
- [Mutate and Re-Fetch with Event Handlers](#mutate-and-re-fetch-with-event-handlers)
  - [Challenge: PATCH and DELETE](#challenge-patch-and-delete)
- [Quiz](#quiz)

## Essential Questions

By the end of this lesson you should be able to answer:

1. What is a side effect in React, and why does fetching data count as one?
2. What is the purpose of the dependency array in `useEffect`, and how does it control when the effect runs?
3. Why can't the `useEffect` callback itself be `async`?
4. Why do we isolate fetch logic in `fetch-helpers.js` instead of writing `fetch` calls directly inside components?
5. What is the refetch-after-write pattern, and why does it keep the UI consistent with the server?

## Key Concepts

* **Side effect** — Anything that happens outside of React such as sending a `fetch` request, starting an animation, or setting up a server connection.
  * Side effects can be triggered by user events like submitting a form or clicking a button.
* **`useEffect`** – A React hook for executing "side effects" caused by a component rendering, not a particular event.
* **Dependency Array** — The array of values provided to `useEffect` that React will watch for changes. If values in the dependency array change between renders, the effect will run again.
* **`fetch-helpers.js`** — A file that isolates all API fetch logic from the React component. The same pattern works in Vanilla JS or React — it is framework-agnostic.
* **Refetch-after-write** — The pattern of re-fetching the full list from the server after any mutation (POST, PATCH, DELETE) to keep the UI in sync.

## The Todo API

Now that we know the basics of React—how to use JSX to create components and manage state with `useState()`—we can start looking at connecting our React frontend applications to an API.

Starting today, our React app connects to a real Express + Postgres backend. The API contract for the Todo app (no auth) is:

| Method | Endpoint              | Description     | Request Body      |
| ------ | --------------------- | --------------- | ----------------- |
| GET    | `/api/todos`          | Get all todos   | —                 |
| POST   | `/api/todos`          | Create a todo   | `{ title }`       |
| PATCH  | `/api/todos/:todo_id` | Toggle complete | `{ is_complete }` |
| DELETE | `/api/todos/:todo_id` | Delete a todo   | —                 |

A todo object looks like:

```json
{
  "todo_id": 1,
  "title": "Buy groceries",
  "is_complete": false
}
```

### API Fetching Layer

When building frontend applications with Vanilla JS, we kept all functions with `fetch` calls in a separate file called `fetch-helpers.js`. This was our "API Layer":

{% code title="fetch-helpers.js" %}
```javascript
export const fetchAllTodos = async () => {
  try {
    const response = await fetch('/api/todos');
    if (!response.ok) return Error(`Error ${response.status} - ${response.statusText}`)
    const data = await response.json();
    return { data, error: null };
  } catch (error) {
    return { data: null, error };
  }
};

export const createTodo = async (title) => {
  try {
    const response = await fetch('/api/todos', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title }),
    });
    if (!response.ok) return Error(`Error ${response.status} - ${response.statusText}`)
    const data = await response.json();
    return { data, error: null };
  } catch (error) {
    return { data: null, error };
  }
};
```
{% endcode %}

This is the same pattern we can use when building React frontends — a dedicated file that handles API communication, completely separate from your UI code. Moving to React doesn't change how we talk to APIs. The fetch logic is the same whether the UI is written in Vanilla JS or React. It is the layer that communicates with your server, and it stays the same regardless of your UI framework.

Keeping fetch logic isolated:
- Makes components easier to read (no long fetch calls mixed into JSX)
- Makes helpers reusable across multiple components
- Makes it easy to swap the API URL or add error handling in one place

## The Fetching Pattern: Load → Mutate → Reload

When building a full stack application, there are three key times when the frontend will fetch data from an API.

1. **When a page first loads**—fetching existing data in the database to show to the user for the current page
2. **When the user changes something**—sending a POST, PATCH, or DELETE request
3. **After the user action resolves**—re-fetching data to get sync'ed with the database

For example, consider the following user interactions with our todo app:
* The user visits our application in their browser 
  * → the application sends a GET request to fetch all existing todos and display them
* The user fills out the form to add a new todo 
  * → the application sends a POST request to store the todo in the database
  * → the application sends a GET request to fetch the up-to-date todos and display them

Import these helpers into the `App.jsx` file:

```jsx
import { fetchAllTodos, createTodo } from './fetch-helpers';
```

## Fetch on Load with useEffect

`useEffect()` is a React hook for executing "side effect" logic caused by a component rendering. Let's break that definition down.

In React, rendering a component is meant to be a pure operation: we provide props and state values and React immediately returns JSX to be rendered. However, when we render our root `App` component we also want it to:
1. Fetch the todos from our server
2. Wait for a response
3. Render the resulting data

Fetching code is considered a **side effect** since it takes time and can produce varied results. Remember, pure functions don't like side effects!

`useEffect` addresses this by allowing us to define some logic that will occur *after* the component renders. This lets the component rendering process to remain pure. 

{% hint style="info" %}
💡 You can think of `useEffect` kind of like registering an event handler where the "event" is the component rendering.
{% endhint %}

Import `useEffect` into `App.jsx`, alongside `useState`:

```jsx
import { useState, useEffect } from 'react';
```

### useEffect Syntax

`useEffect` takes in two arguments:

1. A callback function that performs some "side effect" logic after a component renders (e.g. fetching from an API).
2. (optional) A "dependency array" that controls if/when the side effect can run again (e.g. when the component re-renders)

```jsx
import { useState, useEffect } from 'react';

// ...

function App() {
  const [todos, setTodos] = useState([]);

  // 1. Define a callback
  const sideEffect = () => {
    console.log('App rendered!');
  }
  // 2. Set up sideEffect to run after the component renders
  useEffect(sideEffect);

  return (
    <main>
      <h1>My Todos</h1>
      <AddTodoForm setTodos={setTodos} />
      <TodoList todos={todos} />
    </main>
  );
}
```

Just like `useState` and every other hook, `useEffect` should be invoked at the top-most scope of component (never inside of an `if` statement, loop, or function).

### The Dependency Array

When `useEffect` is invoked with just the callback and no second argument, the callback is executed every single time a component renders. This isn't always desirable, particularly for time-consuming operations like fetching—we only want to fetch when absolutely necessary.

The second argument to `useEffect`, the **dependency array**, controls if/when the given callback is executed again. There are two ways we use it:

```jsx
useEffect(sideEffect);              // execute after EVERY re-render
useEffect(sideEffect, []);          // execute only once (on "mount")
useEffect(sideEffect, [a, b]);      // re-run whenever a or b changes
```

* **No dependency array**: runs after every render — usually not what you want
* **Empty array `[]`**: runs once when the component first "mounts" — ideal for loading initial data
* **Array with values**: re-runs when any of those values change between renders (e.g. a user logs out and a `currentUser` state becomes `null`)

### Async Effect Callback

Now, let's try to make a side effect callback that fetches from our Todos API with the `fetchAllTodos` fetch helper. 

Since the helper returns a Promise, we'll use `async`/`await` to make the callback. We'll also use an empty dependency array `[]` so that the effect just runs once.

```jsx
const loadTodos = async () => {
  const { data, error } = await fetchAllTodos();
  if (error) return console.error(error);
  setTodos(data);
};

// This throws an error — you cannot make the useEffect callback async directly
useEffect(loadTodos, []);
```

Unfortunately, due to the way that React handles effects internally, we can't provide `async` callbacks directly to `useEffect()` because they return Promises. Instead, React expects that the `useEffect` callback returns `undefined` or a [cleanup function](https://react.dev/learn/synchronizing-with-effects#step-3-add-cleanup-if-needed).

The workaround is actually quite simple: just create an anonymous function that invokes our side effect:

```jsx
const loadTodos = async () => {
  const { data, error } = await fetchAllTodos();
  if (error) return console.error(error);
  setTodos(data);
};

// Pass in an anonymous "wrapper" that invokes loadTodos
useEffect(() => {  
  loadTodos();
}, []);
```

The flow:
1. Component mounts → `useEffect` fires and invokes `loadTodos()`
2. `loadTodos` fetches `GET /api/todos`
3. Response comes back → `setTodos(data)` triggers a re-render
4. Component re-renders with the list of todos (and does NOT trigger the effect)

**<details><summary>Q: What would this flow look like if we removed the dependency array?</summary>**

We said earlier that fetching on every render would slow down our application because fetching is a time-consuming application. 

This is true but doesn't capture fully what it would really look like if we removed the dependency array. Give it a try and you'll see that the application comes to a complete halt. The reason why illuminates clearly how React rendering cycle works.

The flow would become:
1. Component mounts → `useEffect` fires and invokes `loadTodos()`
2. `loadTodos` fetches `GET /api/todos`
3. Response comes back → `setTodos(data)` triggers a re-render
4. Component re-renders with the list of todos → `useEffect` fires and invokes `loadTodos()`
5. `loadTodos` fetches `GET /api/todos`
6. Response comes back → `setTodos(data)` triggers a re-render
7. Component re-renders with the list of todos → `useEffect` fires and invokes `loadTodos()`
8. And so on...

The result is an infinite loop!

</details>

### Dog App Challenge

Build a simple Vite project called `dog-app` that loads a random dog image from the endpoint `https://dog.ceo/api/breeds/image/random` on load and renders the image. Add a button that fetches and replaces the dog image.

State required:
* `dogImage` (defaults to `''` on load, set to the dog image when the fetch returns)

**<details><summary>App.jsx Solution</summary>**

```jsx
import { useState, useEffect } from 'react'
import './App.css'

function App() {
  const [dogImage, setDogImage] = useState('');

  const loadDogImage = async () => {
    const response = await fetch('https://dog.ceo/api/breeds/image/random');
    const data = await response.json();
    setDogImage(data.message);
  }

  useEffect(() => {
    loadDogImage();
  }, [])

  return (
    <main>
      <img src={dogImage} alt="A picture of a random dog" />
      <button onClick={loadDogImage}>Random Dog</button>
    </main>
  )
}

export default App

```
</details>


## Mutate and Re-Fetch with Event Handlers

We've taken care of the first step: fetching todos when the page first loads. 

Now, a user will see the form and the list of todos. They should be able to fill out the form, submit their todo, and see the list update. This is where the **Mutate -> Refetch** pattern comes in.

Take a look at how we can update both `AddTodoForm` and `App` to accomplish this:

{% tabs %}

{% tab title="App" %}

In `App`, instead of passing down `setTodos`, we pass down the `loadTodos` function as a prop to `AddTodoForm`. This keeps the ownership of updating the `todos` state in the `App` while still allowing the form to trigger the re-fetch.

```javascript
function App() {
  const [todos, setTodos] = useState([]);

  const loadTodos = async () => {
    const { data, error } = await fetchAllTodos();
    if (error) return console.error(error);
    setTodos(data);
  };

  useEffect(() => {
    loadTodos();
  }, []);

  return (
    <main>
      <h1>My Todos</h1>
      <AddTodoForm loadTodos={loadTodos} />
      <TodoList todos={todos} />
    </main>
  );
}
```

{% endtab %}

{% tab title="AddTodoForm" %}

In the `AddTodoForm`, we 
1. Receive `loadTodos` from the `App` as a prop instead of `setTodos`
2. invoke `createTodo` from `fetch-helpers.js` to send a POST request with the form inputs
3. use the `loadTodos` side effect to once again send a GET request for the updated todos. But this function is defined in the `App` so we need to receive it as a prop.

```jsx
const AddTodoForm = ({ loadTodos }) => {
  // This event handler is async now
  const handleSubmit = async (e) => {
    e.preventDefault();
    const form = e.target;
    const title = form.elements.title.value;
    if (!title) return;

    // Step 2: First send the POST request
    const { error } = await createTodo(title);
    if (error) return console.error(error);
    
    // Step 3: Refetch with a GET request
    await loadTodos();
    
    form.reset();
  };

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="title-input">Todo:</label>
      <input type="text" name="title" id="title-input" />
      <button>Add</button>
    </form>
  );
};
```

{% endtab %}

{% endtabs %} 

Submit the form and check the console — you should see the new todo object in the database and the todo added to your list!

When fetching with event handlers in React, the flow looks like this:
1. User submits the form in `AddTodoForm` → `handleSubmit` is invoked
2. `createTodo()` fetches `POST /api/todos { title }`  → Response comes back
3. `loadTodos()` is invoked
4. `fetchAllTodos()` fetches `GET /api/todos` → Response comes back 
5. `setTodos()` updates the `todos` state and re-renders

This **refetch-after-write** pattern applies to any user action that produces a mutation request:

```
User action → mutate (POST/PATCH/DELETE) → refetch (GET) → re-render
```

**<details><summary>Q: Why refetch after mutating the list? Why not just insert a new TodoItem component into the list?</summary>**

In modern applications, many users may be sending in mutating requests at once. By refetching after performing a mutating action, the frontend treats the backend as the "source-of-truth". As a result, if other users were to make simultaneous mutation requests, we would see them all when we re-fetch. 

</details>

### Challenge: PATCH and DELETE

The API contract for the Todo app is:

| Method | Endpoint              | Description     | Request Body      |
| ------ | --------------------- | --------------- | ----------------- |
| GET    | `/api/todos`          | Get all todos   | —                 |
| POST   | `/api/todos`          | Create a todo   | `{ title }`       |
| PATCH  | `/api/todos/:todo_id` | Toggle complete | `{ is_complete }` |
| DELETE | `/api/todos/:todo_id` | Delete a todo   | —                 |

A todo object looks like:

```json
{
  "todo_id": 1,
  "title": "Buy groceries",
  "is_complete": false
}
```

Our application doesn't use the PATCH or DELETE endpoints at all. Update the todo application such that each todo has a checkbox to toggle it complete/incomplete and a button to delete it.

The target HTML for each todo item is (the title and `checked` value may vary):

```html
<li class="todo-item">
  <span>Take out the trash</span>
  <div class="todo-item-controls">
    <input type="checkbox" checked=true />
    <button>Delete</button>
  </div>
</li>
```

Start with adding a checkbox for the `PATCH` request

**Step 1 — Add a fetch helper to `fetch-helpers.js`**

You'll need a new async function `toggleTodo` that sends a `PATCH /api/todos/:todo_id` request with `{ is_complete }` in the body

Questions:
* What does the function need as parameters?
* What should the function return?

**Step 2 — Add the checkbox to `TodoItem`**

1. Add a checkbox to `TodoItem`'s JSX and import `toggleTodo` from `fetch-helpers.js`. 
2. Add an `onChange` prop and a `handleChange` event handler inside `TodoItem` that calls `toggleTodo()`.

Questions:
* The checkbox needs a `checked` attribute. What value should it be set to?
* The `onChange` handler receives an event `e`. How do you get the new checked value from `e`? What arguments does `toggleTodo` expect?

At this point your handler can send the PATCH request — but how do you get the UI to update afterward? You need to call `loadTodos()`, but `loadTodos` is defined in `App`. How do you get it into `TodoItem`?

**Step 3 — Pass `loadTodos` down as a prop**

Pass `loadTodos` from `App` down through `TodoList` to `TodoItem`, then call it inside `handleChange` after the fetch resolves.

Questions:
* What props does `TodoList` need to accept and pass through to `TodoItem`?
* Why can't you just define a new `loadTodos` inside `TodoItem` instead? Think about where `setTodos` lives.

**Step 4 — Add the delete button on your own**

Using everything you've just done as a model, add a delete button to `TodoItem` that calls `deleteTodo` and then re-fetches.

## Quiz

* When should you `fetch` using `useEffect` vs. an event handler?
* What is the purpose of the dependency array in `useEffect`?
* Why can't the `useEffect` callback itself be `async`?
* What is the refetch-after-write pattern and why does it keep the UI accurate?
* Why do we put fetch logic in `fetch-helpers.js` instead of directly in components?
