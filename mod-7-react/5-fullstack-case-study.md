# 5. Full-Stack Case Study: Todo App with Auth

{% hint style="info" %}
Follow along with code examples in the lecture [repo](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app)!
{% endhint %}

Over the past few days you have built a full-stack Todo app: an Express + Postgres backend and a React frontend that fetches, creates, updates, and deletes todos. Today we add **authentication**.

Most of this lesson is about composing everything you have already learned — `useState`, `useEffect`, controlled forms, and conditional rendering — with authentication. We also introduce one new JSX pattern: **short-circuit `&&`** for rendering elements conditionally.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Adding Auth to the Todo App](#adding-auth-to-the-todo-app)
- [Separation of Concerns](#separation-of-concerns)
  - [Fetch Adapters by Domain](#fetch-adapters-by-domain)
  - [Components Folder](#components-folder)
- [The App Component](#the-app-component)
  - [What does a user see when they first visit the page?](#what-does-a-user-see-when-they-first-visit-the-page)
  - [What happens after a user logs in or registers?](#what-happens-after-a-user-logs-in-or-registers)
  - [How does a returning user stay logged in?](#how-does-a-returning-user-stay-logged-in)
- [The TodoPage Component](#the-todopage-component)
  - [Why is `loadTodos` defined outside of `useEffect`?](#why-is-loadtodos-defined-outside-of-useeffect)
  - [What does the user see while todos are loading?](#what-does-the-user-see-while-todos-are-loading)
  - [How does `TodoItem` handle mutations?](#how-does-todoitem-handle-mutations)
- [Putting It All Together](#putting-it-all-together)
- [Your Project Will Follow This Shape](#your-project-will-follow-this-shape)

## Essential Questions

By the end of this lesson you should be able to answer:

1. Why doesn't React state survive a page refresh, and how does `GET /api/auth/me` solve that problem?
2. Why does `TodoPage` fetch todos with `useEffect([])` rather than needing `currentUser` in the dependency array?
3. How do `isLoading` and `error` state improve the user experience during and after a fetch?
4. Why are `handleLogin`, `handleRegister`, and `handleLogout` defined in `App` rather than in the form components?
5. What is the adapter pattern, and why split fetch helpers into domain-specific files instead of one large file?
6. How can the ternary operator and `&&` short circuiting be used to conditionally render JSX?

## Key Concepts

* **Session check on mount** — On every page load, `App` calls `GET /api/auth/me` to ask the server if a session cookie is present. If it is, `currentUser` is set to the logged-in user. If not, it stays `null`. React state doesn't survive a page refresh, but session cookies do.
* **Conditional rendering with auth** — `TodoPage` only renders when `currentUser` is truthy while `AuthPage` is rendered when `currentUser` is `null`.
* **Loading state** — A boolean (`isLoading`) that is `true` while a fetch is in flight. Used to render a placeholder instead of an empty UI.
* **Error state** — A string stored in state so fetch failures are surfaced to the user rather than silently swallowed.
* **Props drilling** — Passing `currentUser` and handlers down through the component tree as props. No global Context required for a single-level app.
* **`&&` Short Circuiting** — The `&&` operator returns the first falsy value it encounters, or the last value if all are truthy. In JSX, this means `{condition && <Component />}` renders the component when `condition` is truthy and renders nothing when it is falsy. Use it to show loading indicators, error messages, or any element that should appear or disappear based on a single boolean.
* **Adapter pattern** — Organizing fetch helpers into domain-specific files (`auth-adapters.js`, `todo-adapters.js`) rather than one large file.

## Adding Auth to the Todo App

The same Todo server from Day 3 now has four additional auth endpoints with session cookie logic built in. These endpoints should be familiar to you — they are written exactly as we saw them in the Databases module:

| Method | Endpoint             | Description                             | Body                     |
| ------ | -------------------- | --------------------------------------- | ------------------------ |
| POST   | `/api/auth/register` | Create account + log in + start session | `{ username, password }` |
| POST   | `/api/auth/login`    | Log in, start session                   | `{ username, password }` |
| DELETE | `/api/auth/logout`   | End session                             | —                        |
| GET    | `/api/auth/me`       | Return current user from session        | —                        |

* The `/api/auth/register` and `/api/auth/login` endpoints each create and send a session cookie to the client.
* The `/api/auth/me` endpoint returns the logged-in user object (e.g. `{ user_id: 1, username: 'alice' }`) if a valid session cookie is present, or `null` if not logged in.
* The `/api/auth/logout` endpoint destroys the session cookie.

Additionally, the todo endpoints now require authentication and only return todos that belong to the logged-in user.

## Separation of Concerns

### Fetch Adapters by Domain

With auth in the picture we have two distinct concerns: auth operations and todo operations. Instead of cramming everything into one `fetch-helpers.js`, we split into domain-specific files: `auth-adapters.js` and `todo-adapters.js`.

In React, this "adapter" terminology comes from the idea that this layer acts as a bridge that translates the data from your API and adapts the data to be used seamlessly with your React application.

{% tabs %}

{% tab title="Auth Adapters" %}

{% code title="src/adapters/auth-adapters.js" %}

```javascript
const handleFetch = async (url, options = {}) => {
  try {
    const response = await fetch(url, options);
    if (!response.ok) throw new Error(`Fetch failed. ${response.status} ${response.statusText}`);
    const data = await response.json();
    return { data, error: null };
  } catch (error) {
    return { data: null, error };
  }
};

export const getMe = async () => {
  return handleFetch('/api/auth/me');
};

export const register = async (username, password) => {
  return handleFetch('/api/auth/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password }),
  });
};

export const login = async (username, password) => {
  return handleFetch('/api/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password }),
  });
};

export const logout = async () => {
  return handleFetch('/api/auth/logout', { method: 'DELETE' });
};
```
{% endcode %}

{% endtab %}

{% tab title="Todo Adapters" %}

{% code title="src/adapters/todo-adapters.js" %}

```javascript
const handleFetch = async (url, options = {}) => {
  try {
    const response = await fetch(url, options);
    if (!response.ok) throw new Error(`Fetch failed. ${response.status} ${response.statusText}`);
    const data = await response.json();
    return { data, error: null };
  } catch (error) {
    return { data: null, error };
  }
};

export const fetchAllTodos = async () => {
  return handleFetch('/api/todos');
};

export const createTodo = async (title) => {
  return handleFetch('/api/todos', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title }),
  });
};

export const updateTodo = async (todo_id, updates) => {
  return handleFetch(`/api/todos/${todo_id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates),
  });
};

export const deleteTodo = async (todo_id) => {
  return handleFetch(`/api/todos/${todo_id}`, { method: 'DELETE' });
};
```
{% endcode %}

{% endtab %}

{% endtabs %}

**<details><summary>Q: Why is splitting into domain files better than one big `fetch-helpers.js`?</summary>**

Each file has one responsibility. When the auth API changes, you only touch `auth-adapters.js`. When the todo API changes, you only touch `todo-adapters.js`. Compare that to a single large file where every concern is mixed together.

</details>

### Components Folder

Take a look at `App.jsx` and notice that it now just houses the `App` component. As React applications grow, we can't have all components in a single file. Instead, it is common to have the `App.jsx` file live at the root of the `src` folder and import components from a `components/` directory.

Each component lives in its own file in this directory so that it can be exported and imported anywhere in the application:

```
swe-casestudy-7-todo-app/
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── adapters/
│   │   │   ├── auth-adapters.js
│   │   │   └── todo-adapters.js
│   │   └── components/
│   │       ├── AuthPage.jsx
│   │       ├── TodoPage.jsx
│   │       ├── AddTodoForm.jsx
│   │       ├── TodoList.jsx
│   │       └── TodoItem.jsx
│   └── vite.config.js
└── server/
```

## The App Component

`App` has one job: manage who is logged in and decide which page to show based on that information. It owns `currentUser` state, defines the handlers that can change it, and renders either `TodoPage` or `AuthPage` based on whether a user is logged in.

### What does a user see when they first visit the page?

**When a user first visits the app, are they logged in or logged out? How does `App` decide which component to render?**

<details><summary>Answer</summary>

When the app first loads, `currentUser` is initialized to `null` — no one is logged in yet. `App`'s return statement uses a **ternary** to switch between two views based on this value:

```jsx
const [currentUser, setCurrentUser] = useState(null);

//...

return (
  <main>
    <h1>Todo App</h1>
    {currentUser
      ? <TodoPage currentUser={currentUser} handleLogout={handleLogout} />
      : <AuthPage handleLogin={handleLogin} handleRegister={handleRegister} />
    }
  </main>
);
```

Since `null` is falsy, the ternary evaluates to `<AuthPage>`. The moment `currentUser` is set to a user object, the ternary re-evaluates and `TodoPage` replaces `AuthPage`.

**Key Takeaway:** A ternary like this is commonly used when you want to render one thing _or_ another based on a state value: `condition ? <A /> : <B />`. A single expression controls the entire app's view.

</details>

### What happens after a user logs in or registers?

**After a user submits the login form, what happens to `currentUser`? Trace the path from form submission all the way to the UI switching to `TodoPage`.**

<details><summary>Answer</summary>

`handleLogin` and `handleRegister` are defined in `App` — the component that owns `currentUser` state — and passed down as props to `AuthPage`:

```jsx
// App.jsx
const handleLogin = async (username, password) => {
  const { data: user, error } = await login(username, password);
  if (error) return error;
  setCurrentUser(user);
};

const handleRegister = async (username, password) => {
  const { data: user, error } = await register(username, password);
  if (error) return error;
  setCurrentUser(user);
};
```

`AuthPage` receives these as props and passes them further into its `LoginForm` and `RegisterForm` components, where they are invoked on form submission. When the API call succeeds, `setCurrentUser(user)` fires — React re-renders `App`, the ternary now evaluates to `<TodoPage>`, and the auth forms are replaced.

**Key Takeaway:** These handlers are defined in `App` rather than in the form components because `App` is the owner of `currentUser` state. The component that owns state should be the only place that controls how that state can change.

</details>

### How does a returning user stay logged in?

**A user logged in yesterday and closed the tab. When they open the app today, how do they end up on `TodoPage` without filling out the login form again?**

<details><summary>Answer</summary>

When the user logged in, the server created a **session cookie** and sent it to the browser. Even though cookies persist across page loads, React state does not—each time the user refreshes the browser tab, the previous `currentUser` state is deleted and then set to `null` again.

To handle this, `App` calls `GET /api/auth/me` on "mount" (the first time the component is rendered) to ask the server "is there an active session?":

```jsx
useEffect(() => {
  const checkForSession = async () => {
    const { data: user } = await getMe();
    setCurrentUser(user);
  };
  checkForSession();
}, []);
```

`GET /api/auth/me` checks the session cookie and returns the logged-in user object if a valid session exists, or `null` if not. Either way, `setCurrentUser` is called and the appropriate page component is rendered:

- **Session exists** → `setCurrentUser(user)` → `currentUser` is truthy → `TodoPage` renders
- **No session** → `setCurrentUser(null)` → `currentUser` is falsy → `AuthPage` renders

**Key Takeaway:** The session cookie is the source of truth; we use the `/api/auth/me` endpoint to ensure that the `currentUser` state in React state always mirrors it.

</details>

## The TodoPage Component

Once a user is logged in, `TodoPage` takes over. It owns all todo-related state — the list of todos, loading status, and any fetch errors. `App` passes in only `currentUser` (needed to display the username) and `handleLogout` (needed for the logout button).

### Why is `loadTodos` defined outside of `useEffect`?

Compare how `checkForSession` is written in `App` versus how `loadTodos` is written in `TodoPage`. In `App`, the async function is defined *inside* the effect. In `TodoPage`, `loadTodos` is defined *outside* of it:

```jsx
// App.jsx — checkForSession is only ever called once, on mount
useEffect(() => {
  const checkForSession = async () => {
    const { data: user } = await getMe();
    setCurrentUser(user);
  };
  checkForSession();
}, []);
```

```jsx
// TodoPage.jsx — loadTodos is defined outside the effect
const loadTodos = async () => { ... };

useEffect(() => {
  loadTodos();
}, []);
```

**Why is `loadTodos` defined outside of `useEffect`? What would break if it were defined inside the effect instead?**

<details><summary>Answer</summary>

`checkForSession` only ever needs to run once — on mount. Defining it inside the effect is fine because nothing else needs to call it.

`loadTodos` is different: it also runs on mount, but it needs to run again every time a todo is created, updated, or deleted. After any mutation, the component that performed the mutation calls `loadTodos()` to refetch the latest data from the server. This is the **refetch-after-write** pattern.

For example, the `TodoItem` component has these event handlers:

```jsx
const handleChange = async (e) => {
  const { error } = await updateTodo(todo.todo_id, { is_complete: e.target.checked });
  if (error) return console.error(error);
  loadTodos();
};

const handleDelete = async () => {
  const { error } = await deleteTodo(todo.todo_id);
  if (error) return console.error(error);
  loadTodos();
};
```

For child components to call `loadTodos`, it has to be passed down as a prop:

```jsx
// TodoPage.jsx
return (
  <>
    <AddTodoForm loadTodos={loadTodos} />
    <TodoList todos={todos} loadTodos={loadTodos} />
  </>
);
```

**Key Takeaway:** If `loadTodos` were defined inside `useEffect`, it would be scoped to that callback and inaccessible to the rest of the component — it couldn't be passed as a prop at all. Defining it at the component level makes it available everywhere it's needed.

</details>

### What does the user see while todos are loading?

**Between when `fetchAllTodos` function is invoked and when the server responds with todos, what does the user see? What if the request fails?**

<details><summary>Answer</summary>

Every fetch has three possible states: pending ("in flight"), succeeded, or failed. Without handling all three, the UI either shows a blank screen while loading or silently breaks on error. Two pieces of state make all three states visible: `isLoading` and `error`

```jsx
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState(null);

const loadTodos = async () => {
  setIsLoading(true);
  setError(null);
  const { data, error: fetchError } = await fetchAllTodos();
  if (fetchError) {
    setError(fetchError.message);
  } else {
    setTodos(data);
  }
  setIsLoading(false);
};
```

`TodoPage` renders feedback for each state using `&&`:

```jsx
{isLoading && <p>Loading todos...</p>}
{error && <p className="error">Something went wrong: {error}</p>}
```

The `&&` here is a new JSX pattern called **short-circuit rendering**. To understand why it works, recall how `&&` behaves in plain JavaScript. When both values are booleans it returns `true` or `false`:

```js
true && true    // → true
true && false   // → false
false && true   // → false
```

But with non-boolean values, `&&` evaluates left-to-right and returns the **first falsy value** it encounters — or the **last value** if all are truthy:

```js
false && "hello"   // → false   (stopped at false)
null && "hello"    // → null    (stopped at null)
true && "hello"    // → "hello" (both sides are truthy, returns right side)
0 && "hello"       // → 0       (stopped at 0, which is falsy)
```

This is called **short-circuiting**. When React renders `null`, `false`, or `undefined`, it renders nothing — no element, no space:

```jsx
{null && <p>Loading...</p>}    // → renders nothing
{false && <p>Loading...</p>}   // → renders nothing
{true && <p>Loading...</p>}    // → renders <p>Loading...</p>
```

So `{isLoading && <p>Loading todos...</p>}` works like this:
- When `isLoading` is `false` → `&&` short-circuits and returns `false` → React renders nothing
- When `isLoading` is `true` → `&&` evaluates the right side → React renders the `<p>`

Use `&&` when you want to show something **or nothing**. Use a ternary (`condition ? <A /> : <B />`) when you need to swap between two elements.

**Key Takeaway:** Your app should never show a blank screen without explanation. If there is a fetch that is "in flight" (pending), make sure to show the user something to indicate that the app is waiting for a response. Similarly, let the user know if an error has occurred. Use `&&` short-circuiting to simplify this rendering logic.

</details>

### How does `TodoItem` handle mutations?

**`TodoItem` can both toggle a todo's completion status and delete a todo. After either action succeeds, what needs to happen — and how does `TodoItem` trigger it?**

<details><summary>Answer</summary>

After any mutation (toggle or delete), the UI needs to reflect the latest data from the server. `TodoItem` does this by calling `loadTodos()` after a successful API call — the **refetch-after-write** pattern:

```jsx
function TodoItem({ todo, loadTodos }) {
  const handleChange = async (e) => {
    const { error } = await updateTodo(todo.todo_id, { is_complete: e.target.checked });
    if (error) return console.error(error);
    loadTodos();
  };

  const handleDelete = async () => {
    const { error } = await deleteTodo(todo.todo_id);
    if (error) return console.error(error);
    loadTodos();
  };

  return (
    <li className="todo-item">
      <input type="checkbox" checked={todo.is_complete} onChange={handleChange} />
      <span className={todo.is_complete ? 'completed' : ''}>{todo.title}</span>
      <button className="delete-btn" onClick={handleDelete}>Delete</button>
    </li>
  );
}
```

`loadTodos` is defined in `TodoPage` and passed down through the component tree as a prop: `TodoPage` → `TodoList` → `TodoItem`. Every component that performs a mutation receives `loadTodos` so it can trigger a refetch when it's done.

Notice that `TodoItem` never needs to know about `currentUser`. The auth guard is handled structurally: `TodoPage` only mounts when `currentUser` is truthy, so if `TodoItem` is rendering at all, a user is logged in.

**Key Takeaway:** After any mutation, call `loadTodos()` to sync the UI with the server. The `loadTodos` function is defined at the top level of `TodoPage` so it can be passed as a prop to any child that needs to trigger a refetch.

</details>

## Putting It All Together

Here is the complete data flow for the authenticated Todo app:

```
Page loads
  └─ App mounts → useEffect([]) fires
       └─ GET /api/auth/me
            ├─ Session exists → setCurrentUser(user) → TodoPage mounts
            │    └─ TodoPage useEffect([]) fires
            │         └─ GET /api/todos → setTodos(data)
            └─ No session → setCurrentUser(null) → AuthPage renders

User logs in (via AuthPage)
  └─ POST /api/auth/login → setCurrentUser(user) → TodoPage mounts
       └─ TodoPage useEffect([]) fires
            └─ GET /api/todos → setTodos(data)

User mutates a todo
  └─ POST/PATCH/DELETE /api/todos/:id
       └─ loadTodos() → GET /api/todos → setTodos(data)

User logs out
  └─ DELETE /api/auth/logout → setCurrentUser(null) → TodoPage unmounts
       └─ todos state destroyed automatically → AuthPage renders
```

## Your Project Will Follow This Shape

The full-stack project you're about to build follows exactly this same structure:

1. **Session check on mount** — call `GET /api/auth/me` to restore `currentUser` from the server's session cookie
2. **Fetch on mount** in your data page — the component only mounts when logged in, so no `currentUser` dependency needed
3. **`isLoading` and `error` state** on every fetch — no silent empty screens
4. **`currentUser` passed as a prop** (no Context required)
5. **Conditional rendering** to toggle between auth UI and app UI
6. **Refetch-after-write** for all mutations

For bonus features — multiple pages (React Router), or global `currentUser` state (Context) — see the bonus lessons.
