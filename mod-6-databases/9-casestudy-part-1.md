# 9. Case Study: Social Bookmark Manager (Part 1)

{% hint style="info" %}
Case study repo: [swe-casestudy-6-social-bookmark-manager](https://github.com/The-Marcy-Lab-School/swe-casestudy-6-social-bookmark-manager). Full investigation guide: [case-study.md](./case-study.md).
{% endhint %}

Over the past two weeks you've learned every piece of a Postgres-backed fullstack application: SQL queries, schema design, JOINs, the `pg` library, and Postgres-backed models in Express. Today you'll see all of those pieces working together in a complete application.

The **Social Bookmark Manager** is a fullstack app where users create accounts, share bookmarks, and like each other's links. Most of it will look familiar — `pool.js`, model methods, controllers, routes, Vanilla JS fetch calls. Some of it will be new: password hashing, sessions, and auth middleware. That's intentional. Seeing those pieces in a working app before you build them yourself gives you a concrete picture of where the next few lessons are headed.

**Table of Contents**

- [File and Folder Structure](#file-and-folder-structure)
- [The Build Order Rule](#the-build-order-rule)
- [The `seed.js` Pattern](#the-seedjs-pattern)
- [Today's Investigation](#todays-investigation)

## File and Folder Structure

The app is split into two top-level folders: `server/` and `frontend/`. This separation keeps backend and frontend concerns completely distinct.

```
server/
  db/
    pool.js         ← creates and exports the shared pg connection pool
    seed.js         ← drops, recreates, and seeds the database
  models/
    userModel.js    ← SQL queries for the users table
    bookmarkModel.js← SQL queries for the bookmarks table (includes JOINs)
  middleware/
    checkAuthentication.js ← auth guard: blocks unauthenticated requests
  controllers/
    authControllers.js     ← register, login, me, logout
    bookmarkControllers.js ← bookmark CRUD and likes
  index.js          ← Express app: middleware, routes, error handler
  .env              ← database credentials and session secret (gitignored)

frontend/
  index.html        ← single-page HTML structure
  src/
    main.js         ← page load logic and event handlers
    fetch-helpers.js← functions that call the API
    dom-helpers.js  ← functions that update the DOM
```

Notice the same patterns you've already learned:
- `db/pool.js` creates and exports the `Pool` — models `require()` it
- Models own the SQL — controllers never call `pool.query()` directly
- Controllers own the HTTP logic — models know nothing about `req` and `res`
- The frontend is completely separate from the server code

What's new: a `middleware/` folder for auth guards, and a `controllers/authControllers.js` that handles registration, login, and logout. You'll study both in detail.

## The Build Order Rule

When building or debugging a fullstack app, always work in this order:

```
database → model → controllers → frontend
```

Each layer depends on the one below it. If the database schema isn't right, the model queries will fail. If the model is broken, the controller can't get data to send. If the controller is wrong, the frontend fetch will get an unexpected response.

Starting at the database and verifying each layer before moving up means you always know exactly where a bug lives.

## The `seed.js` Pattern

Previous lessons used `.sql` files for seeding (`psql -f seed.sql`). This app uses a JavaScript seed script instead:

```sh
npm run db:seed # node db/seed.js
```

The `seed.js` script does the same things a `.sql` seed file does — drops tables, recreates the schema, and inserts sample rows — it just does it through `pool.query()` calls from Node instead of from the `psql` CLI.

So, why write it in JavaScript? A `.sql` file is plain text — it can run SQL statements but it can't call JavaScript functions. This app stores hashed passwords in the database, and hashing requires `bcrypt.hash()`, which is a JavaScript function. There is no SQL equivalent. Any seed data that requires runtime computation must live in a `.js` file.


## Today's Investigation

Clone the repo and open [the case study](./case-study.md). Work through the following sections:

1. **Setup** — create the database, fill in `.env`, run `npm run db:seed`, start the server
2. **Overview** — read the description of what the app does
3. **Schema** — study the three-table schema; trace the foreign key relationships in the ERD
4. **API Endpoints** — read the full endpoint table; note which endpoints require auth and which don't
5. **Scenario 1** — study the fully worked sequence diagram and detailed breakdown
6. **Scenario 2** — study the fully worked sequence diagram and detailed breakdown
7. **Guided Reading** — answer the questions for `server/db/pool.js`, `server/db/seed.js`, and `server/index.js`
