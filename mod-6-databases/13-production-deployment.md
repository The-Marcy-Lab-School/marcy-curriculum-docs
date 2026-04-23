# 13. Production Deployment

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-13-production-deployment)!

Checkout the `solution` branch to see the final version and practice deploying!

```sh
git checkout solution
```
{% endhint %}

Your app works locally. This lesson covers what it takes to make it work for anyone, anywhere — deploying your Express server and Postgres database to the internet using [Render](https://render.com).

Before you can deploy safely, you need to move every secret and environment-specific value out of your code and into environment variables. That's the first half of this lesson. The second half walks through creating a Render Postgres database and a Render web service, and wiring the two together with those environment variables.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Environment Variables](#environment-variables)
  - [What Goes in `.env`](#what-goes-in-env)
  - [What Is `PG_CONNECTION_STRING`?](#what-is-pg_connection_string)
- [Updating Server Code To Use Environment Variables](#updating-server-code-to-use-environment-variables)
  - [dotenv](#dotenv)
  - [Using `process.env` Values in `pool.js` to Configure `pg.Pool`](#using-processenv-values-in-pooljs-to-configure-pgpool)
  - [Using `process.env` Values in `index.js` to Configure `cookieSession`](#using-processenv-values-in-indexjs-to-configure-cookiesession)
  - [The `.env.template` Pattern](#the-envtemplate-pattern)
- [Deploying to Render](#deploying-to-render)
  - [Step 1: Create a Postgres Database](#step-1-create-a-postgres-database)
  - [Step 2: Deploy the Web Service](#step-2-deploy-the-web-service)
  - [Step 3: Seed the Production Database](#step-3-seed-the-production-database)
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

## Environment Variables

The app as written in lesson 12 has two values hardcoded directly in the source code:

```js
// server/db/pool.js
const config = {
  host: 'localhost',
  port: 5432,
  database: 'users_db',
};
```

```js
// server/index.js
secret: 'dev-only-secret-replace-before-deploying',
```


Neither of these will work in production:
* The database config points at your local machine. While Render can host both a database server and an Express server, it is unlikely that they will run on the same hardware.
* The session secret must never be shared. Publishing it in a GitHub repository would allow malicious users to forge fake cookies and impersonate users.

The solution is environment variables: values that live outside the code, set differently per environment (your laptop vs. a Render server), and never committed to git.

### What Goes in `.env`

Any value that:
- Is a secret (password, API key, session signing secret)
- Varies between environments (development database configurations vs. production database configurations)
- Should never be visible in the codebase

In this app, that means having a `.env` file with the following:

```sh
# Generate your own session secret
SESSION_SECRET=bbd40cc09435a51129a424042d78afaf79ec202be2310b85e51bbec230d7b1ee

PGHOST=localhost
PGPORT=5432
PGDATABASE=users_db
PGUSER=
PGPASSWORD=

# When deploying, set this to your hosted database URL instead of the vars above
PG_CONNECTION_STRING=
```

{% hint style="warning" %}
`.env` must be in `.gitignore`. Never commit it. If you accidentally commit secrets, rotate them immediately — deleting the file doesn't remove it from git history.
{% endhint %}

To generate a `SESSION_SECRET` value, we can use a website like [https://randomkeygen.com/](https://randomkeygen.com/) or just enter the command below into your terminal:

```sh
openssl rand -hex 32
# → bbd40cc09435a51129a424042d78afaf79ec202be2310b85e51bbec230d7b1ee
```

This will generate a 32 random bytes and converts them into a 64-character hexadecimal string.

### What Is `PG_CONNECTION_STRING`?

Connecting to a Postgres database requires several pieces of information: a host, a port, a database name, a username, and a password. You can supply these as separate values, or you can combine them into a single **connection string** — a URL that encodes all of them:

```
postgres://username:password@hostname:5432/database_name
```

Render provides your hosted database's credentials in this format (called the **Internal Database URL**). Rather than splitting the URL back into individual parts, you can pass the whole string directly to `pg.Pool`.

The `.env` file supports both approaches. During local development, the individual `PGHOST`, `PGDATABASE`, etc. variables point at your local Postgres instance. For production — or any time you have a connection string — you set `PG_CONNECTION_STRING` instead, and the individual variables are ignored.

## Updating Server Code To Use Environment Variables

### dotenv

Open up `pool.js`, which we will want to update to use our environment variables. To access environment variables in a Node application, we use the `process.env` object. Print it out to see what is inside of `process.env`:

{% code title="server/db/pool.js" %}
```js
console.log(process.env);
```
{% endcode %}

Run the seed file which uses `pool.js` and you should see the environment variables printed to the console:

```sh
node db/seed.js
```

You'll notice that our environment variables `PGHOST`, `PGPORT`, etc. are not there!

Having the `.env` file will not automatically add our environment variables to `process.env`. We need a tool like `dotenv` to do that for us:

```sh
npm install dotenv
```

Now, at the top of `pool.js`, add the following code to use dotenv:

{% code title="server/db/pool.js" %}
```js
// 1. Import dotenv and run config(). Must be called before any process.env references
require('dotenv').config(); 
```
{% endcode %}

This one line imports `dotenv` and then immediately invokes the `config()` method which looks for any `.env` files and loads them into `process.env`. If you run the seed file again you should see that our environment variables appear!

### Using `process.env` Values in `pool.js` to Configure `pg.Pool`

Now, we can update `pool.js` to set up the `pg` connection configuration to read from `process.env` instead of using hardcoded values:

{% tabs %}

{% tab title="With Environment Variables (New)" %}

{% code title="server/db/pool.js" %}
```js
// 1. Import dotenv and run config(). Must be called before any process.env references
require('dotenv').config(); 
const { Pool } = require('pg');

// 2. Replace hard-coded values with `process.env`
const devConfig = {
  host: process.env.PGHOST,
  port: process.env.PGPORT,
  user: process.env.PGUSER,
  password: process.env.PGPASSWORD,
  database: process.env.PGDATABASE,
};

// 3. Create this separate config for production environments where we'll have a connection string
const prodConfig = {
  connectionString: process.env.PG_CONNECTION_STRING,
  ssl: { rejectUnauthorized: false },
}

// 4. Use PG_CONNECTION_STRING if available, otherwise use individual credentials.
const pool = new Pool(process.env.PG_CONNECTION_STRING ? prodConfig : devConfig);

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

The conditional `process.env.PG_CONNECTION_STRING ? prodConfig : devConfig` means:
- **In production**: Render sets `PG_CONNECTION_STRING` to the hosted database's Internal URL. `pg.Pool` uses that connection string and ignores the individual variables.
- **In development**: `PG_CONNECTION_STRING` is empty in `.env`, so `pg.Pool` uses the individual `PGHOST`, `PGDATABASE`, etc. values that point at your local database.

This lets you use the same `pool.js` in both environments with no code changes — just different environment variable values.

{% hint style="info" %}
The `ssl` option in the production configuration is only needed for the production connection string path — your local Postgres does not require SSL.
{% endhint %}

### Using `process.env` Values in `index.js` to Configure `cookieSession`

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
// ⚠️ secret is hardcoded for development only — move to .env before deploying
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

`.env` is gitignored — so how do teammates know which variables to set? What would happen if they set `PG_HOST` instead of `PGHOST`?

The solution is `.env.template`: a committed file with all the variable names and empty (or example) values.

{% code title="server/.env.template" %}
```
SESSION_SECRET=''

PGHOST=localhost
PGPORT=5432
PGDATABASE=users_db
PGUSER=
PGPASSWORD=

# When testing a production database, set this to your hosted database URL instead of the vars above
PG_CONNECTION_STRING=
```
{% endcode %}

When a new developer clones the repo, their first step is to make a copy of that file called `.env`:

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
3. Give it a name (e.g. `users_db`)
4. Choose the **Free** tier
5. Click **Create Database**

Once created, Render shows you a **Connection** panel with individual fields (host, port, database, username, password) and an **Internal Database URL** — a single connection string that encodes all of them. Copy the **Internal Database URL**; you'll need it in the next step.

{% hint style="info" %}
Use the **Internal** URL (not External) when connecting from another Render service. Internal URLs route traffic within Render's private network, which is faster and doesn't count against bandwidth limits.

You can also connect to the Render-hosted Postgres database in your local development environment by adding the internal database URL to your `.env` file as `PG_CONNECTION_STRING`.
{% endhint %}

### Step 2: Deploy the Web Service

1. Click **New +** → **Web Service**
2. Connect your GitHub repository
3. Set the **Root Directory** to `server`
4. Set the **Build Command** to `npm install`
5. Set the **Start Command** to `node index.js` (or `npm start` if you have that script)
6. Choose the **Free** tier
7. Set **Environment Variables**

| Key                    | Value                                                                                                                       |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `SESSION_SECRET`       | A long random string (e.g. generate one with `openssl rand -hex 32` in your terminal or using Render's **generate** button) |
| `PG_CONNECTION_STRING` | The Internal Database URL from Step 1                                                                                       |

8. Click **Create Web Service** — don't add environment variables yet

After saving, Render will deploy and connect to your database!

### Step 3: Seed the Production Database

Your production database is empty. You need to run the seed script against it once to create the tables.

The easiest way is to temporarily set `PG_CONNECTION_STRING` in your local `.env` to the **External** Database URL (for local → Render connections), and run the seed script:

```sh
# In server/.env, temporarily set:
PG_CONNECTION_STRING=<External Database URL from Render>

# Then run:
cd server
node db/seed.js

# Remove or clear PG_CONNECTION_STRING from .env when done
```

Alternatively, Render provides a **Shell** tab in the web service dashboard where you can run commands directly on the deployed server:

```sh
node db/seed.js
```

After seeding, visit your web service's public URL — the app should be fully functional.

## The Complete Picture

After deployment, the same app runs in two environments simultaneously:

|                    | Development                  | Production                             |
| ------------------ | ---------------------------- | -------------------------------------- |
| **Server**         | `localhost:8080`             | Render Web Service                     |
| **Database**       | Local Postgres               | Render Postgres                        |
| **Secrets**        | `server/.env` (gitignored)   | Render Environment Variables           |
| **Session secret** | Value from `.env`            | Value from Render env                  |
| **DB connection**  | `localhost` config in `.env` | `PG_CONNECTION_STRING` from Render env |

The code is identical in both environments. The only difference is *where the environment variables come from* — your local `.env` file in development, Render's environment panel in production. That's the entire purpose of environment variables: one codebase, multiple environments, no secrets in git.
