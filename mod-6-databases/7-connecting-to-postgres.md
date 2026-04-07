# 7. Connecting to Postgres with `pg`

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-7-connecting-to-postgres)!
{% endhint %}

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Setup](#setup)
- [What is `pg`?](#what-is-pg)
  - [Step 1. Installing `pg` and `dotenv`](#step-1-installing-pg-and-dotenv)
  - [Step 2. Adding Postgres Configuration Values to `.env` File](#step-2-adding-postgres-configuration-values-to-env-file)
  - [Step 3. Create a `Pool` Instance](#step-3-create-a-pool-instance)
  - [Deep Dive: What Exactly is a Pool?](#deep-dive-what-exactly-is-a-pool)
- [Running Queries with `pool.query()`](#running-queries-with-poolquery)
  - [Beware of SQL Injections!](#beware-of-sql-injections)
  - [Parameterized Queries: `$1` Placeholders](#parameterized-queries-1-placeholders)
  - [RETURNING](#returning)
- [Practice: Writing Query Functions](#practice-writing-query-functions)

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

## Setup

**Before you begin:** seed the database by running `psql -f db/seed.sql` (Mac) or `sudo -u postgres psql -f db/seed.sql` (WSL). This creates the `users` table and inserts sample rows so you have real data to query during the lesson.

With a seeded database, we know that we can run SQL queries directly in `psql` and TablePlus. Now it's time to run those same queries from JavaScript.

Your Express server stores data in memory — JavaScript arrays that live only as long as the process is running. Restart the server and everything is gone. In this lesson, we connect a Node application to a real Postgres database using the `pg` library so that data persists.

**You already know the queries**:`SELECT * FROM users WHERE user_id = $1` is the same SQL you've been writing all week. The only new thing is how you send it — from JavaScript instead of a terminal.

## What is `pg`?

Your Postgres database is an independent process running on your computer (or a remote server). Your Node application can't just "call" it like a function or send an HTTP request to it with `fetch`.

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

The `pg` library acts as the bridge between a Node application and Postgres. It:

1. Opens a network connection from your Node process (your server) to the Postgres process
2. Sends your SQL strings over that connection
3. Receives the results and converts them into JavaScript objects you can work with

Without `pg`, there is no way to speak PostgreSQL from JavaScript.

Before we can use `pg` in our application, we need to do some configuration. This is boilerplate meaning it will be the same configuration steps every time you start a new project with `pg`!

### Step 1. Installing `pg` and `dotenv`

First, we need to install the `pg` and `dotenv` modules:

```sh
npm install pg dotenv
```

* `pg` is the Postgres "client"—it is the tool we will use to communicate with the Postgres server.
* `dotenv` helps us use environment variables stored in a `.env` file

### Step 2. Adding Postgres Configuration Values to `.env` File

In order to connect to our Postgres server, `pg` needs to know a few things:
* the host and port where the server is running
* the specific database to connect to
* the username and password credentials (not needed for MacOS, needed for WSL connections)

These are the same things that we provide to TablePlus when connecting to Postgres.

We often will store this information inside of a `.env` file rather than hard-coding it into our program. We will be using the variables defined in the `.env.template` file in the example repository for this lesson:

```sh
# Local development — credentials to connect to your local Postgres server
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=users_db
PG_USER=
PG_PASSWORD=

# Production — Add a value here to connect to a production database
# PG_CONNECTION_STRING=postgres://user:password@host:5432/database
```

Remember, never commit your `.env` file to GitHub. Make sure it is added to `.gitignore` alongside things like `node_modules`:

```gitignore
# .gitignore
node_modules
.env
```

{% hint style="info" %}
**What is PG_CONNECTION STRING?**

The `PG_CONNECTION_STRING` variable will be used when we eventually deploy a Postgres database onto a hosting platform like Render. Render will provide us with a single URL that contains all of the connection information in one string. If we want to test our application using the production database, we can add that connection string here.

For now, we can leave it commented out.
{% endhint %}

### Step 3. Create a `Pool` Instance

Take a look at `db/pool.js`. It imports the `Pool` constructor from `pg` and then creates a `pool` instance using the environment variables we just set:

```js
const { Pool } = require('pg');
const dotenv = require('dotenv');

dotenv.config(); // loads values from .env to create configurations

const productionConfig = {
  connectionString: process.env.PG_CONNECTION_STRING
};

const developmentConfig = {
  host: process.env.PG_HOST,
  port: process.env.PG_PORT,
  user: process.env.PG_USER,
  password: process.env.PG_PASSWORD,
  database: process.env.PG_DATABASE,
}

// If the PG_CONNECTION_STRING is set, use it. Otherwise use the individual variables.
const pool = process.env.PG_CONNECTION_STRING
  ? new Pool(productionConfig)
  : new Pool(developmentConfig);
```

When invoking the `Pool` constructor, we provide a configuration object that either has the single `connectionString` or individual values for `host`, `port`, etc. The `pool` instance represents our open connection to Postgres (technically a group of open connections) through which we can send SQL. 

The method `pool.query()` can be used to execute any SQL statement we provide as a string. Uncomment the test block in `pool.js` to confirm the connection is working:

```js
const test = async () => {
  const { rows } = await pool.query('SELECT * FROM users');
  console.log(rows);
};
test()
```

Run it with `node db/pool.js`. You should see your seeded users logged to the terminal.

Try it first *without* `.finally(() => pool.end())` — you'll notice the process hangs and never exits. That's because the pool keeps several database connections open in the background, waiting for more queries. Node won't exit while those connections are open.

`pool.end()` closes all the connections in the pool, allowing the process to exit. Chaining it in `.finally()` ensures it runs whether the query succeeds or fails.

{% hint style="info" %}
`pool.end()` is only needed in **one-shot scripts** — files you run directly with `node` to do a single task. When you're running a long-lived Express server, you *want* the pool to stay open so it can handle repeated requests. Never call `pool.end()` in a server.
{% endhint %}

Now that we know `pool` provides a connection to our database, we export it so that it can be used by other files in our project:

```js
module.exports = pool;
```

**<details><summary>Q: Why do we export the pool from a separate `db/pool.js` file rather than creating it inside each file that needs it?</summary>**

The pool should be a singleton — one shared instance for the entire application. If you created a new `Pool` in every file, you'd end up with multiple pools competing for connections and consuming more resources than necessary. Exporting from `db/pool.js` ensures the pool is created once and everything shares the same instance.

</details>

**<details><summary>Q: Why does `pool.js` check for `PG_CONNECTION_STRING` first, before the individual variables?</summary>**

Deployment platforms like Render and Railway automatically inject a `DATABASE_URL` or similar connection string environment variable when you add a Postgres database to your project. Using the connection string in production is simpler — one variable instead of five — and it's the format these platforms expect. Individual variables (`PG_HOST`, `PG_DATABASE`, etc.) are more readable during local development where you're configuring things manually. The `pool.js` file handles both cases so neither environment requires changes to the code.

</details>

### Deep Dive: What Exactly is a Pool?

When your application needs to query the database, it needs an open **connection** — a dedicated channel to communicate with Postgres. When we use `pg` in our servers, each time a client sends an HTTP request to our server, our server will then send a SQL query to Postgres using that open connection. 

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

However, only a connection can only process one SQL query at a time. If multiple users send HTTP requests to our server at the same time, we would end up with a bottleneck with more and more users waiting for their turn to use that one connection. Additionally, opening a new connection takes time and resources. If your server opened and closed a connection for every HTTP request, it would be noticeably slow.

A **connection pool** solves this by keeping a set of connections open and ready. When a query comes in, the poollends an available connection. When the query finishes, the connection returns to the pool.

No waiting for a single connection to free up. No waiting to open and close a connection. 

## Running Queries with `pool.query()`

Let's look closer at running queries with `pool.query()`. Typically, we will use `pool.js` to create the `pool` instance and export it. Then we will import it into other files to actually use `pool.query()`.

In `queries.js`, we start by importing the `pool` instance. Then, we can create functions for each specific query we want to run, such as getting all users from the table:
* `pool.query()` returns a **Promise**:
* `pool.query()` resolves to a result object. The property you'll use most is `.rows`:

```js
const pool = require('./pool');

const getAllUsers = async () => {
  const result = await pool.query('SELECT * FROM users ORDER BY user_id');
  return result.rows; // an array of user objects
};

// ...

const main = async () => {
  console.log('--- All users ---');
  const users = await getAllUsers();
  console.log(users);
  // --- All users ---
  // [
  //   { user_id: 1, username: 'ann_duong', email: 'ann@example.com' },
  //   { user_id: 2, username: 'reuben_o',  email: 'reuben@example.com' }
  // ]

  // ...
};

main()
  .catch((err) => {
    console.error(err);
    process.exit(1);
  })
  .finally(() => pool.end());
```

Each element of `result.rows` is a plain JavaScript object. Column names become property keys, exactly as named in the database.

You can also destructure `rows` directly from the result, which is a common shorthand:

```js
const { rows } = await pool.query('SELECT * FROM users');
console.log(rows); // same array as result.rows above
```

**<details><summary>Q: You run `SELECT * FROM users WHERE user_id = $1` with an ID that doesn't exist. What does `result.rows` look like?</summary>**

`result.rows` will be an empty array `[]`. `pool.query()` does not throw an error when a `SELECT` returns no rows — it just returns zero rows. Handle this explicitly:

```js
const getUserById = async (user_id) => {
  const { rows } = await pool.query(
    'SELECT * FROM users WHERE user_id = $1',
    [user_id]
  );
  return rows[0] || null; // first row, or null if not found
};
```

Returning `null` makes the "not found" case explicit for any code that calls this function.

</details>


### Beware of SQL Injections!

When your queries include values from user input — an ID from the URL, a username from a form — you must use **parameterized queries**.

{% hint style="danger" %}
```javascript
// NEVER DO THIS
const getUserById = async (user_id) => {
  const { rows } = await pool.query(`SELECT * FROM users WHERE user_id = ${user_id}`);
  return rows[0];
};
```
{% endhint %}

If `user_id` comes from user input, a malicious user could send:

```
1; DROP TABLE users; --
```

Your query would become:

```sql
SELECT * FROM users WHERE user_id = 1; DROP TABLE users; --
```

Postgres would execute both statements and your `users` table would be gone. This is **SQL injection** — one of the most common and destructive web vulnerabilities.

### Parameterized Queries: `$1` Placeholders

Parameterized queries separate the SQL structure from the values:

```js
const getUserById = async (user_id) => {
  const { rows } = await pool.query(
    'SELECT * FROM users WHERE user_id = $1',
    [user_id]  // values passed in a separate array
  );
  return rows[0] || null;
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
  const { rows } = await pool.query(
    'SELECT * FROM users WHERE username = $1 AND email = $2',
    [username, email]
  );
  return rows[0] || null;
};
```

`$1` maps to `username` (index 0 of the array) and `$2` maps to `email` (index 1). The order of values in the array must match the order of the placeholders in the SQL string.

</details>

### RETURNING

Unlike `SELECT`, write operations (`INSERT`, `UPDATE`, `DELETE`) don't return rows by default — they only tell you how many rows were affected (`rowCount`). For these write operations, if I want to see the result, I would need to write a second `SELECT` query:

{% hint style="warning" %}
Without RETURNING:
```js
const createUser = async (username, email) => {
  // First insert
  await pool.query(
    'INSERT INTO users (username, email) VALUES ($1, $2)',
    [username, email]
  );

  // Then query again to get the data
  const { rows } = await pool.query(
    'SELECT * FROM users WHERE username=$1',
    [username]
  );
  return rows[0];
};
```
{% endhint %}

However, we can also add use `RETURNING *` to the end of the query to get back the updated row:

{% hint style="success" %}
With RETURNING:
```js
// CREATE — inserts a new row and returns it
const createUser = async (username, email) => {
  const { rows } = await pool.query(
    'INSERT INTO users (username, email) VALUES ($1, $2) RETURNING *',
    [username, email]
  );
  return rows[0];
};
```
{% endhint %}

This `createUser()` function now inserts a given username and email into the users table and returns the newly created user — including the auto-generated `user_id` — without needing to make a second query.

{% hint style="info" %}
`RETURNING *` is a Postgres extension of SQL that makes `INSERT`, `UPDATE`, and `DELETE` return the affected row(s) as if they were a `SELECT`.
{% endhint %}

**<details><summary>Q: `createUser` returns the newly created user. How would you call it and log the result?</summary>**

```js
const main = async () => {
  const newUser = await createUser('new_person', 'new@example.com');
  console.log(newUser);
  // { user_id: 4, username: 'new_person', email: 'new@example.com' }
};

main()
  .catch(console.error)
  .finally(() => pool.end());
```

`createUser` is `async`, so you must `await` it. The returned object is the newly inserted row, including the `user_id` that Postgres generated.

</details>

## Practice: Writing Query Functions

`db/queries.js` is where we practice sending each type of SQL query from JavaScript. Each query is wrapped in an `async` function that calls `pool.query()` and returns the result.

Here are the two `SELECT` functions, already implemented:

```js
// db/queries.js
const pool = require('./pool');

// Returns all users, ordered by user_id
const getAllUsers = async () => {
  const { rows } = await pool.query(
    'SELECT * FROM users ORDER BY user_id'
  );
  return rows;
};

// Returns a single user by user_id, or null if not found
const getUserById = async (user_id) => {
  const { rows } = await pool.query(
    'SELECT * FROM users WHERE user_id = $1',
    [user_id]
  );
  return rows[0] || null;
};
```

Run the file with `npm run db:queries` to see these in action.

The remaining two operations — `UPDATE` and `DELETE` — follow the same pattern but write data instead of reading it. See if you can implement those functions!

**<details><summary>Solution</summary>**

```js
// UPDATE — modifies an existing row and returns it, or null if not found
const updateUser = async (user_id, username, email) => {
  const { rows } = await pool.query(
    `UPDATE users
     SET username = $1, email = $2
     WHERE user_id = $3
     RETURNING *`,
    [username, email, user_id]
  );
  return rows[0] || null;
};

// DELETE — removes a row and returns it, or null if not found
const deleteUser = async (user_id) => {
  const { rows } = await pool.query(
    'DELETE FROM users WHERE user_id = $1 RETURNING *',
    [user_id]
  );
  return rows[0] || null;
};
```
</details>

**<details><summary>Q: What is the difference between `rows[0]` and `rows`? When do you use each?</summary>**

- `rows` — the full array of matching rows. Use when you expect multiple results (e.g., `getAllUsers` — returns every user).
- `rows[0]` — the first row only. Use when you expect exactly one result (e.g., `getUserById`, `createUser`, `updateUser`).

If you use `rows[0]` and no row exists, you get `undefined`. That's why functions like `getUserById` and `updateUser` return `rows[0] || null` — to make the "not found" case explicit.

</details>
