# 16. Sessions & Login

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-4-1-sessions-login)!
{% endhint %}

In the previous lesson, you built a registration endpoint that hashes passwords with bcrypt. A user can now create an account. But after they create it, how does your server remember them on future requests? This lesson answers that question by introducing **sessions** — the mechanism that makes login possible.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Terms](#terms)
- [The Problem: HTTP is Stateless](#the-problem-http-is-stateless)
- [The Solution: Sessions and Cookies](#the-solution-sessions-and-cookies)
  - [How Cookie-Based Sessions Work](#how-cookie-based-sessions-work)
- [Setting Up `cookie-session`](#setting-up-cookie-session)
- [Building the Login Endpoint](#building-the-login-endpoint)
  - [The Login Flow](#the-login-flow)
  - [The Login Controller](#the-login-controller)
- [The `/api/me` Auto-Login Pattern](#the-apime-auto-login-pattern)
- [Logout](#logout)
- [Putting It Together: Auth Endpoints](#putting-it-together-auth-endpoints)


## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What does it mean that HTTP is stateless? Why is that a problem for login?
2. What is a session? What is a cookie? How do they work together?
3. What does `cookie-session` do? What does `req.session` give you access to?
4. What is the login flow, step by step?
5. What is the `/api/me` pattern? Why is it useful for frontends?
6. How do you implement logout using `cookie-session`?

## Key Concepts

* **Stateless** — HTTP is stateless: every request is independent. The server does not remember previous requests from the same client.
* **Session** — a way for the server to persist information across multiple requests from the same user, typically by storing it server-side and giving the client a token to identify the session.
* **Cookie** — a small piece of data set by a server and automatically sent by the browser with every subsequent request to that domain.
* **`cookie-session`** — an npm package that stores session data directly in an encrypted, signed cookie (no server-side session store needed).
* **`req.session`** — the session object provided by `cookie-session`. You can read from and write to it inside any controller or middleware.
* **`bcrypt.compare()`** — a bcrypt function that checks whether a plaintext password matches a stored hash. Returns `true` or `false`.
* **`/api/me`** — a convention for an endpoint that returns the currently logged-in user based on the session.

## The Problem: HTTP is Stateless

HTTP is a **stateless** protocol. That means every request your server receives is completely independent — the server has no memory of previous requests.

```
Client → GET /api/posts         (request 1)
Server → [array of posts]       (response 1)

Client → GET /api/me            (request 2)
Server → ??? Who is this person? I've never seen them before.
```

This creates a fundamental problem for applications that require login. After a user logs in, how does the server know who they are on the next request?

<details>

<summary><strong>Q: Why doesn't the server just save the user ID in a JavaScript variable after login?</strong></summary>

A JavaScript variable in your server code is shared across *all* users. If you did `let currentUser = user` after login, you'd overwrite the previous user's session every time anyone logged in. There's no way to associate a server-side variable with a specific browser connection — HTTP doesn't maintain persistent connections between requests.

</details>

## The Solution: Sessions and Cookies

The solution is **sessions**: the server stores data about the logged-in user and gives the client a token (in a **cookie**) to identify themselves on future requests.

### How Cookie-Based Sessions Work

```
1. User submits login form
   Client → POST /api/login { username, password }

2. Server verifies credentials
   Server: "Yes, that's bob. His user ID is 7."

3. Server creates a session and sets a cookie
   Server → Response with Set-Cookie: session=<encrypted data>
   (The encrypted data contains { userId: 7 })

4. Browser stores the cookie automatically

5. User navigates to another page
   Client → GET /api/profile
             Cookie: session=<encrypted data>   ← sent automatically!

6. Server reads the cookie, decrypts the session
   Server: "The cookie says userId is 7. That's bob."
   Server → bob's profile data
```

The cookie is sent automatically by the browser with every subsequent request to the same domain — no extra frontend code needed. The server reads the cookie, decrypts it, and knows who the user is.

<details>

<summary><strong>Q: What's the difference between a session stored in a cookie and a session stored in a database?</strong></summary>

* **Cookie-based sessions** (what `cookie-session` does): session data is encrypted and stored in the cookie itself. No server-side database needed. Simpler to set up, but there's a size limit on how much data you can store, and invalidating sessions (e.g., forcing a logout) requires extra work since the server doesn't track them.

* **Database-backed sessions** (what `express-session` with a store does): the server stores session data in a database (or Redis) and only puts a session ID in the cookie. More flexible (no size limit, easy to invalidate), but requires additional infrastructure.

For learning and small apps, `cookie-session` is sufficient. Production applications often use database-backed sessions.

</details>

## Setting Up `cookie-session`

Install the package:

```sh
npm install cookie-session
```

Add it to your Express app as middleware, before your routes:

```js
const cookieSession = require('cookie-session');

app.use(cookieSession({
  name: 'session',
  secret: process.env.SESSION_SECRET, // from your .env file
  maxAge: 24 * 60 * 60 * 1000, // 24 hours in milliseconds
}));
```

Add `SESSION_SECRET` to your `.env` file:

```
SESSION_SECRET=some-long-random-secret-string
```

{% hint style="warning" %}
The `secret` is used to sign the cookie, which prevents tampering. It must be kept private — never commit it to GitHub. Use a long, random string in production.
{% endhint %}

Once this middleware is in place, `req.session` is available in every controller. You can read from it and write to it:

```js
// Writing to the session
req.session.userId = 7;

// Reading from the session
const userId = req.session.userId; // 7

// Clearing the session (logout)
req.session = null;
```

## Building the Login Endpoint

### The Login Flow

Login requires three steps:

1. Find the user by username (or email) in the database
2. Compare the submitted password against the stored hash with `bcrypt.compare()`
3. If they match, write the user's ID to the session and return the user

```
POST /api/login { username: 'alice', password: 'mypassword' }
  ↓
Find user by username in DB
  ↓ (not found)     ↓ (found)
401 Unauthorized   bcrypt.compare(password, user.password_hash)
                     ↓ (no match)     ↓ (match)
                   401 Unauthorized  req.session.userId = user.id
                                       ↓
                                     200 OK { id, username }
```

### The Login Controller

```js
// controllers/authControllers.js
const bcrypt = require('bcrypt');
const User = require('../models/User');

const login = async (req, res, next) => {
  try {
    const { username, password } = req.body;

    // Step 1: find the user
    const user = await User.findByUsername(username);
    if (!user) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    // Step 2: verify the password
    const passwordMatch = await bcrypt.compare(password, user.password_hash);
    if (!passwordMatch) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    // Step 3: set the session
    req.session.userId = user.id;

    // Send back the user (without the password hash)
    res.json({ id: user.id, username: user.username });
  } catch (err) {
    next(err);
  }
};
```

{% hint style="info" %}
Notice that both "user not found" and "wrong password" return the same `401 Invalid credentials` response. This is intentional — telling an attacker whether a username exists gives them information. Always use the same generic message for both cases.
{% endhint %}

You'll need a model method to find a user by username:

```js
// models/User.js
static async findByUsername(username) {
  const result = await pool.query(
    'SELECT * FROM users WHERE username = $1',
    [username]
  );
  return result.rows[0] || null;
}
```

And the route:

```js
// routers/authRouter.js
const express = require('express');
const { login } = require('../controllers/authControllers');

const router = express.Router();
router.post('/login', login);

module.exports = router;
```

```js
// index.js
const authRouter = require('./routers/authRouter');
app.use('/api', authRouter);
```

## The `/api/me` Auto-Login Pattern

When a user returns to your app after previously logging in, their cookie is still there. The frontend needs a way to ask "Am I still logged in? Who am I?"

The `/api/me` endpoint handles this:

```js
const getMe = async (req, res, next) => {
  try {
    const { userId } = req.session;

    // No session — user is not logged in
    if (!userId) return res.status(401).json(null);

    // Session exists — look up and return the user
    const user = await User.find(userId);
    res.json({ id: user.id, username: user.username });
  } catch (err) {
    next(err);
  }
};
```

```js
router.get('/me', getMe);
```

The frontend calls `GET /api/me` when the app loads. If the response is a user object, the app knows the user is logged in. If it's a `401`, the app shows the login screen.

```js
// frontend/app.js
const checkLoginStatus = async () => {
  const response = await fetch('/api/me');
  if (response.ok) {
    const user = await response.json();
    renderLoggedInView(user);
  } else {
    renderLoginForm();
  }
};

checkLoginStatus();
```

<details>

<summary><strong>Q: Why is <code>/api/me</code> useful even after the user just logged in?</strong></summary>

When a user logs in on a login page, your app often redirects them to a dashboard or home page. That new page loads fresh — it doesn't "remember" that the login just happened. By calling `/api/me` on every page load, the app can always determine the current user state from the session, regardless of how the user got to the page.

</details>

## Logout

Logout is simple — clear the session:

```js
const logout = (req, res) => {
  req.session = null; // clears the cookie
  res.json({ message: 'Logged out' });
};

router.delete('/logout', logout);
```

Setting `req.session = null` tells `cookie-session` to delete the cookie from the browser. On the next request, `req.session.userId` will be `undefined`.

<details>

<summary><strong>Q: A user logs out, but if they copy their session cookie from before logout and manually paste it back into their browser, can they log back in?</strong></summary>

With `cookie-session` (client-side sessions), yes — in theory. `cookie-session` doesn't maintain a server-side session store, so it can't invalidate old cookies. Setting `req.session = null` clears the cookie from the browser, but if someone saved the old cookie value, they could manually restore it.

This is a known limitation of client-side sessions. Mitigations include:
* Short `maxAge` values (sessions that expire quickly)
* Changing the `secret` to invalidate all existing cookies at once
* Using server-side sessions (e.g., `express-session` with a database store) where you can explicitly delete sessions

For learning purposes and most small apps, `cookie-session` is fine. For production applications handling sensitive data, server-side sessions are more appropriate.

</details>

## Putting It Together: Auth Endpoints

Here's a summary of the four auth endpoints:

| Method   | Endpoint        | What it does                                   |
| -------- | --------------- | ---------------------------------------------- |
| `POST`   | `/api/register` | Create a new user (hash password, store in DB) |
| `POST`   | `/api/login`    | Verify credentials, set session cookie         |
| `GET`    | `/api/me`       | Return current user from session (or 401)      |
| `DELETE` | `/api/logout`   | Clear the session cookie                       |

With these four endpoints, your application has a complete authentication system. The next lesson adds **authorization** — checking whether a logged-in user has permission to access specific resources.

<details>

<summary><strong>Q: A user visits your app for the first time. Walk through exactly which auth endpoints get called and when.</strong></summary>

1. **App loads** → frontend calls `GET /api/me`
   - No session cookie exists yet
   - Server returns `401` and `null`
   - Frontend shows the login/register form

2. **User registers** → frontend calls `POST /api/register` with username and password
   - Server hashes the password, creates the user in the DB
   - Server sets `req.session.userId` and returns the new user
   - Frontend shows the logged-in view

3. **User returns the next day** → browser automatically sends the session cookie
   - Frontend calls `GET /api/me`
   - Server reads `req.session.userId`, looks up the user in the DB
   - Returns the user object
   - Frontend shows the logged-in view without requiring a new login

4. **User logs out** → frontend calls `DELETE /api/logout`
   - Server sets `req.session = null`
   - Cookie is cleared from browser
   - Frontend shows the login form again

</details>
