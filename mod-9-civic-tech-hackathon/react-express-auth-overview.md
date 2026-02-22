# React Express Auth Template Overview

{% hint style="info" %}
Follow along with the React Express + Auth Template repository [here](https://github.com/The-Marcy-Lab-School/react-express-auth)!
{% endhint %}

This repository is a full-stack starter for apps that need user accounts and authentication. It uses a React frontend, an Express server, and Postgres accessed through the `pg` package.

![The frontend, server, and database work together to form a full-stack application.](../.gitbook/assets/full-stack-diagram.svg)

**Table of Contents**

* [Application Overview](react-express-auth-overview.md#application-overview)
* [Getting Started](react-express-auth-overview.md#getting-started)
  * [Create your repo](react-express-auth-overview.md#create-your-repo)
  * [Getting to know the folder structure](react-express-auth-overview.md#getting-to-know-the-folder-structure)
  * [Configure your database and environment variables](react-express-auth-overview.md#configure-your-database-and-environment-variables)
  * [Kickstart the project](react-express-auth-overview.md#kickstart-the-project)
  * [Recommended Approach for Building](react-express-auth-overview.md#recommended-approach-for-building)
  * [Testing Along the Way](react-express-auth-overview.md#testing-along-the-way)
* [Database](react-express-auth-overview.md#database)
  * [Database Initialization](react-express-auth-overview.md#database-initialization)
  * [Database Access with `pg`](react-express-auth-overview.md#database-access-with-pg)
* [The Server Application](react-express-auth-overview.md#the-server-application)
  * [Server Overview](react-express-auth-overview.md#server-overview)
  * [User Model](react-express-auth-overview.md#user-model)
  * [Controllers and API endpoints](react-express-auth-overview.md#controllers-and-api-endpoints)
  * [Creating New Users: Securing Passwords with Bcrypt](react-express-auth-overview.md#creating-new-users-securing-passwords-with-bcrypt)
  * [Logging In: Authentication and Authorization](react-express-auth-overview.md#logging-in-authentication-and-authorization)
* [Front-end](react-express-auth-overview.md#front-end)
  * [Frontend Utils](react-express-auth-overview.md#frontend-utils)
  * [Adapters](react-express-auth-overview.md#adapters)
  * [Example Page Component](react-express-auth-overview.md#example-page-component)
  * [Current User Context](react-express-auth-overview.md#current-user-context)
* [Deploying](react-express-auth-overview.md#deploying)

## Application Overview

This template provides the auth foundation for applications that need users. The backend includes:

* A `users` table schema initializer (`server/db/init.js`)
* A `User` model (`server/models/User.js`) that runs SQL queries with `pg`
* Auth and user endpoints for registration, login, profile lookup, updates, and logout

The `users` table looks like this:

* `id` (primary key)
* `username` (unique)
* `password_hash` (bcrypt hash)
* `created_at`
* `updated_at`

With this functionality, users can:

* Register (create) a new user account with a username and password
* Log in using their credentials
* View a list of all users
* View a single user's page
* Update their username (and only their own)
* Log out

The frontend is intentionally minimal so teams can build on top of it.

## Getting Started

### Create your repo

* If you are working on a team, first make sure your team has a GitHub Organization for your project.
* Select <kbd>Use this template</kbd> and choose <kbd>Create a new repository</kbd>.
* Clone your repo and open it in your editor.

### Getting to know the folder structure

At the root:

* `frontend/` — React app
* `server/` — Express app + Postgres access

Important server directories/files:

* `server/index.js` — server entrypoint and route wiring
* `server/controllers/` — route handler logic
* `server/models/User.js` — database model methods
* `server/db/pool.js` — shared Postgres pool (`pg`)
* `server/db/init.js` — one-time schema initialization script

### Configure your database and environment variables

Before building features, create a local Postgres database and configure the server environment variables.

* Create a Postgres database with a name of your choice.
* In `server/`, create `.env` by copying `server/.env.template`.
* Update values to match your local Postgres setup.
* Replace `SESSION_SECRET` with your own random string.

Example:

```sh
# Replace these variables with your Postgres server information
# These values are used by the pg pool to connect to your postgres server
PG_HOST='127.0.0.1'
PG_PORT=5432
PG_USER='itsamemario'
PG_PASS='12345'
PG_DB='my_react_express_auth_database'

# Replace session secret with your own random string!
# This is used by handleCookieSessions to hash your cookie data
SESSION_SECRET='your-random-secret'

# When deploying, this can be used to connect directly to your deployed DB
PG_CONNECTION_STRING=''
```

How DB connection works:

* If `PG_CONNECTION_STRING` is present, `server/db/pool.js` uses it.
* Otherwise, the pool uses `PG_HOST`, `PG_PORT`, `PG_USER`, `PG_PASS`, and `PG_DB`.

### Kickstart the project

From the project root, run:

```sh
# install frontend dependencies and build static assets
cd frontend && npm i && npm run build

# return to root
cd ..

# install server dependencies and initialize database schema
cd server && npm i && npm run db:init
```

You can also run:

```sh
npm run kickstart
```

After initialization, start both apps in separate terminals:

```sh
# terminal 1
cd server && npm run dev

# terminal 2
cd frontend && npm run dev
```

### Recommended Approach for Building

Work from database -> model -> controllers -> frontend.

This minimizes blockers because frontend behavior depends on server routes, and server routes depend on database behavior.

### Testing Along the Way

* **Database schema:** run `npm run db:init`, then inspect tables with `psql`, TablePlus, or Postico.
* **Model:** call model methods from a scratch script and confirm DB state.
* **Controllers / Endpoints:** use Postman or Insomnia and verify DB changes.
* **Adapters / Components:** interact with the frontend and confirm API + DB behavior.

## Database

The project uses Postgres directly through `pg` (node-postgres). There is no Knex, and there are no migration/seed folders in this refactor.

### Database Initialization

`server/db/init.js` creates the `users` table if it does not exist.

Run it with:

```sh
npm run db:init
```

Core schema:

```sql
CREATE TABLE IF NOT EXISTS users (
  id SERIAL PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### Database Access with `pg`

`server/db/pool.js` creates a shared `Pool` from `pg`.

Model queries use parameterized SQL with `$1`, `$2`, etc:

```js
const query = 'SELECT * FROM users WHERE id = $1';
const result = await pool.query(query, [id]);
```

This approach keeps SQL injection protections while staying close to native SQL.

## The Server Application

### Server Overview

`server/index.js` wires middleware and routes:

* `cookie-session` for auth cookies
* `express.json()` for JSON request parsing
* auth routes under `/api/auth`
* user routes under `/api/users`
* static frontend serving from `frontend/dist`

### User Model

`server/models/User.js` encapsulates user data access:

* `User.create(username, password)`
* `User.list()`
* `User.find(id)`
* `User.findByUsername(username)`
* `User.update(id, username)`

The model constructor stores `password_hash` privately so user instances can be returned safely without exposing hashes.

### Controllers and API endpoints

Auth routes:

* `POST /api/auth/register`
* `POST /api/auth/login`
* `GET /api/auth/me`
* `DELETE /api/auth/logout`

User routes (protected):

* `GET /api/users`
* `GET /api/users/:id`
* `PATCH /api/users/:id`

### Creating New Users: Securing Passwords with Bcrypt

Before insertion, plain-text passwords are hashed using bcrypt (`SALT_ROUNDS = 12`). Only the hash is stored in `password_hash`.

### Logging In: Authentication and Authorization

Auth flow:

* Login verifies username + password.
* On success, `req.session.userId` is set.
* Protected routes use middleware to ensure a valid session.
* Update route authorization ensures users can only update their own profile.

## Front-end

### Frontend Utils

`frontend/src/utils/fetchingUtils.js` provides shared fetch behavior for API calls.

### Adapters

Adapters in `frontend/src/adapters/` isolate API request logic from components:

* `auth-adapter.js`
* `user-adapter.js`

### Example Page Component

Pages in `frontend/src/pages/` implement route-level UI:

* `Home.jsx`
* `Login.jsx`
* `SignUp.jsx`
* `Users.jsx`
* `User.jsx`

### Current User Context

`frontend/src/contexts/CurrentUserContextProvider.jsx` stores the authenticated user in app state and exposes it via context.

## Deploying

For deployment:

* Set production environment variables (`SESSION_SECRET`, `PG_CONNECTION_STRING`, etc.).
* Build frontend assets.
* Run `npm run db:init` against the target database at least once.
* Start the server with `npm start`.
