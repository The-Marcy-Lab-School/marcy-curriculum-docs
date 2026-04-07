# 7. Connecting to Postgres with `pg`

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-7-connecting-to-postgres)!
{% endhint %}

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Setup](#setup)
- [What is `pg`?](#what-is-pg)
  - [Creating a `Pool` Instance](#creating-a-pool-instance)
  - [Sending a Query with `pool.query()`](#sending-a-query-with-poolquery)
  - [Closing Connections with `pool.end()`](#closing-connections-with-poolend)
  - [Query Challenge: Get a Single User](#query-challenge-get-a-single-user)
- [Parameterized Queries](#parameterized-queries)
  - [Beware of SQL Injections!](#beware-of-sql-injections)
  - [`$NUM` Placeholders](#num-placeholders)
  - [Parameterized Queries Challenge: `findByCredentials()`](#parameterized-queries-challenge-findbycredentials)
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

## What is `pg`?

Your Postgres database is an independent process running on your computer (or a remote server). Your Node application can't just call it like a function or use `fetch()` to send it an HTTP request.

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

The `pg` library acts as the bridge between a Node application and Postgres. It:

1. Opens a network connection from your Node process (your server) to the Postgres process
2. Sends your SQL strings over that connection
3. Receives the results and converts them into JavaScript objects you can work with

Without `pg`, there is no way to speak PostgreSQL from JavaScript.

Before we can use `pg` in our application, we need to do some set up.

First, we need to install the `pg` modules:

```sh
npm install pg
```

`pg` is the Postgres "client" library—it has the tools we will use to communicate with the Postgres server.

### Creating a `Pool` Instance

In order to connect to our Postgres server, `pg` needs to know a few things:
* the **host** and **port** of Postgres
* the **database name**
* username and password credentials (on MacOS, these may be optional if your local Postgres is configured to use your system's login)

The `pg` module provides a class called `Pool` whose constructor we invoke with a configuration object containing these details.

{% code title="db/pool.js" overflow="wrap" lineNumbers="true" %}
```javascript
const { Pool } = require('pg');

const config = {
  host: 'localhost',
  port: 5432,
  database: 'users_db',
  user: 'username',
  password: 'password',
};

const pool = new Pool(config);
```
{% endcode %}

A `Pool` instance manages a group of connections to your Postgres server (a.k.a "connection pool"). 

{% hint style="info" %}
**Why do we need a pool of connections rather than one?**

When your application needs to query the database, it needs an open **connection** — a dedicated channel to communicate with Postgres. When we use `pg` in our servers, each time a client sends an HTTP request to our server, our server will then send a SQL query to Postgres using that open connection. 

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

However, a connection can only process one SQL query at a time. If multiple users send HTTP requests to our server at the same time, we would end up with a bottleneck with more and more users waiting for their turn to use that one connection. 

Additionally, opening a new connection takes time and resources. If your server opened and closed a connection for every HTTP request, it would be noticeably slow.

A **connection pool** solves this by keeping a set of connections open and ready. When a query comes in, the pool lends an available connection. When the query finishes, the connection returns to the pool.

No waiting for a single connection to free up. No waiting to open and close a connection.
{% endhint %}

### Sending a Query with `pool.query()`

We're ready to send SQL to Postgres!

The method `pool.query()` can be used to execute any SQL statement we provide as a string:

{% code title="db/pool.js" %}
```javascript
const printUsers = async () => {
  const result = await pool.query('SELECT * FROM users');
  console.log(result);
  // {
  //   ...,
  //   rows: [ /* user data */ ],
  //   ...,
  // }
};
```
{% endcode %}

Here are some key details about `pool.query()`:
* `pool.query()` returns a **Promise**
* `pool.query()` resolves to a result object. The property you'll use most is `.rows`
* Each element of `result.rows` is a plain JavaScript object. Column names become property keys, exactly as named in the database.

Most often, we will only care about `rows` so it is common to destructure it immediately from the results:

{% code title="db/pool.js"%}
```javascript
const printUsers = async () => {
  const { rows } = await pool.query('SELECT * FROM users');
  console.log(rows);
  // [ /* user data */ ]
};
```
{% endcode %}

Run this file with `node db/pool.js`. You should see your seeded users logged to the terminal!

### Closing Connections with `pool.end()`

You'll notice that your Node program executes the query and then hangs—the process never exits. That's because the pool keeps several database connections open in the background, waiting for more queries. Node won't exit while those connections are open.

`pool.end()` closes all the connections in the pool, allowing the process to exit cleanly. Let's add a call to `pool.end()` after we run all of our queries

{% code title="db/pool.js" %}
```javascript
const main = async () => {
  await printUsers();

  // Close connections and print after to confirm
  await pool.end();
  console.log('Connection pool drained');
};

main()
```
{% endcode %}

`pool.end()` is only needed in **one-shot scripts** — files you run directly with `node` to do a single task. When you're running a long-lived Express server, you *want* the pool to stay open so it can handle repeated requests. Never call `pool.end()` in a server.

### Query Challenge: Get a Single User

Write a function called `getUserById(user_id)` that *returns* (not prints) a specific user given the user's `user_id`.

**<details><summary>Solution</summary>**

**DANGER:** This solution works but has a serious security vulnerability. Read the next section to see why.

```js
const getUserById = async (user_id) => {
  const { rows } = await pool.query(`SELECT * FROM users WHERE user_id = ${user_id}`);
  return rows[0];
}
```

Even though this query only ever returns a single user, it will still put the result in a `rows` array. Rather than return the entire array, we return just the first element.

In `main()` we can call `getUserById()` and log the result:

```js
const main = async () => {
  await printUsers()
  
  const user = await getUserById(1);
  console.log(user);
  // { user_id: 1, username: 'ann_duong', email: 'ann@example.com' },

  await pool.end();
  console.log('Connection pool drained');
};

main()
```

</details>

**<details><summary>Q: What happens if you call the function with an ID that doesn't exist? What does `result.rows` look like?</summary>**

`result.rows` will be an empty array `[]`. `pool.query()` does not throw an error when a `SELECT` returns no rows — it just returns zero rows.

```js
const getUserById = async (user_id) => {
  const { rows } = await pool.query(`SELECT * FROM users WHERE user_id = ${user_id}`);
  return rows[0] || null;
}
```

Returning `null` makes the "not found" case explicit for any code that calls this function.

</details>

## Parameterized Queries

### Beware of SQL Injections!

When your queries include values from user input — an ID from the URL, a username from a form — using string interpolation can open serious security vulnerabilities:

{% hint style="danger" %}
```javascript
const getUserById = async (user_id) => {
  // NEVER USE STRING INTERPOLATION
  const { rows } = await pool.query(`SELECT * FROM users WHERE user_id = ${user_id}`);
  return rows[0] || null;
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

### `$NUM` Placeholders

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

### Parameterized Queries Challenge: `findByCredentials()`

Write a function called `findByCredentials(username, email)` that finds a user with the given `username` and `email`.

**<details><summary>Solution</summary>**

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

## RETURNING

Suppose I wanted to write a function `createUser(username, email)` that inserts a user into the database and then returns the newly created user.

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

Instead, we can add use `RETURNING *` to the end of the query to get back the updated row:

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

In `main()` we can call `createUser()` and log the result:

```js
const main = async () => {
  // ...
  
  const newUser = await createUser('new_person', 'new@example.com');
  console.log(newUser);
  // { user_id: 4, username: 'new_person', email: 'new@example.com' }
};

main();
```

The returned object is the newly inserted row, including the `user_id` that Postgres generated.

{% hint style="info" %}
Due to the `UNIQUE` constraint on username and email set in the `seed.sql` file, running this file again with the same username and email will cause an error!
{% endhint %}

## Practice: Writing Query Functions

Now it is time for you to show what you've learned! Create the two functions below, invoke them in `main()` and print the results! Then run `node db/pool.js` to test your work.

**`updateUser(user_id, username, email)`**
- `UPDATE` the row with the matching `user_id`
- Set `username` and `email` to the new values
- Use `RETURNING *` to get back the updated row
- Return the updated user, or `null` if not found

**`deleteUser(user_id)`**
- `DELETE` the row with the matching `user_id`
- Use `RETURNING *` to get back the deleted row
- Return the deleted user, or `null` if not found

**<details><summary>Solution</summary>**

```js
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

- `rows` — the full array of matching rows. Use when you expect multiple results (e.g., `getAllUsers`).
- `rows[0]` — the first row only. Use when you expect exactly one result (e.g., `getUserById`, `createUser`, `updateUser`).

If you use `rows[0]` and no row exists, you get `undefined`. That's why functions like `getUserById` and `updateUser` return `rows[0] || null` — to make the "not found" case explicit.

</details>
