# 17. Authorization Middleware

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-4-2-authorization-middleware)!
{% endhint %}

Authentication answers "who are you?" Authorization answers "what are you allowed to do?" In this lesson, we use middleware to enforce authorization — preventing unauthenticated or unauthorized users from accessing protected endpoints.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Terms](#terms)
- [Authentication vs. Authorization](#authentication-vs-authorization)
- [The Problem: Unprotected Endpoints](#the-problem-unprotected-endpoints)
- [Writing Authorization Middleware](#writing-authorization-middleware)
  - [The `checkAuthentication` Middleware](#the-checkauthentication-middleware)
  - [Applying It to Routes](#applying-it-to-routes)
  - [Applying It to an Entire Router](#applying-it-to-an-entire-router)
- [Ownership-Based Authorization](#ownership-based-authorization)
- [Testing Protected Routes with Postman](#testing-protected-routes-with-postman)
- [The Complete Auth Picture](#the-complete-auth-picture)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is the difference between authentication and authorization?
2. What problem does authorization middleware solve? Why not just put the check inside every controller?
3. How does middleware use `next()` to either continue the request or short-circuit it?
4. How do you protect a single route vs. an entire router?
5. What is ownership-based authorization? How do you check if a user owns a resource?

## Terms

* **Authentication** — verifying *who you are* (proving your identity, typically through login).
* **Authorization** — determining *what you're allowed to do* (checking permissions after identity is established).
* **Protected Route / Endpoint** — an endpoint that requires a valid session (or additional permissions) to access.
* **`checkAuthentication` middleware** — a custom middleware function that checks for a valid session and either allows the request to continue (`next()`) or short-circuits it with a `401` response.
* **Ownership** — a resource (post, comment, profile) that belongs to a specific user. Authorization checks that the currently logged-in user owns the resource before allowing modifications.

## Authentication vs. Authorization

These two terms are related but distinct — and it's important to understand the difference:

|                       | Authentication              | Authorization                               |
| --------------------- | --------------------------- | ------------------------------------------- |
| **Question**          | Who are you?                | What are you allowed to do?                 |
| **How**               | Login (username + password) | Session check + permission check            |
| **Status on failure** | 401 Unauthorized            | 403 Forbidden                               |
| **Comes first?**      | Yes                         | After authentication                        |
| **Example**           | Logging into Instagram      | Editing *your own* post, not someone else's |

Authentication always comes first. You can't authorize someone whose identity you haven't verified.

<details>

<summary><strong>Q: A logged-in user tries to delete another user's post. Is this an authentication failure or an authorization failure?</strong></summary>

**Authorization failure.** The user is authenticated (we know who they are — they have a valid session), but they don't have *permission* to delete someone else's post. The server should return `403 Forbidden`, not `401 Unauthorized`.

* `401` — "I don't know who you are. Log in."
* `403` — "I know who you are, but you can't do this."

</details>

## The Problem: Unprotected Endpoints

Without authorization middleware, every endpoint on your server is public:

```js
// Anyone can do this — logged in or not
router.delete('/posts/:id', deletePost);
```

A user who knows the URL can delete posts even if they're not logged in, or delete someone else's posts. We need to protect endpoints that require login.

You might be tempted to handle this inside each controller:

```js
const deletePost = async (req, res, next) => {
  // Bad: checking auth inside every controller
  if (!req.session.userId) {
    return res.status(401).json({ error: 'Must be logged in' });
  }
  // ... rest of delete logic
};
```

This works, but it's repetitive. If you have 10 protected endpoints, you'd write the same check 10 times. Middleware solves this by extracting the check into a reusable function.

## Writing Authorization Middleware

### The `checkAuthentication` Middleware

Authorization middleware is just a regular Express middleware function — it receives `req`, `res`, and `next`. The key behavior:

* **If the session is valid:** call `next()` to pass control to the next middleware or controller
* **If the session is missing:** send a `401` response and stop

```js
// middleware/checkAuthentication.js
const checkAuthentication = (req, res, next) => {
  const { userId } = req.session;

  if (!userId) {
    return res.status(401).json({ error: 'You must be logged in to do that.' });
  }

  next(); // session is valid — continue to the controller
};

module.exports = checkAuthentication;
```

That's it. The middleware either stops the request by sending a response, or lets it continue by calling `next()`.

<details>

<summary><strong>Q: What happens if you forget to call <code>next()</code> in middleware when the session is valid?</strong></summary>

The request hangs. The middleware function returns without sending a response or calling `next()`, so Express doesn't know what to do next. The client's request will eventually time out. Always make sure every code path through a middleware either calls `next()`, calls `next(err)`, or sends a response.

</details>

### Applying It to Routes

Add the middleware as an argument between the path and the controller:

```js
const checkAuthentication = require('../middleware/checkAuthentication');

// Public — no middleware
router.get('/posts', getAllPosts);

// Protected — must be logged in
router.post('/posts', checkAuthentication, createPost);
router.patch('/posts/:id', checkAuthentication, updatePost);
router.delete('/posts/:id', checkAuthentication, deletePost);
```

When a request hits `DELETE /posts/5`:
1. Express calls `checkAuthentication`
2. If session is missing → `401`, stops here
3. If session is valid → calls `next()`, Express calls `deletePost`

The controller never even runs if the user isn't logged in.

<details>

<summary><strong>Q: You have a blog app where anyone can read posts, but only logged-in users can create, edit, or delete. Which routes get the middleware and which don't?</strong></summary>

```js
// Public
router.get('/posts', getAllPosts);           // anyone can read
router.get('/posts/:id', getPost);          // anyone can read one post

// Protected
router.post('/posts', checkAuthentication, createPost);
router.patch('/posts/:id', checkAuthentication, updatePost);
router.delete('/posts/:id', checkAuthentication, deletePost);
```

Only the write operations (create, update, delete) require authentication. Read-only endpoints remain public.

</details>

### Applying It to an Entire Router

If every endpoint on a router requires authentication, use `router.use()` to apply the middleware to all routes at once:

```js
// routers/postsRouter.js

const router = express.Router();

// Every route on this router requires authentication
router.use(checkAuthentication);

router.get('/', getAllPosts);         // still protected
router.post('/', createPost);        // protected
router.patch('/:id', updatePost);    // protected
router.delete('/:id', deletePost);   // protected

module.exports = router;
```

You can also do this when mounting the router in `index.js`:

```js
// index.js
app.use('/api/admin', checkAuthentication, adminRouter);
```

Every route under `/api/admin` requires a valid session. Routes under other prefixes are unaffected.

## Ownership-Based Authorization

Being logged in is the first check. But some actions require that you own the resource. Anyone logged in shouldn't be able to edit anyone else's post.

Ownership authorization looks like this:

```js
const updatePost = async (req, res, next) => {
  try {
    const post = await Post.find(req.params.id);
    if (!post) return res.status(404).json({ error: 'Post not found' });

    // Check ownership — logged-in user must own this post
    if (post.user_id !== req.session.userId) {
      return res.status(403).json({ error: 'You do not have permission to edit this post.' });
    }

    const updated = await Post.update(req.params.id, req.body);
    res.json(updated);
  } catch (err) {
    next(err);
  }
};
```

1. `checkAuthentication` middleware already confirmed `req.session.userId` exists
2. The controller fetches the post and compares `post.user_id` to `req.session.userId`
3. If they don't match → `403 Forbidden`
4. If they match → proceed with the update

You could also extract ownership checking into its own middleware, but keeping it in the controller is common when the check requires a database lookup.

<details>

<summary><strong>Q: Why does ownership authorization return <code>403</code> instead of <code>401</code>?</strong></summary>

Because the user *is* authenticated — we know who they are. The `401` ("Unauthorized") response specifically means "I don't know who you are, please authenticate." Since we do know who they are, and we're denying them based on permissions, the correct code is `403 Forbidden`: "I know who you are, but you're not allowed to do this."

</details>

<details>

<summary><strong>Q: Could you write a reusable middleware for ownership checking? What would it look like?</strong></summary>

You could, but it's tricky because ownership middleware needs to know which model to look up and which field represents the owner. A common pattern is a factory function that generates middleware:

```js
const checkOwnership = (Model) => async (req, res, next) => {
  try {
    const resource = await Model.find(req.params.id);
    if (!resource) return res.status(404).json({ error: 'Not found' });
    if (resource.user_id !== req.session.userId) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    req.resource = resource; // make it available to the controller
    next();
  } catch (err) {
    next(err);
  }
};

// Usage
router.patch('/:id', checkAuthentication, checkOwnership(Post), updatePost);
```

This approach is clean for large applications. For a smaller app, handling ownership inline in the controller is simpler and perfectly fine.

</details>

## Testing Protected Routes with Postman

When testing protected routes in Postman, you need an active session cookie. The workflow:

1. **Register or login** — send a `POST /api/login` request with valid credentials
2. **Postman stores the cookie automatically** — look in the "Cookies" tab to confirm
3. **Make requests to protected endpoints** — Postman sends the cookie automatically, just like a browser would
4. **Test the unauthorized case** — clear the cookies in Postman and try the protected endpoint again. You should get a `401`.

This mirrors exactly how the browser and your frontend would behave.

## The Complete Auth Picture

Here's the full authentication and authorization system we've built:

```
POST /api/register   → hash password, create user, set session
POST /api/login      → verify password, set session
GET  /api/me         → return current user from session
DELETE /api/logout   → clear session

GET  /api/posts      → public (no middleware)
POST /api/posts      → checkAuthentication → createPost controller
PATCH /api/posts/:id → checkAuthentication → updatePost (+ ownership check inside)
DELETE /api/posts/:id → checkAuthentication → deletePost (+ ownership check inside)
```

The flow for a protected request:

```
Browser sends GET /api/posts/5/edit
  ↓
cookie-session reads session cookie → req.session.userId = 12
  ↓
checkAuthentication middleware
  → session.userId exists? Yes → next()
  ↓
getPost controller
  → Post.find(5) → { id: 5, user_id: 12, ... }
  → post.user_id === req.session.userId? Yes
  → res.json(post)
```

And for an unauthorized request:

```
Browser (not logged in) sends DELETE /api/posts/5
  ↓
cookie-session reads session cookie → no cookie → req.session = {}
  ↓
checkAuthentication middleware
  → session.userId exists? No
  → res.status(401).json({ error: 'Must be logged in' })
  → Request stops here. Controller never runs.
```

<details>

<summary><strong>Q: You're building a notes app. Every user can only see and edit their own notes — no note is public. Design the middleware strategy for the <code>/api/notes</code> router.</strong></summary>

Since no route on this router is public, apply `checkAuthentication` to the entire router:

```js
// routers/notesRouter.js
const router = express.Router();

router.use(checkAuthentication); // all routes require login

router.get('/', getUserNotes);        // returns only notes belonging to req.session.userId
router.post('/', createNote);         // associates new note with req.session.userId
router.get('/:id', getNote);          // + ownership check in controller
router.patch('/:id', updateNote);     // + ownership check in controller
router.delete('/:id', deleteNote);    // + ownership check in controller
```

In `getUserNotes`, you'd filter by `user_id`:

```js
const getUserNotes = async (req, res, next) => {
  try {
    const notes = await Note.listByUser(req.session.userId);
    res.json(notes);
  } catch (err) {
    next(err);
  }
};
```

Model method:

```js
static async listByUser(userId) {
  const result = await pool.query(
    'SELECT * FROM notes WHERE user_id = $1 ORDER BY created_at DESC',
    [userId]
  );
  return result.rows;
}
```

This design ensures users can only ever see and modify their own data.

</details>
