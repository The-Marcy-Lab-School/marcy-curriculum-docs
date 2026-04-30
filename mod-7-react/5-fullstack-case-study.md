# 5. Full-Stack Case Study: Todo App with Auth

{% hint style="info" %}
Follow along with code examples in the `swe-casestudy-7-todo-app/` lecture repo!
{% endhint %}

Over the past few days you have built a full-stack Todo app: an Express + Postgres backend and a React frontend that fetches, creates, updates, and deletes todos. Today we add **authentication**.

No new React concepts are introduced here. This lesson is about composing everything you have already learned — `useState`, `useEffect`, controlled forms, and conditional rendering — with authentication.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [What Changed on the Backend](#what-changed-on-the-backend)
- [Fetch Helpers: Organizing by Domain](#fetch-helpers-organizing-by-domain)
- [Session Rehydration](#session-rehydration)
- [Auth-Dependent Effects](#auth-dependent-effects)
- [currentUser as a Prop](#currentuser-as-a-prop)
- [Conditional Rendering: Login vs. App](#conditional-rendering-login-vs-app)
- [Putting It All Together](#putting-it-all-together)
- [Your Project Will Follow This Shape](#your-project-will-follow-this-shape)

## Essential Questions

By the end of this lesson you should be able to answer:

1. What is session rehydration, and why does React state alone not survive a page refresh?
2. What does it mean for an effect to be "auth-dependent," and how does `currentUser` in a dependency array achieve this?
3. Why do we pass `currentUser` as a prop rather than using global state?
4. What is the adapter pattern, and why split fetch helpers into domain-specific files instead of one large file?
5. How does conditional rendering let a single-page app show different views for logged-in vs. logged-out users?

## Key Concepts

* **Session rehydration** — Using `GET /api/auth/me` on mount to restore the logged-in user from a server-side session cookie. React state doesn't survive a page refresh, but session cookies do.
* **Auth-dependent effects** — A `useEffect` with `currentUser` in its dependency array. The effect re-runs when the user logs in or out.
* **Props drilling** — Passing `currentUser` (and auth handlers) down through the component tree as props. No global Context required for a single-level app.
* **Adapter pattern** — Organizing fetch helpers into domain-specific files (`auth-helpers.js`, `todo-helpers.js`) rather than one large file.

## What Changed on the Backend

The same Todo server from Day 3 now has four additional auth endpoints:

| Method | Endpoint              | Description                        | Body                         |
| ------ | --------------------- | ---------------------------------- | ---------------------------- |
| POST   | `/api/auth/register`  | Create account + log in            | `{ username, password }`     |
| POST   | `/api/auth/login`     | Log in, start session              | `{ username, password }`     |
| DELETE | `/api/auth/logout`    | End session                        | —                            |
| GET    | `/api/auth/me`        | Return current user from session   | —                            |

`GET /api/auth/me` returns the logged-in user object (e.g. `{ id: 1, username: 'alice' }`) if a valid session cookie is present, or `null` if not logged in.

The todo endpoints now require authentication and only return todos that belong to the logged-in user.

## Fetch Helpers: Organizing by Domain

With auth in the picture we have two distinct concerns: auth operations and todo operations. Instead of cramming everything into one `fetch-helpers.js`, we split into domain-specific files. This pattern is called the **adapter pattern** — each file is an adapter between your UI and one slice of the API.

```js
// src/auth-helpers.js

export const register = async (username, password) => {
  const response = await fetch('/api/auth/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password }),
  });
  return response.json();
};

export const login = async (username, password) => {
  const response = await fetch('/api/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password }),
  });
  return response.json();
};

export const logout = async () => {
  await fetch('/api/auth/logout', { method: 'DELETE' });
};

export const getMe = async () => {
  const response = await fetch('/api/auth/me');
  return response.json();
};
```

```js
// src/todo-helpers.js

export const fetchAllTodos = async () => {
  const response = await fetch('/api/todos');
  return response.json();
};

export const createTodo = async (title) => {
  const response = await fetch('/api/todos', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title }),
  });
  return response.json();
};

export const updateTodo = async (id, updates) => {
  const response = await fetch(`/api/todos/${id}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates),
  });
  return response.json();
};

export const deleteTodo = async (id) => {
  await fetch(`/api/todos/${id}`, { method: 'DELETE' });
};
```

**Q: Why is splitting into domain files better than one big `fetch-helpers.js`?**

Each file has one responsibility. When the auth API changes, you only touch `auth-helpers.js`. When the todo API changes, you only touch `todo-helpers.js`. Compare that to a single large file where every concern is mixed together.

## Session Rehydration

React state lives in memory. When the user refreshes the page, all state is reset — `currentUser` goes back to `null`.

But **session cookies** survive page refreshes. If the user logged in yesterday, their session cookie is still in the browser. We just need to ask the server "is there a session?"

We do this on mount with `GET /api/auth/me`:

```jsx
const [currentUser, setCurrentUser] = useState(null);

useEffect(() => {
  const rehydrate = async () => {
    const user = await getMe();
    setCurrentUser(user); // null if not logged in, user object if logged in
  };
  rehydrate();
}, []); // empty array = run once on mount
```

**The flow:**
1. Page loads → component mounts → `useEffect` fires
2. `GET /api/auth/me` → server checks session cookie
3. If valid session: returns user object → `setCurrentUser(user)` → React re-renders showing the app
4. If no session: returns `null` → `setCurrentUser(null)` → React re-renders showing the login form

This is **session rehydration**: restoring React state from a server-side session on page load.

## Auth-Dependent Effects

The todo list should only be fetched after we know who the user is. If we fetch on mount unconditionally, we might try to load todos before the server knows who is logged in.

The solution: put `currentUser` in the dependency array so the fetch happens (or re-happens) when the user changes.

```jsx
const [todos, setTodos] = useState([]);

const loadTodos = async () => {
  const data = await fetchAllTodos();
  setTodos(data);
};

useEffect(() => {
  if (currentUser) {
    loadTodos();
  } else {
    setTodos([]); // clear todos on logout
  }
}, [currentUser]); // re-run whenever currentUser changes
```

**The flow:**
- On mount: `currentUser` is `null` → effect runs, skips the fetch
- After session rehydration: `currentUser` is set → effect re-runs, fetches todos
- After login: `currentUser` is set → effect re-runs, fetches todos
- After logout: `currentUser` is `null` → effect re-runs, clears todos

## currentUser as a Prop

`currentUser` is defined in `App` and needs to be available in child components — for example, to show the logged-in username or to protect the "Delete" button.

We pass it down as a prop:

```jsx
// App.jsx
return (
  <TodoList
    todos={todos}
    currentUser={currentUser}
    onRefresh={loadTodos}
  />
);
```

```jsx
// TodoList.jsx
function TodoList({ todos, currentUser, onRefresh }) {
  return (
    <ul>
      {todos.map((todo) => (
        <TodoItem
          key={todo.id}
          todo={todo}
          currentUser={currentUser}
          onRefresh={onRefresh}
        />
      ))}
    </ul>
  );
}
```

This is called **props drilling** — passing a value through multiple levels of the component tree. It's explicit and easy to follow. For a single-level app like this, it's the right approach.

> **Note**: For larger apps with many levels, Context can eliminate the repetition. That's covered in the bonus lesson.

## Conditional Rendering: Login vs. App

The app has two distinct views:
- **Not logged in**: show a login/register form
- **Logged in**: show the todo list

We use a ternary to switch between them based on `currentUser`:

```jsx
function App() {
  const [currentUser, setCurrentUser] = useState(null);
  // ... (rehydration useEffect, auth-dependent useEffect, loadTodos, etc.)

  const handleLogin = async (username, password) => {
    const user = await login(username, password);
    setCurrentUser(user);
  };

  const handleLogout = async () => {
    await logout();
    setCurrentUser(null);
  };

  return (
    <main>
      <h1>Todo App</h1>
      {currentUser
        ? <TodoApp
            todos={todos}
            currentUser={currentUser}
            onLogout={handleLogout}
            onRefresh={loadTodos}
          />
        : <AuthForm onLogin={handleLogin} onRegister={handleRegister} />
      }
    </main>
  );
}
```

A single ternary controls the entire app. No routing needed for a two-view app.

## Putting It All Together

Here is the complete data flow for the authenticated Todo app:

```
Page loads
  └─ useEffect([]) fires
       └─ GET /api/auth/me
            ├─ Session exists → setCurrentUser(user)
            │    └─ useEffect([currentUser]) fires
            │         └─ GET /api/todos → setTodos(data)
            └─ No session → setCurrentUser(null)
                 └─ Show <AuthForm />

User logs in (via AuthForm)
  └─ POST /api/auth/login → setCurrentUser(user)
       └─ useEffect([currentUser]) fires
            └─ GET /api/todos → setTodos(data)
            └─ Show <TodoApp />

User mutates a todo
  └─ POST/PATCH/DELETE /api/todos/:id
       └─ loadTodos() → GET /api/todos → setTodos(data)

User logs out
  └─ DELETE /api/auth/logout → setCurrentUser(null)
       └─ useEffect([currentUser]) fires → setTodos([])
       └─ Show <AuthForm />
```

## Your Project Will Follow This Shape

The full-stack project you're about to build follows exactly this same structure:

1. **Session rehydration** on mount with `GET /api/auth/me`
2. **Auth-dependent fetch** with `currentUser` in the dependency array
3. **`currentUser` passed as a prop** (no Context required)
4. **Conditional rendering** to toggle between auth UI and app UI
5. **Refetch-after-write** for all mutations

For bonus features — multiple pages (React Router), or global `currentUser` state (Context) — see the bonus lessons.
