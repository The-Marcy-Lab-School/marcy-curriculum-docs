# Cheat Sheet — Databases

- [SQL + Postgres](#sql--postgres)
  - [SQL + Postgres Basics](#sql--postgres-basics)
  - [One-To-Many Table](#one-to-many-table)
  - [Many To Many Table](#many-to-many-table)
- [`pg`](#pg)
  - [`pg` Basics](#pg-basics)
  - [Setting Up `pool.js`](#setting-up-pooljs)
  - [Running Queries with `pool.query()`](#running-queries-with-poolquery)
  - [Parameterized Queries](#parameterized-queries)
  - [CRUD Operations with `pg`](#crud-operations-with-pg)
  - [Async Model Methods](#async-model-methods)
  - [Async Controllers & Error Handling Middleware](#async-controllers--error-handling-middleware)
- [Authentication and Authorization](#authentication-and-authorization)
  - [Authentication and Authorization Basics](#authentication-and-authorization-basics)
  - [Bcrypt](#bcrypt)
  - [Sessions with `cookie-session`](#sessions-with-cookie-session)
  - [Auth Endpoints](#auth-endpoints)
  - [Authorization Middleware](#authorization-middleware)
  - [Ownership-Based Authorization](#ownership-based-authorization)

![](./img/cheatsheet/client-server-database.svg)

## SQL + Postgres

### SQL + Postgres Basics

* **Fullstack** - refers to the combination of frontend (client-side) and backend (server-side) technologies.
* **PERN** - an acronym for a specific set of technologies used to build a fullstack web application: Postgres, Express, React, and Node. This acronym is useful when asked "what stack do you use?"
* **Database** - a structured collection of data that is organized in a manner for easy retrieval.
* **Database Management System (DBMS)** - a piece of software used to create and maintain a database.
* **Postgres** - a popular "relational" database management system that stores data in a table-like manner
* **SQL (Structured Query Language)** - a language used by relational database management systems to create, read, update, or delete data from a database.

![](./img/cheatsheet/labeled-erd.png)

### One-To-Many Table

* **Schema Design** - the process of planning the structure and relationships of the tables in a database.
* **Primary Key** - a column in a table that uniquely identifies each row in the table.
* **Foreign Key** - a column in a table that references the primary key of another table.
* **One-to-Many** - a relationship between two tables in which instances in one table can be referenced by many instances in another table.
* `JOIN` - A SQL statement that combines the columns of two tables.

```sql
CREATE TABLE people (
	id SERIAL PRIMARY KEY, -- primary key
	name TEXT NOT NULL
);
CREATE TABLE pets (
	id SERIAL PRIMARY KEY, -- primary key
	name TEXT NOT NULL,
	type TEXT NOT NULL,
	owner_id INTEGER REFERENCES people --foreign key
);
```

**`people` Table:**
| id  | name           |
| --- | -------------- |
| 1   | Ann Duong      |
| 2   | Reuben Ogbonna |
| 3   | Carmen Salas   |
| 4   | Ben Spector    |

**`pets` Table:**
| id  | name       | type | owner_id |
| --- | ---------- | ---- | -------- |
| 1   | Khalo      | dog  | 3        |
| 2   | Juan Pablo | dog  | 2        |
| 3   | Bora       | bird | 1        |
| 4   | Frida      | cat  | 3        |
| 5   | Tora       | dog  | 1        |
| 6   | Pon Juablo | cat  | 2        |
| 7   | Kora       | dog  | 1        |

What are the names and ids of all the pets owned by Ann?

```sql
SELECT pets.id, pets.name
FROM pets
JOIN people ON pets.owner_id = people.id
WHERE people.name = 'Ann Duong';
```

* The order that you select `FROM` and `JOIN` does not matter
* The `ON` clause indicates the relationship between the two tables (the `pets.owner_id` column references the `people.id` column)
* The `WHERE` clause filters down the result.
* When dealing with multiple tables, *always* specify the table that a value comes from.

### Many To Many Table

* **Entity Relationship Diagram** - a diagram that illustrates the relationships between tables.
* **Many-to-Many** - a relationship between two tables in which the instances of each table can be referenced by many instances in the other table.
* **Association/Junction Table** - a table used to create a many-to-many relationship using two foreign keys to reference two tables.

![](./img/cheatsheet/labeled-erd.png)

> *created using https://dbdiagram.io/*

Q: Give me the names and ids of the customers that ordered product #2

```sql
SELECT customers.name, customers.id
FROM customers
JOIN orders ON customers.id=orders.customer_id
JOIN products ON products.id=orders.product_id
WHERE products.id = 2;
```

## `pg`

![](./img/cheatsheet/client-server-database.svg)

### `pg` Basics

* **`pg`** — the official Node.js client library for PostgreSQL. It lets JavaScript code send SQL queries to and receive results from a Postgres database.
* **Connection Pool** — a cache of database connections reused across queries, rather than opening and closing a new connection for each query.
* **`pool.query()`** — the primary method for sending a SQL query to the database. Returns a Promise that resolves to a result object.
* **Result Object** — the object resolved by `pool.query()`. Its `.rows` property is an array of matching records, each as a plain JavaScript object.
* **Parameterized Query** — a query where user-supplied values are passed separately from the SQL string using `$1`, `$2`, etc. placeholders, preventing SQL injection.
* **SQL Injection** — a security attack where malicious SQL is embedded in user input and executed by the database. Parameterized queries prevent this.
* **`.env` file** — a file that stores environment variables (like database credentials) that should not be committed to version control.

### Setting Up `pool.js`

0. Install `pg` and `dotenv`:

    ```sh
    npm install pg dotenv
    ```

1. Create a `.env` file in your project root:

    ```
    PG_CONNECTION_STRING=postgres://localhost/your_database_name
    ```

    > Never commit `.env` to GitHub. Add it to `.gitignore`.

2. Create `db/pool.js` that sets up the pool once and exports it:

    ```js
    // db/pool.js
    require('dotenv').config();
    const { Pool } = require('pg');

    const pool = new Pool({ connectionString: process.env.PG_CONNECTION_STRING });

    module.exports = pool;
    ```

### Running Queries with `pool.query()`

```js
const pool = require('../db/pool');

const getAllUsers = async () => {
  const result = await pool.query('SELECT * FROM users');
  return result.rows; // an array of user objects
};
```

* `pool.query()` returns a Promise — always `await` it inside an `async` function.
* `result.rows` is an array of records. Each row is a plain JavaScript object.
* `result.rowCount` is the number of rows returned or affected.
* When no row matches, `result.rows` is `[]` — not an error.

### Parameterized Queries

**Never** interpolate user input directly into a SQL string — it opens your app to SQL injection:

```js
// NEVER DO THIS
const result = await pool.query(`SELECT * FROM users WHERE id = ${id}`);
```

Use `$1`, `$2`, etc. placeholders and pass values in a separate array:

```js
const getUserById = async (id) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE id = $1',
    [id]
  );
  return result.rows[0] || null;
};
```

* `$1` maps to the first element of the values array, `$2` to the second, and so on.
* Postgres treats the values as *data only*, never as SQL commands — even if they contain SQL syntax.

### CRUD Operations with `pg`

```js
// models/User.js
const pool = require('../db/pool');

class User {
  // READ all
  static async list() {
    const result = await pool.query('SELECT * FROM users ORDER BY id');
    return result.rows;
  }

  // READ one
  static async find(id) {
    const result = await pool.query(
      'SELECT * FROM users WHERE id = $1',
      [id]
    );
    return result.rows[0] || null;
  }

  // CREATE
  static async create(username, email) {
    const result = await pool.query(
      'INSERT INTO users (username, email) VALUES ($1, $2) RETURNING *',
      [username, email]
    );
    return result.rows[0]; // the newly created row
  }

  // UPDATE
  static async update(id, username, email) {
    const result = await pool.query(
      `UPDATE users
       SET username = $1, email = $2
       WHERE id = $3
       RETURNING *`,
      [username, email, id]
    );
    return result.rows[0] || null;
  }

  // DELETE
  static async destroy(id) {
    const result = await pool.query(
      'DELETE FROM users WHERE id = $1 RETURNING *',
      [id]
    );
    return result.rows[0] || null;
  }
}

module.exports = User;
```

* `RETURNING *` makes `INSERT`, `UPDATE`, and `DELETE` return the affected row(s). Without it, `result.rows` is empty.
* Use `result.rows[0]` when you expect exactly one result (find, create, update, delete).
* Use `result.rows` when you expect multiple results (list).

### Async Model Methods

All `pg` queries are asynchronous. Every model method that calls `pool.query()` must be `async` and use `await`:

```js
// Without async/await — WRONG
static find(id) {
  const result = pool.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0]; // result is a Promise, not the actual data!
}

// With async/await — CORRECT
static async find(id) {
  const result = await pool.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0] || null;
}
```

Model methods can wrap queries in `try/catch` to provide context on failure:

```js
static async create(username, email) {
  try {
    const result = await pool.query(
      'INSERT INTO users (username, email) VALUES ($1, $2) RETURNING *',
      [username, email]
    );
    return result.rows[0];
  } catch (err) {
    throw new Error(`Failed to create user: ${err.message}`);
  }
}
```

### Async Controllers & Error Handling Middleware

Controllers that call async model methods must also be `async`. Use `try/catch` and pass errors to `next(err)`:

```js
const getUser = async (req, res, next) => {
  try {
    const user = await User.find(req.params.id);
    if (!user) return res.status(404).json({ error: 'User not found' });
    res.json(user);
  } catch (err) {
    next(err); // passes to error handling middleware
  }
};

const createUser = async (req, res, next) => {
  try {
    const { username, email } = req.body;
    const user = await User.create(username, email);
    res.status(201).json(user);
  } catch (err) {
    next(err);
  }
};
```

Add an **error handling middleware** after all routes in `index.js`. Express identifies it by its four-parameter signature:

```js
// index.js — AFTER all routes
const handleError = (err, req, res, next) => {
  console.error(err);
  const status = err.status ?? 500;
  const message = err.message ?? 'Internal Server Error';
  res.status(status).json({ error: message });
};

app.use(handleError);
```

## Authentication and Authorization

### Authentication and Authorization Basics

* **Hashing** - a mathematical algorithm that transforms a string of characters into a fixed-length string of characters.
* **Password Salt** - A salt is a random string of data that is added to the input data before the hash function is applied. This changes the hash value that is produced, even for the same input data.
* **Salt Rounds** - the number of times a password has been salted before being hashed
* **Plaintext password** - the password as it was entered by the user, before it is hashed.
* **Bcrypt** - a Node module that provides functions for hashing strings and verifying hashed strings.
* **Authentication** - confirming *who you are* — verifying identity through login. Failure returns `401 Unauthorized`.
* **Authorization** - confirming *what you're allowed to do* — checking permissions after identity is established. Failure returns `403 Forbidden`.
* **Session** - a way for the server to persist information (like a logged-in user's ID) across multiple requests from the same client.
* **Cookie** - a small piece of data set by a server and automatically sent by the browser with every subsequent request to that domain.

### Bcrypt

The `bcrypt` module makes handling hashing, salting, and verification straightforward with two key methods:
* `bcrypt.hash(password, saltRounds)`
* `bcrypt.compare(password, hashedPassword)`

We can create these helper functions to gracefully handle errors that can occur when using these asynchronous operations.
```js
const hashPassword = async (password, saltRounds = 8) => {
  try {
    const hashedPassword = await bcrypt.hash(password, saltRounds);
    return hashedPassword;
  } catch (err) {
    console.error(err.message);
    return null;
  }
};
const isValidPassword = async (password, hash) => {
  try {
    const isValid = await bcrypt.compare(password, hash);
    return isValid;
  } catch (err) {
    return console.error(err.message);
  }
};
```

And when users want to create a new account or login to their existing account, we can use these functions for authentication:

```js
const testSignUpAndLogin = async () => {
  // when a new user signs up, they will create a new hashed password
  const hashedPW = await hashPassword('foobar');
  console.log(hashedPW); // => some random fixed-length string

  // later, when they want to log in, we will need to verify their password
  console.log(await isValidPassword('flubear', hashedPW)); // => false
  console.log(await isValidPassword('foobar', hashedPW)); // => true
}
```

### Sessions with `cookie-session`

HTTP is **stateless** — the server has no memory of previous requests. Sessions solve this by storing data (like the logged-in user's ID) that persists across requests.

**`cookie-session`** stores session data directly in an encrypted, signed cookie sent to the browser.

Install and configure it before your routes:

```sh
npm install cookie-session
```

```js
// index.js
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

Once the middleware is in place, `req.session` is available in every controller:

```js
// Writing to the session (e.g., after login)
req.session.userId = user.id;

// Reading from the session
const userId = req.session.userId;

// Clearing the session (logout)
req.session = null;
```

![](./img/cheatsheet/cookies.png)

### Auth Endpoints

A complete authentication system has four endpoints:

| Method   | Endpoint        | What it does                                   |
| -------- | --------------- | ---------------------------------------------- |
| `POST`   | `/api/register` | Create a new user (hash password, store in DB) |
| `POST`   | `/api/login`    | Verify credentials, set session cookie         |
| `GET`    | `/api/me`       | Return current user from session (or 401)      |
| `DELETE` | `/api/logout`   | Clear the session cookie                       |

**Login controller** — find user, verify password, set session:

```js
const login = async (req, res, next) => {
  try {
    const { username, password } = req.body;

    const user = await User.findByUsername(username);
    if (!user) return res.status(401).json({ error: 'Invalid credentials' });

    const passwordMatch = await bcrypt.compare(password, user.password_hash);
    if (!passwordMatch) return res.status(401).json({ error: 'Invalid credentials' });

    req.session.userId = user.id;
    res.json({ id: user.id, username: user.username });
  } catch (err) {
    next(err);
  }
};
```

> Use the same generic message for both "user not found" and "wrong password" — telling an attacker which one is true gives them information.

**`/api/me` controller** — return the current user from the session:

```js
const getMe = async (req, res, next) => {
  try {
    const { userId } = req.session;
    if (!userId) return res.status(401).json(null);

    const user = await User.find(userId);
    res.json({ id: user.id, username: user.username });
  } catch (err) {
    next(err);
  }
};
```

**Logout controller** — clear the session:

```js
const logout = (req, res) => {
  req.session = null;
  res.json({ message: 'Logged out' });
};
```

### Authorization Middleware

**`checkAuthentication`** middleware protects routes that require login. It checks for a valid session and either continues the request or returns a `401`:

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

Apply it to individual routes:

```js
// Public — no middleware
router.get('/posts', getAllPosts);

// Protected — must be logged in
router.post('/posts', checkAuthentication, createPost);
router.patch('/posts/:id', checkAuthentication, updatePost);
router.delete('/posts/:id', checkAuthentication, deletePost);
```

Or apply it to an entire router with `router.use()`:

```js
const router = express.Router();

router.use(checkAuthentication); // every route on this router requires login

router.get('/', getAllPosts);
router.post('/', createPost);
router.patch('/:id', updatePost);
router.delete('/:id', deletePost);
```

### Ownership-Based Authorization

Being logged in is the first check. Some actions also require that the logged-in user *owns* the resource. Compare `req.session.userId` against the resource's owner field, returning `403 Forbidden` if they don't match:

```js
const updatePost = async (req, res, next) => {
  try {
    const post = await Post.find(req.params.id);
    if (!post) return res.status(404).json({ error: 'Post not found' });

    // Must own the post to edit it
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

* `401 Unauthorized` — "I don't know who you are. Log in."
* `403 Forbidden` — "I know who you are, but you can't do this."
