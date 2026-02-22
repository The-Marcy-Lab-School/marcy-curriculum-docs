# 12. Async Patterns with `pg`

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/5-2-2-async-patterns-pg)!
{% endhint %}

In the previous lesson, you learned how to connect to Postgres and run queries with `pg`. Every one of those queries is **asynchronous** — they return Promises. In this lesson, we'll dig into what that means for your model methods, how to handle errors from async database calls, and — most importantly — how to swap an in-memory model for a Postgres-backed one without touching a single line of controller or route code.

This lesson is the payoff of MVC.

**Table of Contents:**

* [Essential Questions](12-async-patterns-pg.md#essential-questions)
* [Terms](12-async-patterns-pg.md#terms)
* [Why Database Calls Are Async](12-async-patterns-pg.md#why-database-calls-are-async)
* [Async Model Methods](12-async-patterns-pg.md#async-model-methods)
  * [Every Method Becomes async](12-async-patterns-pg.md#every-method-becomes-async)
  * [Error Handling with try/catch](12-async-patterns-pg.md#error-handling-with-trycatch)
* [Async Controllers](12-async-patterns-pg.md#async-controllers)
* [The Model Swap: MVC's Biggest Payoff](12-async-patterns-pg.md#the-model-swap-mvcs-biggest-payoff)
  * [The In-Memory Model](12-async-patterns-pg.md#the-in-memory-model)
  * [The Postgres Model](12-async-patterns-pg.md#the-postgres-model)
  * [What Doesn't Change](12-async-patterns-pg.md#what-doesnt-change)
* [Express Error Handling Middleware](12-async-patterns-pg.md#express-error-handling-middleware)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. Why are all `pg` database queries asynchronous?
2. How does `async/await` change the way you write model methods?
3. Why do async functions need `try/catch` for error handling?
4. When you swap an in-memory model for a Postgres model, what parts of the application change? What parts stay the same?
5. What is error handling middleware in Express? How do you use `next(err)` to trigger it?

## Terms

* **Asynchronous** — code that doesn't block execution while waiting for an operation to complete. Database queries are async because the database is a separate process and the response takes time.
* **`async` function** — a function declared with the `async` keyword that always returns a Promise and allows use of `await` inside.
* **`await`** — pauses execution inside an `async` function until a Promise resolves, then returns the resolved value.
* **`try/catch`** — a pattern for handling errors in async code. Code in the `try` block runs normally; if it throws, execution jumps to the `catch` block.
* **`next(err)`** — passing an error to Express's `next` function triggers error handling middleware instead of the normal response flow.
* **Error Handling Middleware** — a special Express middleware with four parameters `(err, req, res, next)` that catches errors forwarded by `next(err)`.

## Why Database Calls Are Async

When your Node server calls `pool.query()`, it sends a message over a network socket to the Postgres process (even if both are running on your laptop, they're separate processes communicating over a socket).

Postgres receives the query, executes it, and sends the results back. This round trip takes time — not much time (usually milliseconds), but time nonetheless. JavaScript is single-threaded: if it stopped and waited for every database call to finish before continuing, your entire server would be frozen during that wait.

`pg` returns a Promise so Node can schedule the wait asynchronously: "Go do other things. When the database responds, run the next part of this code."

```
Server receives HTTP request
  ↓
Controller calls model method
  ↓
Model sends query to Postgres (async — returns Promise)
  ↓
Node: "I'll wait. Handling other requests in the meantime..."
  ↓              ↓
(other requests)  Postgres finishes, returns result
                  ↓
              Node: "Database is done. Resume model method."
                  ↓
              Model returns data to controller
                  ↓
              Controller sends HTTP response
```

## Async Model Methods

### Every Method Becomes `async`

Because `pool.query()` is async, any function that calls it must be `async` too so it can use `await`:

```js
// Without async/await — WRONG
static find(id) {
  const result = pool.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0]; // result is a Promise, not the actual result!
}

// With async/await — CORRECT
static async find(id) {
  const result = await pool.query('SELECT * FROM users WHERE id = $1', [id]);
  return result.rows[0] || null;
}
```

Without `await`, `result` would be a Promise object, not the query result. `result.rows` would be `undefined`.

<details>

<summary><strong>Q: What would happen if you forgot the <code>await</code> keyword before <code>pool.query()</code>?</strong></summary>

`pool.query()` would still run — the database query would still be sent — but the function would not wait for it to finish. `result` would be a Promise object, not the resolved result. When you then access `result.rows`, you'd get `undefined` because Promise objects don't have a `.rows` property. Your controller would likely receive `undefined` from the model and send an unexpected response to the client.

Always double-check that every `pool.query()` call is preceded by `await`, and that the function containing it is declared `async`.

</details>

### Error Handling with `try/catch`

Database queries can fail for many reasons:
- A constraint is violated (e.g., inserting a duplicate email with a UNIQUE constraint)
- The database connection drops
- A query has a syntax error
- A referenced foreign key doesn't exist

Without error handling, these failures would crash your server or cause unhandled Promise rejections. Wrap async model code in `try/catch`:

```js
static async create(username, email) {
  try {
    const result = await pool.query(
      'INSERT INTO users (username, email) VALUES ($1, $2) RETURNING *',
      [username, email]
    );
    return result.rows[0];
  } catch (err) {
    // Re-throw with context so the controller knows what happened
    throw new Error(`Failed to create user: ${err.message}`);
  }
}
```

Or you can handle `try/catch` in the controller and let the model throw naturally — both patterns are common. We'll look at the controller pattern below.

## Async Controllers

Since model methods are now `async` and return Promises, controllers that call them must also be `async`:

```js
const getUser = async (req, res, next) => {
  try {
    const user = await User.find(req.params.id);
    if (!user) return res.status(404).json({ error: 'User not found' });
    res.json(user);
  } catch (err) {
    next(err); // pass errors to Express error handling middleware
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

The pattern is consistent:
1. `async` controller
2. `try` block with `await` model calls
3. `catch` block that calls `next(err)` to delegate to error middleware

<details>

<summary><strong>Q: Why do controllers call <code>next(err)</code> instead of sending an error response directly?</strong></summary>

Centralizing error handling in middleware keeps controllers lean. If every controller had to format error responses itself, you'd repeat that logic everywhere and your error format might be inconsistent. By calling `next(err)`, you delegate to a single error handling middleware that formats all error responses the same way. It also makes it easy to log errors in one place, distinguish between different error types (database errors vs validation errors), and change your error response format without touching every controller.

</details>

## The Model Swap: MVC's Biggest Payoff

This is the most important moment in the entire backend unit. You're going to swap out an in-memory model for a Postgres-backed one — and nothing else in your application will change.

### The In-Memory Model

You built this in the MVC lesson:

```js
// models/Pet.js (in-memory version)
class Pet {
  static #allPets = [];
  static #nextId = 1;

  static list() {
    return Pet.#allPets;
  }

  static find(id) {
    return Pet.#allPets.find((p) => p.id === Number(id)) || null;
  }

  static create(name, species) {
    const pet = { id: Pet.#nextId++, name, species };
    Pet.#allPets.push(pet);
    return pet;
  }

  static update(id, name, species) {
    const pet = Pet.find(id);
    if (!pet) return null;
    pet.name = name ?? pet.name;
    pet.species = species ?? pet.species;
    return pet;
  }

  static destroy(id) {
    const idx = Pet.#allPets.findIndex((p) => p.id === Number(id));
    if (idx === -1) return null;
    return Pet.#allPets.splice(idx, 1)[0];
  }
}
```

Data lives in `#allPets`. Restart the server: data is gone.

### The Postgres Model

Now we replace the internals with `pg` queries. **The method names and signatures stay exactly the same:**

```js
// models/Pet.js (Postgres version)
const pool = require('../db/pool');

class Pet {
  static async list() {
    const result = await pool.query('SELECT * FROM pets ORDER BY id');
    return result.rows;
  }

  static async find(id) {
    const result = await pool.query(
      'SELECT * FROM pets WHERE id = $1',
      [id]
    );
    return result.rows[0] || null;
  }

  static async create(name, species) {
    const result = await pool.query(
      'INSERT INTO pets (name, species) VALUES ($1, $2) RETURNING *',
      [name, species]
    );
    return result.rows[0];
  }

  static async update(id, name, species) {
    const result = await pool.query(
      `UPDATE pets SET name = COALESCE($1, name), species = COALESCE($2, species)
       WHERE id = $3 RETURNING *`,
      [name, species, id]
    );
    return result.rows[0] || null;
  }

  static async destroy(id) {
    const result = await pool.query(
      'DELETE FROM pets WHERE id = $1 RETURNING *',
      [id]
    );
    return result.rows[0] || null;
  }
}

module.exports = Pet;
```

### What Doesn't Change

Look at your controllers. They still call the same model methods:

```js
// controllers/petControllers.js — UNCHANGED
const getAllPets = async (req, res, next) => {
  try {
    const pets = await Pet.list();      // same call
    res.json(pets);
  } catch (err) {
    next(err);
  }
};

const getPet = async (req, res, next) => {
  try {
    const pet = await Pet.find(req.params.id);  // same call
    if (!pet) return res.status(404).json({ error: 'Not found' });
    res.json(pet);
  } catch (err) {
    next(err);
  }
};
```

Your routes don't change either. Your frontend doesn't change. **Only the model changed.**

This is the entire point of MVC's separation of concerns. The controller doesn't care whether the data comes from an array or a database — it just calls the model and trusts the result.

{% hint style="info" %}
**Do the swap yourself.** Don't just watch the instructor demonstrate this. Take your in-memory Pet model and convert it to a Postgres model method by method. Each time you swap one method and your app still works, you feel the power of this architecture.
{% endhint %}

<details>

<summary><strong>Q: The controllers now need to <code>await</code> the model methods. Did you have to change the controller code when swapping models?</strong></summary>

If you wrote your controllers with `async/await` from the start (anticipating that the model might become async), then the controllers already had `await` before each model call — even when the model was synchronous. Calling `await` on a non-Promise just returns the value immediately, so it's harmless. This means your controllers didn't need to change at all during the swap.

If your original controllers weren't async (because the in-memory model was synchronous), then yes — you'd need to add `async` and `await` to the controllers too. But the routes still don't change.

</details>

<details>

<summary><strong>Q: After the swap, you restart the server, add some pets, restart again — and the pets are still there. What's different about where the data lives?</strong></summary>

With the in-memory model, data lived in a JavaScript array inside your Node process. When the process stopped, the array was destroyed.

With the Postgres model, data lives in a Postgres database — a separate process with its own storage that persists to disk. Restarting your Node server has no effect on the database. The data lives in Postgres until you explicitly delete it with a `DELETE` statement.

</details>

## Express Error Handling Middleware

When a controller calls `next(err)`, Express looks for a middleware with four parameters. This is the **error handling middleware**:

```js
// index.js — add this AFTER all routes
const handleError = (err, req, res, next) => {
  console.error(err);

  const status = err.status ?? 500;
  const message = err.message ?? 'Internal Server Error';

  res.status(status).json({ error: message });
};

app.use(handleError);
```

This one function handles all errors from all controllers. When a database query fails, the error bubbles up through `next(err)` to here, where it's logged and a consistent JSON error response is sent.

<details>

<summary><strong>Q: Why must error handling middleware have exactly four parameters <code>(err, req, res, next)</code>? What happens if you only write three?</strong></summary>

Express identifies error handling middleware by its signature — specifically, by the presence of exactly four parameters. If you write a function with three parameters, Express treats it as a normal middleware function, not an error handler. When `next(err)` is called, Express would skip it entirely and the error would not be caught. Always include all four parameters, even if you don't use `next` in the body.

</details>
