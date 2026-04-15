# 13. Production Deployment

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-12-putting-it-all-together)!
{% endhint %}

Your app works locally. This lesson covers what it takes to make it work for anyone, anywhere — deploying your Express server and Postgres database to the internet using [Render](https://render.com).

Before you can deploy safely, you need to move every secret and environment-specific value out of your code and into environment variables. That's the first half of this lesson. The second half walks through creating a Render Postgres database and a Render web service, and wiring the two together with those environment variables.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Hardening for Deployment: Environment Variables](#hardening-for-deployment-environment-variables)
  - [What Goes in `.env`](#what-goes-in-env)
  - [Updating `pool.js`](#updating-pooljs)
  - [Updating `index.js`](#updating-indexjs)
  - [The `.env.template` Pattern](#the-envtemplate-pattern)
- [Deploying to Render](#deploying-to-render)
  - [Step 1: Create a Postgres Database](#step-1-create-a-postgres-database)
  - [Step 2: Deploy the Web Service](#step-2-deploy-the-web-service)
  - [Step 3: Set Environment Variables on Render](#step-3-set-environment-variables-on-render)
  - [Step 4: Seed the Production Database](#step-4-seed-the-production-database)
- [The Complete Picture](#the-complete-picture)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What sensitive values must never be committed to GitHub? Where should they live instead?
2. How does moving database credentials to `.env` change `pool.js`?
3. What is the difference between `.env` and `.env.template`? Why commit one but not the other?
4. What two Render services does a full-stack app with a database require?
5. How does the deployed web service know how to connect to the production database?

## Key Concepts

* **Environment variable** — a variable set outside the application code, typically in a `.env` file. Used to store secrets and configuration that varies between environments (development, production).
* **`.env`** — the file that holds your actual secrets. Never committed to version control.
* **`.env.template`** — a committed file that documents which environment variables the app needs, with empty values. New developers copy this and fill in their own values.
* **`dotenv`** — the npm package that reads `.env` and loads its values into `process.env`.
* **Render** — a cloud platform for deploying web services and hosted databases.
* **Connection string** — a single URL that encodes all the information needed to connect to a database: host, port, database name, user, and password.

## Hardening for Deployment: Environment Variables

The app as written in lesson 12 has two values hardcoded directly in the source code:

```js
// server/index.js
secret: 'dev-only-secret-replace-before-deploying',
```

```js
// server/db/pool.js
const config = {
  host: 'localhost',
  port: 5432,
  database: 'users_db',
};
```

Neither of these will work in production. The session secret is a placeholder, and the database config points at your local machine. If you pushed this code to GitHub as-is, anyone reading your repo would see your secrets — and the app would fail the moment it tried to connect to a database that doesn't exist on the production server.

The solution is environment variables: values that live outside the code, set differently per environment (your laptop vs. a Render server), and never committed to git.

### What Goes in `.env`

Any value that:
- Is a secret (password, API key, session signing secret)
- Varies between environments (dev database vs. production database)
- Should never be visible in the codebase

In this app, that means:

```
SESSION_SECRET=some-long-random-string

PGHOST=localhost
PGPORT=5432
PGDATABASE=users_db
PGUSER=
PGPASSWORD=
```

{% hint style="warning" %}
`.env` must be in `.gitignore`. Never commit it. If you accidentally commit secrets, rotate them immediately — deleting the file doesn't remove it from git history.
{% endhint %}

### Updating `pool.js`

With those values in `.env`, the `pg` connection config reads from `process.env` instead of hardcoded values:

{% tabs %}

{% tab title="With Environment Variables (New)" %}

{% code title="server/db/pool.js" %}
```js
const { Pool } = require('pg');

// Values come from .env via dotenv — never hardcode credentials
const pool = new Pool({
  host:     process.env.PGHOST,
  port:     process.env.PGPORT,
  database: process.env.PGDATABASE,
  user:     process.env.PGUSER,
  password: process.env.PGPASSWORD,
});

module.exports = pool;
```
{% endcode %}

{% endtab %}

{% tab title="Hardcoded (Old)" %}

```js
const { Pool } = require('pg');

const config = {
  host: 'localhost',
  port: 5432,
  database: 'users_db',
  // user: 'username',
  // password: 'password',
};

const pool = new Pool(config);
module.exports = pool;
```

{% endtab %}

{% endtabs %}

### Updating `index.js`

Replace the hardcoded session secret and add `require('dotenv').config()` at the top so the `.env` values are loaded into `process.env` before anything else reads them:

{% tabs %}

{% tab title="With Environment Variables (New)" %}

{% code title="server/index.js" %}
```js
require('dotenv').config(); // must be called before any process.env reads

// ...

app.use(cookieSession({
  name: 'session',
  secret: process.env.SESSION_SECRET,
  maxAge: 24 * 60 * 60 * 1000,
}));
```
{% endcode %}

{% endtab %}

{% tab title="Hardcoded (Old)" %}

```js
app.use(cookieSession({
  name: 'session',
  secret: 'dev-only-secret-replace-before-deploying',
  maxAge: 24 * 60 * 60 * 1000,
}));
```

{% endtab %}

{% endtabs %}

`dotenv` is already a dependency in `package.json`. Calling `require('dotenv').config()` at the very top of `index.js` reads the `.env` file and loads every variable into `process.env` before any other code runs.

### The `.env.template` Pattern

`.env` is gitignored — so how do teammates know which variables to set? The answer is `.env.template`: a committed file with all the variable names and empty (or example) values.

{% code title="server/.env.template" %}
```
SESSION_SECRET=''

PGHOST=localhost
PGPORT=5432
PGDATABASE=users_db
PGUSER=
PGPASSWORD=
```
{% endcode %}

When a new developer clones the repo, their first step is:

```sh
cp server/.env.template server/.env
# then fill in the real values
```

This makes setup self-documenting: the template is always up to date because it's in version control, and no real secrets are ever committed.

Test that your app still works locally after these changes before moving on to deployment.

## Deploying to Render

Render hosts both web services (your Express app) and managed Postgres databases. A full-stack app needs both, and you'll create them in that order: database first, web service second.

### Step 1: Create a Postgres Database

1. Go to [render.com](https://render.com) and sign in
2. Click **New +** → **PostgreSQL**
3. Give it a name (e.g. `users-db`)
4. Choose the **Free** tier
5. Click **Create Database**

Once created, Render shows you a **Connection** panel with individual fields (host, port, database, username, password) and an **Internal Database URL** — a single connection string that encodes all of them. Copy the **Internal Database URL**; you'll need it in the next step.

{% hint style="info" %}
Use the **Internal** URL (not External) when connecting from another Render service. Internal URLs route traffic within Render's private network, which is faster and doesn't count against bandwidth limits.
{% endhint %}

### Step 2: Deploy the Web Service

1. Click **New +** → **Web Service**
2. Connect your GitHub repository
3. Set the **Root Directory** to `server`
4. Set the **Build Command** to `npm install`
5. Set the **Start Command** to `node index.js` (or `npm start` if you have that script)
6. Choose the **Free** tier
7. Click **Create Web Service** — don't add environment variables yet

Render will attempt a first deploy. It will fail because the environment variables aren't set yet. That's expected — you'll fix it in the next step.

### Step 3: Set Environment Variables on Render

In your web service dashboard, go to **Environment** and add the following key-value pairs:

| Key | Value |
| --- | ----- |
| `SESSION_SECRET` | A long random string (e.g. generate one with `openssl rand -base64 32` in your terminal) |
| `DATABASE_URL` | The Internal Database URL from Step 1 |

{% hint style="info" %}
Rather than setting individual `PGHOST`, `PGPORT` etc. variables, it's common in production to use a single `DATABASE_URL` connection string. The `pg` library supports this directly — update `pool.js` to use it:

```js
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: process.env.NODE_ENV === 'production' ? { rejectUnauthorized: false } : false,
});
```

The `ssl` option is required when connecting to Render's hosted Postgres from a Render web service.
{% endhint %}

After saving, Render will trigger a new deploy. This one should succeed.

### Step 4: Seed the Production Database

Your production database is empty. You need to run the seed script against it once to create the tables.

The easiest way is to temporarily set the `DATABASE_URL` in your local `.env`, point it at the **External** Database URL (for local → Render connections), and run the seed script:

```sh
# In server/.env, temporarily set:
DATABASE_URL=<External Database URL from Render>

# Then run:
cd server
node db/seed.js

# Remove DATABASE_URL from .env when done
```

Alternatively, Render provides a **Shell** tab in the web service dashboard where you can run commands directly on the deployed server:

```sh
node db/seed.js
```

After seeding, visit your web service's public URL — the app should be fully functional.

## The Complete Picture

After deployment, the same app runs in two environments simultaneously:

| | Development | Production |
| --- | --- | --- |
| **Server** | `localhost:8080` | Render Web Service |
| **Database** | Local Postgres | Render Postgres |
| **Secrets** | `server/.env` (gitignored) | Render Environment Variables |
| **Session secret** | Value from `.env` | Value from Render env |
| **DB connection** | `localhost` config in `.env` | `DATABASE_URL` from Render env |

The code is identical in both environments. The only difference is *where the environment variables come from* — your local `.env` file in development, Render's environment panel in production. That's the entire purpose of environment variables: one codebase, multiple environments, no secrets in git.
