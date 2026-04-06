# 7. Connecting to Postgres with `pg`

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-7-connecting-to-postgres)!
{% endhint %}

Over the past two weeks you've written SQL queries directly in `psql` and TablePlus. Now it's time to run those same queries from JavaScript.

Your Express server stores data in memory — JavaScript arrays that live only as long as the process is running. Restart the server and everything is gone. In this lesson, we connect a Node application to a real Postgres database using the `pg` library so that data persists.

The key insight: **you already know the queries**. `SELECT * FROM users WHERE user_id = $1` is the same SQL you've been writing all week. The only new thing is how you send it — from JavaScript instead of a terminal.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Why `pg`?](#why-pg)
- [Setting Up the Connection](#setting-up-the-connection)
  - [Installing `pg` and `dotenv`](#installing-pg-and-dotenv)
  - [Connection Pools](#connection-pools)
  - [The `pool.js` Module](#the-pooljs-module)
- [Running Queries with `pool.query()`](#running-queries-with-poolquery)
  - [Reading the Result Object](#reading-the-result-object)
- [Parameterized Queries](#parameterized-queries)
  - [Why Never Interpolate User Input Directly](#why-never-interpolate-user-input-directly)
  - [The Safe Way: `$1` Placeholders](#the-safe-way-1-placeholders)
- [CRUD Operations with `pg`](#crud-operations-with-pg)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What does the `pg` library do? Why can't you just write SQL directly in a JavaScript file?
2. What is a connection pool? Why is it better than opening a new connection for every query?
3. What is a parameterized query? Why must you never interpolate user input directly into a SQL string?
4. What does `pool.query()` return? How do you get the rows out of the result?
5. How do you write a `SELECT`, `INSERT`, `UPDATE`, and `DELETE` query using `pg`?

## Key Concepts

* **`pg`** — the official Node.js client library for PostgreSQL. It lets JavaScript code communicate with a Postgres database.
* **Connection pool** — a cache of database connections kept open and reused across queries, rather than opening and closing a connection for each one.
* **`pool.query()`** — the primary method for sending a SQL query to the database. Returns a Promise that resolves to a result object.
* **Result object** — the object returned by `pool.query()`. Its `.rows` property contains an array of matching records as plain JavaScript objects.
* **Parameterized query** — a query where user-supplied values are passed separately from the SQL string using `$1`, `$2`, etc. placeholders, preventing SQL injection.
* **SQL injection** — a security attack where malicious SQL is embedded in user input and executed by the database. Parameterized queries prevent this.
* **`.env` file** — a file that stores environment variables (like database credentials) outside of source code, so they're never committed to version control.

## Why `pg`?

Your Postgres database is a separate process running on your computer (or a remote server). Your Node application can't just "call" it like a function — they're in different memory spaces, potentially on different machines.

The `pg` library acts as the bridge. It:

1. Opens a network connection from your Node process to the Postgres process
2. Sends your SQL strings over that connection
3. Receives the results and converts them into JavaScript objects you can work with

Without `pg`, there is no way to speak PostgreSQL from JavaScript.

## Setting Up the Connection

### Installing `pg` and `dotenv`

```sh
npm install pg dotenv
```

`pg` is the Postgres client. `dotenv` lets you store database credentials in a `.env` file and load them into `process.env` rather than hardcoding them in your source code.

### Connection Pools

When your application needs to query the database, it needs an open **connection** — a dedicated channel to communicate with Postgres. Opening a new connection takes time and resources. If your server opened and closed a connection for every HTTP request, it would be noticeably slow.

A **connection pool** solves this by keeping a set of connections open and ready. When a query comes in, the pool lends an available connection. When the query finishes, the connection returns to the pool.

```
HTTP Request → pool.query() → [connection pool] → Postgres
                                ┌──────────┐
                                │ conn 1   │ ← in use
                                │ conn 2   │ ← available
                                │ conn 3   │ ← available
                                └──────────┘
```

### The `pool.js` Module

Create `db/pool.js` — a single module that sets up the pool once and exports it for the rest of the application to share:

```js
// db/pool.js
require('dotenv').config();
const { Pool } = require('pg');

// In production (Render, Railway, etc.) set PG_CONNECTION_STRING.
// In local development, set the individual variables instead.
const pool = process.env.PG_CONNECTION_STRING
  ? new Pool({ connectionString: process.env.PG_CONNECTION_STRING })
  : new Pool({
      host:     process.env.PG_HOST     || 'localhost',
      port:     process.env.PG_PORT     || 5432,
      user:     process.env.PG_USER,
      password: process.env.PG_PASSWORD,
      database: process.env.PG_DATABASE,
    });

module.exports = pool;
```

Create a `.env` file in your project root:

```
# Local development — fill in your values
PG_HOST=localhost
PG_PORT=5432
PG_USER=
PG_PASSWORD=
PG_DATABASE=my_app_db

# Production — set this instead of the individual vars above
# PG_CONNECTION_STRING=postgres://user:password@host:5432/database
```

{% hint style="warning" %}
Never commit your `.env` file to GitHub. Add it to `.gitignore`:

```
node_modules
.env
```
{% endhint %}

**<details><summary>Q: Why do we export the pool from a separate `db/pool.js` file rather than creating it inside each model file?</summary>**

The pool should be a singleton — one shared instance for the entire application. If you created a new `Pool` inside every model file, you'd end up with multiple pools competing for connections and consuming more resources than necessary. Exporting from `db/pool.js` ensures the pool is created once and all models share the same instance.

</details>

**<details><summary>Q: Why does `pool.js` check for `PG_CONNECTION_STRING` first, before the individual variables?</summary>**

Deployment platforms like Render and Railway automatically inject a `DATABASE_URL` or similar connection string environment variable when you add a Postgres database to your project. Using the connection string in production is simpler — one variable instead of five — and it's the format these platforms expect. Individual variables (`PG_HOST`, `PG_DATABASE`, etc.) are more readable during local development where you're configuring things manually. The `pool.js` file handles both cases so neither environment requires changes to the code.

</details>

## Running Queries with `pool.query()`

To run a SQL query, call `pool.query()` with your SQL string. It returns a **Promise**:

```js
const pool = require('../db/pool');

const getAllUsers = async () => {
  const result = await pool.query('SELECT * FROM users ORDER BY user_id');
  return result.rows; // an array of user objects
};
```

### Reading the Result Object

`pool.query()` resolves to a result object. The property you'll use most is `.rows`:

```js
const result = await pool.query('SELECT * FROM users');

console.log(result.rows);
// [
//   { user_id: 1, username: 'ann_duong', email: 'ann@example.com' },
//   { user_id: 2, username: 'reuben_o',  email: 'reuben@example.com' }
// ]

console.log(result.rowCount); // 2 — number of rows returned or affected
```

Each element of `result.rows` is a plain JavaScript object. Column names become property keys, exactly as named in the database.

**<details><summary>Q: You run `SELECT * FROM users WHERE user_id = $1` with an ID that doesn't exist. What does `result.rows` look like, and how should your model handle it?</summary>**

`result.rows` will be an empty array `[]`. `pool.query()` does not throw an error when a `SELECT` returns no rows — it just returns zero rows. Your model should handle this explicitly:

```js
const findUser = async (user_id) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE user_id = $1',
    [user_id]
  );
  return result.rows[0] || null; // first row, or null if not found
};
```

Returning `null` makes the "not found" case clear to the controller, which can then send a `404` response.

</details>

## Parameterized Queries

When your queries include values from user input — an ID from the URL, a username from a form — you must use **parameterized queries**.

### Why Never Interpolate User Input Directly

```js
// NEVER DO THIS
const findUser = async (user_id) => {
  const result = await pool.query(`SELECT * FROM users WHERE user_id = ${user_id}`);
  return result.rows[0];
};
```

If `user_id` comes from user input, a malicious user could send:

```
1; DROP TABLE users; --
```

Your query would become:

```sql
SELECT * FROM users WHERE user_id = 1; DROP TABLE users; --
```

Postgres would execute both statements and your `users` table would be gone. This is **SQL injection** — one of the most common and destructive web vulnerabilities.

### The Safe Way: `$1` Placeholders

Parameterized queries separate the SQL structure from the values:

```js
const findUser = async (user_id) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE user_id = $1',
    [user_id]  // values passed in a separate array
  );
  return result.rows[0] || null;
};
```

- `$1` is a placeholder for the first value in the array
- `$2` would be the second, `$3` the third, and so on
- `pg` sends the SQL and values to Postgres separately
- Postgres treats the values as *data only* — never as SQL commands

Even if a user passes `1; DROP TABLE users; --` as `user_id`, Postgres treats the entire string as a value being compared to the `user_id` column. It finds no match and returns no rows. The table is safe.

**<details><summary>Q: A query needs to filter by both `username` and `email`. How do you write it with parameterized placeholders?</summary>**

```js
const findByCredentials = async (username, email) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE username = $1 AND email = $2',
    [username, email]
  );
  return result.rows[0] || null;
};
```

`$1` maps to `username` (index 0 of the array) and `$2` maps to `email` (index 1). The order of values in the array must match the order of the placeholders in the SQL string.

</details>

## CRUD Operations with `pg`

Here is the full set of CRUD operations using `pg`. These live in a model file — the layer that owns all database communication:

```js
// models/userModel.js
const pool = require('../db/pool');

class User {
  // READ all
  static async list() {
    const result = await pool.query('SELECT * FROM users ORDER BY user_id');
    return result.rows;
  }

  // READ one
  static async find(user_id) {
    const result = await pool.query(
      'SELECT * FROM users WHERE user_id = $1',
      [user_id]
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
  static async update(user_id, username, email) {
    const result = await pool.query(
      `UPDATE users
       SET username = $1, email = $2
       WHERE user_id = $3
       RETURNING *`,
      [username, email, user_id]
    );
    return result.rows[0] || null;
  }

  // DELETE
  static async destroy(user_id) {
    const result = await pool.query(
      'DELETE FROM users WHERE user_id = $1 RETURNING *',
      [user_id]
    );
    return result.rows[0] || null;
  }
}

module.exports = User;
```

{% hint style="info" %}
`RETURNING *` is a Postgres extension that makes `INSERT`, `UPDATE`, and `DELETE` return the affected row(s) as if they were a `SELECT`. Without it, those operations return only `rowCount`. Always use `RETURNING *` in your model methods so you can return the created, updated, or deleted record to the controller.
{% endhint %}

**<details><summary>Q: What is the difference between `result.rows[0]` and `result.rows`? When do you use each?</summary>**

- `result.rows` — the full array of matching rows. Use when you expect multiple results (e.g., `list()` — all users).
- `result.rows[0]` — the first row only. Use when you expect exactly one result (e.g., `find(user_id)`, `create()`, `update()`).

If you use `result.rows[0]` and no row exists, you get `undefined`. That's why methods like `find` and `update` return `result.rows[0] || null` — to make the "not found" case explicit for the controller.

</details>

**<details><summary>Q: Your `User.create()` method returns the newly created user. The controller should send a `201 Created` response with that user as JSON. What should the controller look like?</summary>**

```js
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

The `try/catch` is essential — database operations can fail (duplicate username with a `UNIQUE` constraint, network issue, invalid data). The `catch` passes the error to Express error-handling middleware via `next(err)` rather than crashing the server.

</details>
