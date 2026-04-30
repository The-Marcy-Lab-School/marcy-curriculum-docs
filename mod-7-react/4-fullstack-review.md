# 4. Full-Stack Review

{% hint style="info" %}
Follow along with code examples in the lecture repo!
{% endhint %}

Today we revisit the Todo app we built on Day 3 and fill in the "how does this actually work?" gaps. No new React concepts — just a deeper look at the full-stack plumbing.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [The Vite Proxy](#the-vite-proxy)
  - [Why a Proxy?](#why-a-proxy)
  - [Reading vite.config.js](#reading-viteconfigjs)
- [Same-Origin vs. Cross-Origin](#same-origin-vs-cross-origin)
- [Loading State](#loading-state)
- [Error State](#error-state)

## Essential Questions

By the end of this lesson you should be able to answer:

1. Why do we need a Vite proxy in development, and what problem does it solve?
2. What is the difference between a same-origin and cross-origin request, and why does it matter for session cookies?
3. How does `express.static` connect to the React build output in production?
4. How do you use `isLoading` state to improve the user experience while a fetch is in flight?
5. How do you use `error` state to display a meaningful message when a fetch fails?

## Key Concepts

* **Vite proxy** — A development-only configuration that forwards `/api/*` requests from the Vite dev server to your Express server, avoiding CORS issues and making fetch URLs simpler.
* **Same-origin** — A request is same-origin when the scheme, host, and port all match. `fetch('/api/todos')` is always same-origin with the page serving it.
* **Cross-origin** — A request is cross-origin when the URL has a different origin than the page. Browsers restrict these by default (CORS).
* **Loading state** — A boolean piece of state that is `true` while a fetch is in flight and `false` when it resolves.
* **Error state** — A string or error object stored in state so fetch failures can be displayed to the user.

## The Vite Proxy

### Why a Proxy?

In development we run two servers simultaneously:

| Server      | Port | What it serves                     |
| ----------- | ---- | ---------------------------------- |
| Vite        | 5173 | Your React app (JSX → JS on the fly) |
| Express     | 8080 | Your API (`/api/*` routes)         |

Without a proxy, a `fetch('/api/todos')` from the Vite page (origin `http://localhost:5173`) would try to reach `/api/todos` on the **same** origin — but your API lives at `http://localhost:8080`. The browser would get a 404.

There are two ways to fix this:

1. **Use the full URL**: `fetch('http://localhost:8080/api/todos')` — but then session cookies won't be sent (cross-origin cookie restrictions), and you need CORS headers on the server.
2. **Use a proxy** (the better option): tell Vite to forward any request to `/api/*` to Express. From the browser's point of view, the request stays same-origin. Cookies work. No CORS config needed.

### Reading vite.config.js

```js
// frontend/vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {          // any request whose path starts with /api...
        target: 'http://localhost:8080',  // ...forward it to Express
        changeOrigin: true,               // rewrite the Host header
      },
    },
  },
});
```

The `proxy` block says: when the Vite dev server receives a request whose path starts with `/api`, don't serve it — forward it to `http://localhost:8080` instead.

**Q: What does this mean for your fetch calls?**

You can write `fetch('/api/todos')` everywhere in your React code. In development, Vite forwards it to Express. In production (when Express serves the built `dist/` files), the request reaches Express directly — no forwarding needed.

## Same-Origin vs. Cross-Origin

```
Your React app:   http://localhost:5173  (via Vite proxy)
External API:     https://api.example.com
```

```
fetch('/api/todos')                    ← same-origin (proxied to Express)
fetch('https://api.example.com/data')  ← cross-origin (different host)
```

When a request is **same-origin**, the browser automatically includes cookies. This is how session authentication works — the browser sends the session cookie with every API call, and the server can identify the logged-in user.

When a request is **cross-origin**, the browser blocks cookies unless the server explicitly opts in with CORS headers. That's why we use the proxy instead of hardcoding `http://localhost:8080` in our fetch calls.

**In production**, your Express server serves both the frontend (`dist/`) and the API (`/api/*`) from the same port. Everything is same-origin automatically — no proxy needed.

{% hint style="info" %}
**Why refetch instead of just updating local state?** The server is the source of truth. After a POST, the server assigns an `id`, may set timestamps, and applies defaults. Refetching gets you the real, server-confirmed record rather than a locally-constructed guess. One extra round-trip per mutation is a worthwhile trade for correctness.
{% endhint %}

## Loading State

Right now, when the app first loads, there is an instant where the todo list is empty while the fetch is in flight. Let's show a loading indicator instead.

Add an `isLoading` state variable:

```jsx
function App() {
  const [todos, setTodos] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  const loadTodos = async () => {
    setIsLoading(true);
    const data = await fetchAllTodos();
    setTodos(data);
    setIsLoading(false);
  };

  useEffect(() => {
    loadTodos();
  }, []);

  if (isLoading) return <p>Loading todos...</p>;

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

The key idea: set `isLoading = true` before the fetch starts and `isLoading = false` after it completes. Use it to render a placeholder while the data is on its way.

## Error State

What if the fetch fails — the server is down, the network is spotty, or the endpoint returns a 500? Without error handling, the UI silently shows nothing. Let's make failures visible.

```jsx
function App() {
  const [todos, setTodos] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  const loadTodos = async () => {
    setIsLoading(true);
    setError(null);
    try {
      const data = await fetchAllTodos();
      setTodos(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setIsLoading(false);
    }
  };

  useEffect(() => {
    loadTodos();
  }, []);

  if (isLoading) return <p>Loading todos...</p>;
  if (error) return <p className="error">Something went wrong: {error}</p>;

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

The pattern:
1. Wrap the fetch in `try/catch`
2. On catch, call `setError(err.message)`
3. Use conditional rendering to show the error message when `error` is non-null

**Q: What is the difference between `isLoading` and `error`? Could you have both at once?**

You now have a fully-instrumented fetch flow: loading indicator → success (render data) or failure (render error). This is the foundation your project will build on.
