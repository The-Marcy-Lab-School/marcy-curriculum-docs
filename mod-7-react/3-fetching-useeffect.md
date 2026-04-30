# 3. Fetching with useEffect

{% hint style="info" %}
Follow along with code examples in the lecture repo!
{% endhint %}

We've already learned about `useState`. Time for another hook! In this lesson, we'll learn how to use the `useEffect` hook to connect our React frontend to a real backend API.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [The Todo API](#the-todo-api)
- [Isolating Fetch Logic: fetch-helpers.js](#isolating-fetch-logic-fetch-helpersjs)
- [Fetching with Event Handlers](#fetching-with-event-handlers)
- [useEffect](#useeffect)
  - [useEffect Syntax](#useeffect-syntax)
  - [The Effect Callback](#the-effect-callback)
  - [The Dependency Array](#the-dependency-array)
- [GET: Fetch Todos on Mount](#get-fetch-todos-on-mount)
- [POST: Add a Todo](#post-add-a-todo)
- [PATCH and DELETE: Mutations in TodoItem](#patch-and-delete-mutations-in-todoitem)
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

Starting today, our React app connects to a real Express + Postgres backend. The API contract for the Todo app (no auth) is:

| Method | Endpoint           | Description        | Request Body         |
| ------ | ------------------ | ------------------ | -------------------- |
| GET    | `/api/todos`       | Get all todos      | —                    |
| POST   | `/api/todos`       | Create a todo      | `{ title }`          |
| PATCH  | `/api/todos/:id`   | Update a todo      | `{ title?, is_complete? }` |
| DELETE | `/api/todos/:id`   | Delete a todo      | —                    |

A todo object looks like:

```json
{
  "id": 1,
  "title": "Buy groceries",
  "is_complete": false
}
```

> **Vite proxy**: A `vite.config.js` is provided in the starter code. It forwards any request that starts with `/api` to the Express server running on port 8080. You don't need to worry about it yet — we'll look at it closely in Day 4.

## Isolating Fetch Logic: fetch-helpers.js

Before we write any React components, let's talk about where the fetch code lives.

We keep all `fetch` calls in a separate file called **`fetch-helpers.js`**. This file has no JSX and no React — it only knows how to talk to the API.

```js
// src/fetch-helpers.js

export const fetchAllTodos = async () => {
  const response = await fetch('/api/todos');
  const data = await response.json();
  return data;
};

export const createTodo = async (title) => {
  const response = await fetch('/api/todos', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title }),
  });
  const data = await response.json();
  return data;
};

export const updateTodo = async (id, updates) => {
  const response = await fetch(`/api/todos/${id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates),
  });
  const data = await response.json();
  return data;
};

export const deleteTodo = async (id) => {
  await fetch(`/api/todos/${id}`, { method: 'DELETE' });
};
```

**Why separate this out?**

This is the same pattern you used when building Vanilla JS frontends — a dedicated file that handles API communication, completely separate from your UI code. Moving to React doesn't change how we talk to APIs. The fetch logic is the same whether the UI is written in Vanilla JS or React. It is the layer that communicates with your server, and it stays the same regardless of your UI framework.

Keeping fetch logic isolated:
- Makes components easier to read (no long fetch calls mixed into JSX)
- Makes helpers reusable across multiple components
- Makes it easy to swap the API URL or add error handling in one place

## Fetching with Event Handlers

We already know how to fetch inside an event handler — call an async helper on a button click, update state with the result. That works great for mutations (POST, PATCH, DELETE) triggered by the user.

But what about loading the initial list of todos when the page first loads? We don't want to wait for a button click — we want the fetch to happen automatically.

## useEffect

There are two ways to perform a side effect like fetching:

1. In response to user events
2. In response to the component rendering ("reacting to the component rendering")

We can accomplish the second with the hook `useEffect` — a React hook for executing "side effects" caused by a component rendering, not a particular event.

> **Q: How do we know that this is a hook?**

### useEffect Syntax

`useEffect` takes in two arguments:

1. A callback function (the "effect")
2. \[optional] A "dependency array"

It should be invoked at the top of the component, next to the other hooks used by the component (often below `useState`):

```jsx
import { useEffect } from 'react';

function App() {
  useEffect(() => {
    // this runs after the component renders
    console.log('App rendered!');
  }, []);

  return <h1>Hello</h1>;
}
```

### The Effect Callback

Why do we need to create an inner `async` function instead of making the callback itself async?

```jsx
// This throws an error — you cannot make the useEffect callback async directly
useEffect(async () => {
  const todos = await fetchAllTodos();
  setTodos(todos);
}, []);
```

So inside the callback, we define an `async` function and then immediately invoke it:

```jsx
useEffect(() => {
  const loadTodos = async () => {
    const todos = await fetchAllTodos();
    setTodos(todos);
  };
  loadTodos();
}, []);
```

### The Dependency Array

`useEffect(effect, dependencyArray)` can receive the dependency array in three ways:

```jsx
useEffect(effect);              // execute after EVERY re-render
useEffect(effect, []);          // execute only once (on mount)
useEffect(effect, [a, b]);      // re-run whenever a or b changes
```

* **No array**: runs after every render — usually not what you want
* **Empty array `[]`**: runs once when the component first mounts — ideal for loading initial data
* **Array with values**: re-runs when any of those values change between renders

## GET: Fetch Todos on Mount

Let's load todos when the app first renders using `useEffect` with an empty dependency array:

```jsx
import { useState, useEffect } from 'react';
import { fetchAllTodos } from './fetch-helpers';

function App() {
  const [todos, setTodos] = useState([]);

  useEffect(() => {
    const loadTodos = async () => {
      const data = await fetchAllTodos();
      setTodos(data);
    };
    loadTodos();
  }, []); // empty array = run once on mount

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

The flow:
1. Component mounts → `useEffect` fires
2. `loadTodos` fetches `GET /api/todos`
3. Response comes back → `setTodos(data)` triggers a re-render
4. Component re-renders with the list of todos

## POST: Add a Todo

Controlled form + POST on submit + refetch:

```jsx
import { useState, useEffect } from 'react';
import { fetchAllTodos, createTodo } from './fetch-helpers';

function App() {
  const [todos, setTodos] = useState([]);
  const [title, setTitle] = useState('');

  const loadTodos = async () => {
    const data = await fetchAllTodos();
    setTodos(data);
  };

  useEffect(() => {
    loadTodos();
  }, []);

  const handleSubmit = async (e) => {
    e.preventDefault();
    await createTodo(title);
    setTitle('');
    loadTodos(); // refetch to stay in sync with the server
  };

  return (
    <>
      <form onSubmit={handleSubmit}>
        <input
          value={title}
          onChange={(e) => setTitle(e.target.value)}
          placeholder="New todo..."
        />
        <button>Add</button>
      </form>
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>
    </>
  );
}
```

Notice that `loadTodos` is now defined outside the `useEffect` so it can be called both on mount and after form submission. This is the **refetch-after-write** pattern:

```
User action → mutate (POST/PATCH/DELETE) → refetch (GET) → re-render
```

## PATCH and DELETE: Mutations in TodoItem

Both PATCH and DELETE follow the same shape: call the helper, then call `onRefresh()` to trigger a refetch in `App`. We can put both handlers in `TodoItem`:

```jsx
import { updateTodo, deleteTodo } from './fetch-helpers';

function TodoItem({ todo, onRefresh }) {
  const handleToggle = async () => {
    await updateTodo(todo.id, { is_complete: !todo.is_complete });
    onRefresh();
  };

  const handleDelete = async () => {
    await deleteTodo(todo.id);
    onRefresh();
  };

  return (
    <li>
      <button onClick={handleToggle}>
        {todo.is_complete ? '✅' : '⬜'}
      </button>
      <span style={{ textDecoration: todo.is_complete ? 'line-through' : 'none' }}>
        {todo.title}
      </span>
      <button onClick={handleDelete}>🗑️</button>
    </li>
  );
}
```

`App` passes its `loadTodos` function down as the `onRefresh` prop:

```jsx
<ul>
  {todos.map((todo) => (
    <TodoItem key={todo.id} todo={todo} onRefresh={loadTodos} />
  ))}
</ul>
```

## Quiz

* When should you `fetch` using `useEffect` vs. an event handler?
* What is the purpose of the dependency array in `useEffect`?
* Why can't the `useEffect` callback itself be `async`?
* What is the refetch-after-write pattern and why does it keep the UI accurate?
* Why do we put fetch logic in `fetch-helpers.js` instead of directly in components?
