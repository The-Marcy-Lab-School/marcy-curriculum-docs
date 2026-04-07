# 8. Postgres Models

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-8-postgres-models)!
{% endhint %}

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Setup](#setup)
- [Where Does `pg` Fit Into an Express Application?](#where-does-pg-fit-into-an-express-application)
- [Configuring `pg` In an Express App](#configuring-pg-in-an-express-app)
  - [Environment Variables](#environment-variables)
  - [Production and Development Configurations](#production-and-development-configurations)
  - [Export the `pool` Instance](#export-the-pool-instance)
- [Replacing the In-Memory Model](#replacing-the-in-memory-model)
  - [The Postgres Model](#the-postgres-model)
- [Controllers and Error Handling](#controllers-and-error-handling)
  - [`try/catch` in Every Controller](#trycatch-in-every-controller)
  - [Error-Handling Middleware](#error-handling-middleware)
- [Tracing a Request End to End with Sequence Diagrams](#tracing-a-request-end-to-end-with-sequence-diagrams)
- [Practice: Write the Owner Model](#practice-write-the-owner-model)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is the difference between an in-memory model and a Postgres-backed model?
2. When you swap a model from in-memory to Postgres, what changes in your controllers and routes?
3. Why do we store database credentials in a `.env` file and never commit it?
4. Why must every model method be `async` when using `pg`?
5. What is the purpose of `try/catch` in a controller, and what happens if you skip it?
6. What does the error-handling middleware do, and why must it have exactly four parameters?

## Key Concepts

* **In-memory model** — a model that stores data in JavaScript arrays and objects. All data is lost when the server restarts.
* **Postgres-backed model** — a model that uses `pool.query()` to read and write data in a database. Data persists across server restarts.
* **`pool.js`** — a dedicated file that creates and exports the `Pool` instance. Models `require()` it instead of creating their own connections.
* **`.env` file** — a file that stores environment variables like database credentials. It is never committed to version control.
* **`async` model method** — every method that calls `pool.query()` must be `async` because `pool.query()` returns a Promise.
* **`try/catch` in controllers** — catches any error thrown by a model method and forwards it to the error-handling middleware via `next(err)`.
* **Error-handling middleware** — an Express middleware with exactly four parameters `(err, req, res, next)` that sends a structured error response when something goes wrong.

## Setup

**Before you begin:** Clone down the repo above and follow the setup steps in the `README` file. In this lesson, you will be building an API for managing a list of pets and owners.

Once the server is running, you can test the API endpoints using `curl` from a terminal. Here are the commands for every route:

**Pets**

```sh
# Get all pets
curl http://localhost:3000/api/pets/

# Get a single pet
curl http://localhost:3000/api/pets/1

# Create a pet
curl -X POST http://localhost:3000/api/pets/ \
  -H "Content-Type: application/json" \
  -d '{"name": "Mochi", "species": "cat"}'

# Update a pet
curl -X PATCH http://localhost:3000/api/pets/1 \
  -H "Content-Type: application/json" \
  -d '{"name": "Mochi", "species": "rabbit"}'

# Delete a pet
curl -X DELETE http://localhost:3000/api/pets/1
```

**Owners**

```sh
# Get all owners
curl http://localhost:3000/api/owners/

# Create an owner
curl -X POST http://localhost:3000/api/owners/ \
  -H "Content-Type: application/json" \
  -d '{"name": "Alex Kim", "email": "alex@example.com"}'

# Get a single owner
curl http://localhost:3000/api/owners/1

# Update an owner
curl -X PATCH http://localhost:3000/api/owners/1 \
  -H "Content-Type: application/json" \
  -d '{"name": "Alexandra Kim"}'

# Delete an owner
curl -X DELETE http://localhost:3000/api/owners/1
```

## Where Does `pg` Fit Into an Express Application?

In the previous lesson, you ran queries in a standalone Node script (`db/pool.js`) and called `pool.end()` at the end to close the connection pool. That worked fine for a one-shot script, but a real Express server needs to keep the pool open indefinitely so it can handle request after request.

In this lesson, we'll look at how to utilize `pg` in the context of an Express server application. When a user sends a `GET /api/pets` request to our server, the server will then use `pg` to send a `SELECT * FROM pets;` SQL query to Postgres instead of using an in-memory array.

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

The beauty of this MVC architecture, as you will see, is that our controllers won't need to change at all! Just our models.

In this lesson we will also learn some best practices for utilizing `pg` in the context of an Express server application. We will:
* Utilize a `db/seed.sql` file to create the database schema and seed the tables with data
* Store database connection credentials in a `.env` file for security
* Initialize a connection `pool` in `db/pool.js` and export it so it can be shared by our models
* Import the `pool` into our model files and update the model methods to use SQL instead of the in-memory array

{% hint style="info" %}
**Do NOT call `pool.end()`**

In the script from the last lesson, you called `pool.end()` so the Node process could exit. In a server, you *want* the process to keep running. As long as `pool.end()` is never called, the pool stays open and ready to handle queries for every incoming HTTP request.
{% endhint %}

## Configuring `pg` In an Express App

In the previous lesson, we initialized a `Pool` instance like this:

{% hint style="warning" %}
```javascript
const { Pool } = require('pg');

const config = {
  host: 'localhost',
  port: 5432,
  database: 'users_db',
  user: 'username',
  password: 'password',
}

const pool = new Pool(config);
```
{% endhint %}

Hard-coding database credentials (host, port, username, password) directly in your source code is dangerous — anyone who can read your code can read your secrets, and those secrets get committed to version control.

Instead, we should store this data in a gitignored `.env` file.

### Environment Variables

Start by adding your database credentials to the `.env` file (copy the `.env.template` file if you haven't already):

{% code title="server/.env" %}
```sh
# Local development — fill in your values
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=pets_db
PG_USER=your_username
PG_PASSWORD=your_password

# When using a deployed production database, the host (e.g. Render) will provide a connection string to use instead
# PG_CONNECTION_STRING=postgres://user:password@host:5432/database
```
{% endcode %}

**Never commit `.env` to Git.** Add it to `.gitignore` immediately. Credentials that reach a public repository are compromised — even if you delete them later, they may have been indexed or copied.

Your `.gitignore` should already include:

```
.env
node_modules/
```

### Production and Development Configurations

`pool.js` uses the `dotenv` library to load environment variables into `process.env`. It then creates a configuration object and initializes the pool:

{% code title="server/db/pool.js" %}
```javascript
require('dotenv').config(); // loads environment variables from .env into process.env
const { Pool } = require('pg');

// In a local development environment, use this configuration
const devConfig = {
  host: process.env.PG_HOST,
  port: process.env.PG_PORT,
  user: process.env.PG_USER,
  password: process.env.PG_PASSWORD,
  database: process.env.PG_DATABASE,
}

// When using a deployed production DB hosted by a platform like Render
// you will be given a connection string to use instead
const prodConfig = {
  connectionString: process.env.PG_CONNECTION_STRING
};

// If the connection string exists, use the production configuration
const config = process.env.PG_CONNECTION_STRING ? prodConfig : devConfig;

// Create the pool and export it
const pool = new Pool(config);
module.exports = pool;
```
{% endcode %}

Notice that there are two configuration modes in this file:

- **Local development** — uses individual vars (`PG_HOST`, `PG_PORT`, etc.)
- **Production** — uses the single `PG_CONNECTION_STRING`. 

When using a deployed production database, the host (e.g. Render) will provide a connection string to use instead. If that connection string is present, that takes priority. If not, it falls back to the development configuration. This means the same `pool.js` works in both environments without any code changes.

### Export the `pool` Instance

Rather than executing SQL queries in `pool.js`, `pool` is exported. This way every model that needs to query the database simply does:

```javascript
const pool = require('../db/pool');
```

As a result, there is one shared pool of connections used across the whole server.

We're now ready to swap out the in-memory array for Postgres!

## Replacing the In-Memory Model

Before we knew about Postgres, our app used an in-memory model — data stored in plain JavaScript arrays:

{% code title="server/models/petModel-memory.js"%}
```javascript
let allPets = [];
let nextId = 1;

module.exports.list = () => [...allPets];

module.exports.find = (pet_id) =>
  allPets.find((p) => p.pet_id === Number(pet_id)) || null;

module.exports.create = (name, species) => {
  const pet = { pet_id: nextId++, name, species };
  allPets.push(pet);
  return pet;
};

// more methods...
```
{% endcode %}

This works, but the data disappears the moment the server restarts. There's also a subtle difference: the in-memory methods return values *synchronously* — they don't return Promises.

### The Postgres Model

Here is the same model rewritten to import `pool` and use `pool.query()`:

{% code title="server/models/petModel.js" %}
```javascript
const pool = require('../db/pool');

module.exports.list = async () => {
  const { rows } = await pool.query('SELECT * FROM pets ORDER BY pet_id');
  return rows;
};

module.exports.find = async (pet_id) => {
  const { rows } = await pool.query(
    'SELECT * FROM pets WHERE pet_id = $1',
    [pet_id]
  );
  return rows[0] || null;
};

module.exports.create = async (name, species) => {
  const { rows } = await pool.query(
    'INSERT INTO pets (name, species) VALUES ($1, $2) RETURNING *',
    [name, species]
  );
  return rows[0];
};

module.exports.update = async (pet_id, name, species) => {
  const { rows } = await pool.query(
    `UPDATE pets
     SET name = COALESCE($1, name), species = COALESCE($2, species)
     WHERE pet_id = $3
     RETURNING *`,
    [name, species, pet_id]
  );
  return rows[0] || null;
};

module.exports.destroy = async (pet_id) => {
  const { rows } = await pool.query(
    'DELETE FROM pets WHERE pet_id = $1 RETURNING *',
    [pet_id]
  );
  return rows[0] || null;
};
```
{% endcode %}

**What changed in the model:**

- Every method is now `async` — `pool.query()` returns a Promise, so you must `await` it
- Data comes from Postgres instead of an in-memory array
- IDs are generated by Postgres (`SERIAL`), not by a JavaScript counter
- `RETURNING *` brings back the inserted/updated/deleted row so you don't need a follow-up `SELECT`
- `COALESCE($1, name)` in `UPDATE` keeps the existing value when a field is omitted — the Postgres equivalent of `name ?? pet.name`

**What did NOT change:**

- The exported function names: `list`, `find`, `create`, `update`, `destroy`
- The parameters each function accepts
- What each function returns (a single object, an array, or `null`)

This is the payoff of **separation of concerns**. The controllers call the same model methods with the same arguments and get back the same shapes of data. They have no idea — and don't need to know — whether the data came from memory or Postgres.

{% hint style="info" %}
Open `petControllers.js`. Read it. Notice that it does not change at all when you swap from the in-memory model to the Postgres model. Not a single line. The controller only knows that `petModel.find(id)` gives it a pet or `null`. Where the pet comes from is the model's problem.

**This is why MVC matters.**
{% endhint %}

## Controllers and Error Handling

In-memory model methods never fail — they operate on local variables and return immediately. Postgres model methods can fail for many reasons: the database is down, the query has a syntax error, a unique constraint is violated, the connection times out. Any of these will cause `pool.query()` to throw.

### `try/catch` in Every Controller

Unhandled rejections crash the request and the client gets no response (or a generic network error). 

With `try/catch`, you can catch the error and handle it gracefully without crashing the server.

{% code title="server/controllers/petControllers.js" %}
```javascript
const petModel = require('../models/petModel');

const getAllPets = async (req, res, next) => {
  try {
    const pets = await petModel.list();
    res.send(pets);
  } catch (err) {
    // Doing this for every controller would be repetitive
    res.status(500).send({ message: `Internal Server Error`});
  }
};

// other controllers...
```
{% endcode %}

### Error-Handling Middleware

Doing this for every single controller would be highly repetitive. A better practice is to instead define a generic catch-all error handling controller in `index.js`:

{% code title="server/index.js" %}
```javascript
// Error-handling middleware — must have exactly four parameters
const handleError = (err, req, res, next) => {
  console.error(err);
  res.status(500).send({ message: `Internal Server Error` });
};

app.use(handleError);
```
{% endcode %}

This middleware must be registered *after* all your routes. It acts as a catch-all: any error from any route flows here, gets logged, and gets sent back as a structured JSON response with an appropriate status code.

When any controller calls `next(err)`, Express skips all remaining regular middleware and routes and looks for an error-handling middleware like the one we just created.

{% code title="server/controllers/petControllers.js" %}
```javascript
const petModel = require('../models/petModel');

const getAllPets = async (req, res, next) => {
  try {
    const pets = await petModel.list();
    res.send(pets);
  } catch (err) {
    next(err); // passes control to the error handler
  }
};

const getPet = async (req, res, next) => {
  try {
    const pet = await petModel.find(req.params.pet_id);
    if (!pet) return res.status(404).json({ error: 'Pet not found' });
    res.send(pet);
  } catch (err) {
    next(err); // passes control to the error handler
  }
};

// so on...
```
{% endcode %}

The pattern inside every controller is the same:

1. `await` the model method inside `try`
2. Send a success response if everything worked (or a `4xx` error if the user made a mistake)
3. In `catch`, call `next(err)` and let the middleware handle the error.

{% hint style="warning" %}
Express identifies an error-handling middleware *only* by its four-parameter signature `(err, req, res, next)`. If you write it with three parameters like a regular middleware, Express will never call it for errors.
{% endhint %}

**<details><summary>Q: Why do handle user errors (`4xx` errors) separately from the `catch` block?</summary>**

`pet === null` means the query *succeeded* but found no matching row. That's a 404 — the resource doesn't exist.

An error caught by `catch` means something actually *broke* — a database failure, a malformed query, a connection timeout. That's a 500.

These are different problems that deserve different status codes. `if (!pet)` handles the "not found" case; `catch` handles the "something went wrong" case.

</details>


## Tracing a Request End to End with Sequence Diagrams

To help visualize the entire process, we can use a **sequence diagram** which shows the interactions between each layer of the application.

Here is the full sequence for when a user sends a `GET /api/pets/2` request:

![The sequence is client → Express → controller → model → Postgres → model → controller → Express → Client](./img/8-postgres-models/sequence-get-pet-2.png)

Each layer only knows about the layers directly next to it: 
- The client knows nothing about Express internals. 
- Express knows nothing about the model. 
- The controller knows nothing about SQL. 
- The model knows nothing about HTTP. 

That clean separation is what makes the code easy to reason about and easy to change — including swapping the model from in-memory to Postgres.

**<details><summary>Q: What does the flow look like when a database error occurs?</summary>**

Suppose a client tries to create an owner with an email that already exists in the database. The `owners.email` column has a `UNIQUE` constraint, so Postgres will reject the insert.

![A user tries to POST a user with a duplicate email, causing Postgres to reject the insert and throw an error.](./img/8-postgres-models/sequence-error.png)

The query reaches Postgres, but Postgres rejects it because `ann@example.com` is already in the table. `pool.query()` throws, the controller's `catch` block calls `next(err)`, and `handleError` sends the error response. No stack trace leaks. No silent crash.

</details>

## Practice: Write the Owner Model

The repo includes:

- `ownerModel-memory.js` — the in-memory version, already complete
- `ownerModel.js` — a stub with empty functions, waiting for you to fill in
- `ownerControllers.js` — already wired up, currently pointing at `ownerModel-memory`

The `owners` table already exists in the database after running the seed file:

```sql
CREATE TABLE owners (
  owner_id SERIAL PRIMARY KEY,
  name     TEXT NOT NULL,
  email    TEXT NOT NULL UNIQUE
);
```

**Step 1:** Fill in each function in `ownerModel.js` using `pool.query()`. Use `petModel.js` as your reference — the structure is identical, just swap the table name and column names.

**Step 2:** Once your model is complete, open `ownerControllers.js` and change this one line:

```javascript
// Before
const ownerModel = require('../models/ownerModel-memory');

// After
const ownerModel = require('../models/ownerModel');
```

That's it. The controllers, routes, and everything else stay exactly the same. Test your endpoints with Postman or a browser — they should now read from and write to the database.

**<details><summary>Solution: `ownerModel.js`</summary>**

```javascript
const pool = require('../db/pool');

module.exports.list = async () => {
  const { rows } = await pool.query('SELECT * FROM owners ORDER BY owner_id');
  return rows;
};

module.exports.find = async (owner_id) => {
  const { rows } = await pool.query(
    'SELECT * FROM owners WHERE owner_id = $1',
    [owner_id]
  );
  return rows[0] || null;
};

module.exports.create = async (name, email) => {
  const { rows } = await pool.query(
    'INSERT INTO owners (name, email) VALUES ($1, $2) RETURNING *',
    [name, email]
  );
  return rows[0];
};

module.exports.update = async (owner_id, name, email) => {
  const { rows } = await pool.query(
    `UPDATE owners
     SET name = COALESCE($1, name), email = COALESCE($2, email)
     WHERE owner_id = $3
     RETURNING *`,
    [name, email, owner_id]
  );
  return rows[0] || null;
};

module.exports.destroy = async (owner_id) => {
  const { rows } = await pool.query(
    'DELETE FROM owners WHERE owner_id = $1 RETURNING *',
    [owner_id]
  );
  return rows[0] || null;
};
```

</details>

**<details><summary>Q: Why does the `email` column have a `UNIQUE` constraint, and what happens when you violate it?</summary>**

The `UNIQUE` constraint on `email` prevents two owners from being registered with the same email address — a common requirement for accounts or profiles.

If you try to `INSERT` a duplicate email, Postgres throws an error with code `23505` (unique violation). `pool.query()` rejects the Promise, your controller's `catch` block calls `next(err)`, and the error-handling middleware sends back a 500.

For a production app, you would inspect `err.code === '23505'` in the controller or model and return a more specific 409 Conflict response instead of a generic 500.

</details>
