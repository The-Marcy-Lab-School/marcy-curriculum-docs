# 11. Connecting to Postgres with `pg`

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-2-1-connecting-to-postgres-pg)!
{% endhint %}

So far, your Express server stores data in memory — JavaScript arrays and objects that live only as long as the process is running. Restart the server and everything is gone. In this lesson, we connect your Node application to a real Postgres database using the `pg` library so that data persists.

We'll run the same SQL queries you wrote last week, but now from JavaScript.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Terms](#terms)
- [Why `pg`?](#why-pg)
- [Setting Up the Connection](#setting-up-the-connection)
  - [Installing `pg`](#installing-pg)
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
* **Connection Pool** — a cache of database connections that are reused across multiple queries, rather than opening and closing a new connection for each query.
* **`pool.query()`** — the primary method for sending a SQL query to the database. Returns a Promise that resolves to a result object.
* **Result Object** — the object returned by `pool.query()`. Its `.rows` property contains an array of matching records, each as a plain JavaScript object.
* **Parameterized Query** — a query where user-supplied values are passed separately from the SQL string using `$1`, `$2`, etc. placeholders, preventing SQL injection.
* **SQL Injection** — a security attack where malicious SQL is embedded in user input and executed by the database. Parameterized queries prevent this.
* **`.env` file** — a file that stores environment variables (like database credentials) that should not be committed to version control.

## Why `pg`?

Your Postgres database is a separate process running on your computer (or a remote server). Your Node application can't just "call" it like a function — they're in different memory spaces, potentially on different machines.

The `pg` library acts as the bridge. It:

1. Opens a network connection from your Node process to the Postgres process
2. Sends your SQL queries over that connection
3. Receives the results and converts them into JavaScript objects you can work with

Without `pg`, you'd have no way to speak PostgreSQL from JavaScript.

## Setting Up the Connection

### Installing `pg`

```sh
npm install pg
```

You'll also want `dotenv` so you can store database credentials in a `.env` file:

```sh
npm install dotenv
```

### Connection Pools

When your application needs to query the database, it needs a **connection** — an open channel to communicate with Postgres. Opening a new connection takes time. If your server opens and closes a connection for every incoming HTTP request, it will be slow and use excessive resources.

A **connection pool** solves this by keeping a set of connections open and ready. When a query comes in, the pool lends out an available connection. When the query finishes, the connection goes back to the pool for the next query.

```
HTTP Request → pool.query() → [connection pool] → Postgres
                                ┌──────────┐
                                │ conn 1   │ ← in use
                                │ conn 2   │ ← available
                                │ conn 3   │ ← available
                                └──────────┘
```

### The `pool.js` Module

Create a file `db/pool.js` that sets up the pool once and exports it:

```js
// db/pool.js
require('dotenv').config();
const { Pool } = require('pg');

// process.env.PG_CONNECTION_STRING comes from your .env file
const pool = new Pool({ connectionString: process.env.PG_CONNECTION_STRING });

module.exports = pool;
```

Create a `.env` file in your project root:

```
PG_CONNECTION_STRING=postgres://localhost/your_database_name
```

{% hint style="warning" %}
Never commit your `.env` file to GitHub. Make sure it's listed in your `.gitignore`:

```
node_modules
.env
```
{% endhint %}

The **connection string** format is:

```
postgres://[user]:[password]@[host]:[port]/[database_name]
```

For local development without a password, it's often as simple as:

```
postgres://localhost/my_app_db
```

<details>

<summary><strong>Q: Why do we export the pool from a separate file rather than creating it inside every model file?</strong></summary>

The pool should be a singleton — one shared pool for the entire application. If you created a new `Pool` in every model file, you'd end up with multiple pools competing for connections and consuming more resources than necessary. Exporting from `db/pool.js` ensures the pool is created once and every model shares the same instance.

</details>

## Running Queries with `pool.query()`

To run a SQL query, call `pool.query()` with your SQL string. It returns a **Promise**:

```js
const pool = require('../db/pool');

// All db queries are async
const getAllUsers = async () => {
  const result = await pool.query('SELECT * FROM users');
  return result.rows; // an array of user objects
};
```

### Reading the Result Object

`pool.query()` resolves to a result object with several properties. The one you'll use most is `.rows`:

```js
const result = await pool.query('SELECT * FROM users');

console.log(result.rows);
// [
//   { id: 1, username: 'alice', email: 'alice@example.com' },
//   { id: 2, username: 'bob', email: 'bob@example.com' }
// ]

console.log(result.rowCount); // 2 — how many rows were returned/affected
```

Each row in `result.rows` is a plain JavaScript object. Column names become property keys.

<details>

<summary><strong>Q: You run <code>SELECT * FROM users WHERE id = 99</code> and no user has that ID. What does <code>result.rows</code> look like?</strong></summary>

It will be an empty array: `[]`. `pool.query()` does not throw an error when a `SELECT` finds nothing — it just returns zero rows. Your model method should handle this and return `null` or throw a meaningful error rather than passing an empty array to the controller.

```js
const getUserById = async (id) => {
  const result = await pool.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0] || null; // first row or null if not found
};
```

</details>

## Parameterized Queries

When your queries include values that come from user input — an ID from the URL, a username from a form — you must use **parameterized queries**.

### Why Never Interpolate User Input Directly

Consider this seemingly reasonable code:

```js
// NEVER DO THIS
const getUserById = async (id) => {
  const result = await pool.query(`SELECT * FROM users WHERE id = ${id}`);
  return result.rows[0];
};
```

If `id` comes from user input, a malicious user could send:

```
1; DROP TABLE users; --
```

Your query would become:

```sql
SELECT * FROM users WHERE id = 1; DROP TABLE users; --
```

Postgres would execute both statements and your `users` table would be gone. This is **SQL injection** — one of the most common and devastating web vulnerabilities.

### The Safe Way: `$1` Placeholders

Parameterized queries separate the SQL structure from the values:

```js
const getUserById = async (id) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE id = $1',
    [id]  // values are passed in a separate array
  );
  return result.rows[0] || null;
};
```

* `$1` is a placeholder for the first value in the array
* `$2` would be the second, `$3` the third, etc.
* `pg` sends the SQL and values to Postgres separately
* Postgres treats the values as *data only*, never as SQL commands

Even if a user passes `1; DROP TABLE users; --` as `id`, Postgres will treat the entire string as a data value being compared to the `id` column — not as a command to execute. It will simply find no matching row.

<details>

<summary><strong>Q: A query needs to filter by both username and email. How do you write it with parameterized placeholders?</strong></summary>

```js
const getUserByCredentials = async (username, email) => {
  const result = await pool.query(
    'SELECT * FROM users WHERE username = $1 AND email = $2',
    [username, email]
  );
  return result.rows[0] || null;
};
```

`$1` maps to `username` (first element of the array) and `$2` maps to `email` (second element). The order of the array must match the order of the placeholders.

</details>

## CRUD Operations with `pg`

Here's how each CRUD operation looks using `pg`. These would typically live in a model file:

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

{% hint style="info" %}
`RETURNING *` is a PostgreSQL extension that makes an `INSERT`, `UPDATE`, or `DELETE` return the affected row(s) as if it were a `SELECT`. Without it, those operations return `rowCount` but no rows. Always use `RETURNING *` in your model methods so you can return the created/updated/deleted record to the controller.
{% endhint %}

<details>

<summary><strong>Q: What's the difference between <code>result.rows[0]</code> and <code>result.rows</code>? When do you use each?</strong></summary>

* `result.rows` — the full array of all matching rows. Use this when you expect multiple results (e.g., `list()` — all users).
* `result.rows[0]` — just the first row. Use this when you expect exactly one result (e.g., `find(id)` — one user, `create()` — the newly created row).

If you use `result.rows[0]` when no row exists, you'll get `undefined`. That's why methods like `find` and `update` often return `result.rows[0] || null` to make the "not found" case explicit.

</details>

<details>

<summary><strong>Q: Your <code>User.create()</code> method returns the newly created user. Your controller needs to send a <code>201 Created</code> status with that user as JSON. What should the controller look like?</strong></summary>

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

Notice the `try/catch` — database operations can fail (duplicate email, network issue, constraint violation). The `catch` passes the error to Express's error handling middleware via `next(err)` rather than crashing the process.

</details>
