# Database Migrations and Seeds

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-3-2-migrations-seeds)!
{% endhint %}

Thus far, we've been executing individual SQL queries to set up the structure of our tables and to modify the data inside. This is fine when you're learning and experimenting, however, when working with a team on a shared database, we'll need a more robust approach.

This is where migration and seed files come into play. 

Migration and seed files are used to automate the process of structuring a database and populating it with data. They provide a few key benefits:
* Unlike SQL statements that are executed manually and one at a time, migration and seed files define all of the operations to be executed at once with a single node command.
* Since they are files, they can be included in a Git repository and are easily shared and maintained by a team.
* A new developer on the team just needs to execute the contents of migration and seed files to get started.


**Table of Contents**

- [Terms](#terms)
- [Commands](#commands)
- [Setup](#setup)
- [Migrations and Seeds: Why do we need them?](#migrations-and-seeds-why-do-we-need-them)
- [Migrations](#migrations)
  - [Creating New Migrations](#creating-new-migrations)
  - [Updating Migrations](#updating-migrations)
- [Seeds](#seeds)
  - [Creating Seed Files](#creating-seed-files)
- [Migration Example Scenarios](#migration-example-scenarios)

## Terms

* **Migration File** - a file defining a change to the structure of your database (creating, updating, deleting tables)
* **Seed File** - a file for inserting an initial dataset into a database
* **Rollback** - the act of reverting a system to a previous state 

## Commands

**Migrations Commands**:
* `npx knex migrate:make migration_name` - create an update to your schema
* `npx knex migrate:down` - rewind/undo the last migration
* `npx knex migrate:rollback` - rewind/undo your migrations
* `npx knex migrate:latest` - run your migrations

**Seeds Commands**:
- `npx knex seed:make seed_name` - create a new seed file
- `npx knex seed:run` - run all seed files

[Knex Docs](http://knexjs.org)

## Setup

In order to experiment with migration and seed files, you will need a database. Do the following to set up a database to practice with:

* Create a database called `migrations_seeds_practice`
* Create a new folder called `migrations-practice` and `cd` into it
* Install dependencies: `npm i knex pg`
* Create a `knexfile.js` file: `npx knex init`
* Modify the `knexfile.js` to use your `migrations_seeds_practice` database

```js
  development: {
    client: 'pg',
    connection: {
      user: 'postgres', // unless you want to use a different user
      password: 'postgres', // unless you changed your password
      database: 'migrations_seeds_practice', 
    }
  },
```

* Create your first migration file: `npx knex migrate:make init`
* Read about migrations below!

## Migrations and Seeds: Why do we need them?

When working on a team, everyone must be on the same page about the structure of the database. However, this can be challenging when developers (typically) test their code on databases running on their local machines.

Imagine this:
- You're working on a team of developers that share a code-base.
- You all push and pull from the same remote Github repo.
- While working, you modify the `users` table in your local Postgres database and add a `bio` property.
- You finish building out your bio feature and push your new code up! 
- Your teammates pull down the code but their local databases don't have the `bio` property, so the app crashes!

Each developer needs to keep their local database up to date with the latest database design (A.K.A. "database schema"). Migrations will make this so much easier!

## Migrations

Migration files standardize the way a given database should be configured. Since they can be stored in Git repositories, they ensure that an entire team can reliably have the same database schema and make it easy for new developers on the team to get started.

There are many ways to implement migration files but we will be using [Knex](https://knexjs.org/) which makes the process much easier. Here is what a migration file made using Knex looks like:

```js
exports.up = function (knex) {
  return knex.schema
    .createTable('fellows', function (table) {
      table.increments('id').primary();
      table.string('name', 255).notNullable();
    })
    .createTable('posts', function (table) {
      table.increments('id').primary();
      table.string('content').notNullable();
      table.integer('fellow_id').notNullable();
      table.foreign('fellow_id').references('id').inTable('fellows');
    });
};

exports.down = function (knex) {
  return knex.schema.dropTable('posts').dropTable('fellows');
};
```

Can you already tell what it does? Let's dive into it.

### Creating New Migrations

A new migration file should be created any time that you want to make a lasting change to your database structure. Run the following command to create a new migration file:

```sh
npx knex migrate:make "short_description"
```

The generated file will look like this: `20250804202350_short_description.js`

* The `short_description` part is the chosen name of the migration file. 
* The long number before it is a timestamp in the format `yyyymmddhhmmss`.

Every migration file will export two functions: `up` and `down`:

```js
exports.up = function(knex) {
  // make changes to your database such as adding new tables,
  // updating existing tables, deleting tables, etc...
};

exports.down = function(knex) {
  // undo the changes made above. this lets you execute a "rollback"
  // if you ever want to undo something
};
```

- The `up` function defines the changes you want to make to your database schema including adding, updating, or deleting tables
- The `down` function defines how to undo those changes should you ever want to perform a "rollback".

```js
exports.up = function (knex) {
  return knex.schema
    .createTable('fellows', function (table) {
      table.increments('id').primary();
      table.string('name', 255).notNullable();
    })
    .createTable('posts', function (table) {
      table.increments('id').primary();
      table.string('content').notNullable();
      table.integer('fellow_id').notNullable();
      table.foreign('fellow_id').references('id').inTable('fellows');
    });
};

exports.down = function (knex) {
  return knex.schema.dropTable('posts').dropTable('fellows');
};
```

To run your migrations (and execute the `up` function): `npx knex migrate:latest` 
* At this point, if you view your database, you'll see it has one table: `fellows`.
* Notice the line of code `table.foreign('fellow_id').references('id').inTable('fellows');`
* This is creating a foreign key reference. We will not be allowed to have a post unless it has a reference to a record in the `fellows` table.

To undo your migrations (and execute the `down` function): `npx knex migrate:rollback`
* This will delete the tables `posts` and `fellows`.

Run the `npx knex migrate:latest` command again to recreate the tables.

### Updating Migrations

Often, you will need to make changes to your database schema in the middle of a project. Suppose I need to change the column name of `posts.content` to `posts.post_content` or I need to add a new column to a table.

You *could* rollback, edit the original migration file, and then run the migration again, but rolling back a migration drops the table meaning all data in the table is lost.

The best practice to preserve the data is to create a new migration file: `npx knex migrate:make change_post_content_column`:

```js
exports.up = function (knex) {
  return knex.schema.alterTable('posts', function (table) {
    table.dropColumn('content');
    table.string('post_content');
  });
};

// notice how the `down` function undoes the `up` function's changes
exports.down = function (knex) {
  knex.schema.alterTable('posts', function (table) {
    table.dropColumn('post_content');
    table.string('content');
  });
};
```

## Seeds

When building out your backend, it will be helpful if the database had some data to test with. Instead of manually inserting data using SQL `INSERT` statements, we can create a **seed file**.

All a seed file really does is populate a database with some starter data.

### Creating Seed Files

To create a seed file, run the command:

```sh
npx knex seed:make "seed_filename"
```

This would make `seed_filename.js` in the designated seed file. To start, here's what that file would look like:

```js
exports.seed = async function(knex) {
  // Deletes ALL existing entries
  await knex('table_name').del()
  await knex('table_name').insert([
    {colName: 'value', otherColName: 'value'},
    {colName: 'value', otherColName: 'value'},
    {colName: 'value', otherColName: 'value'}
  ]);
};
```

Let's replace the provided code with our own:

```js
exports.seed = async function (knex) {
  // Deletes ALL existing entries
  await knex('posts').del()
  await knex('fellows').del()

  // Reset the auto increment so ids start back at 1
  await knex.raw('ALTER SEQUENCE posts_id_seq RESTART WITH 1')
  await knex.raw('ALTER SEQUENCE fellows_id_seq RESTART WITH 1')

  // Use the knex query builder methods to insert fellow data
  await knex('fellows').insert([
    { name: 'maya' },
    { name: 'reuben' },
    { name: 'ann' }
  ]);

  // insert the array of post data
  // await knex('posts').insert(postData);
  await knex('posts').insert([
    { post_content: `hello world i am maya`, fellow_id: 1 },
    { post_content: `hello world i am reuben`, fellow_id: 2 },
    { post_content: `hello world i am ann`, fellow_id: 3 },
  ])
};
```

* We first delete `posts` because they reference `fellows` (what would happen if we did it the other way?)
* We then set the auto increments back to 0. If we were to re-seed the database, the id values of each record would pick up from the last seed (in this example, `fellow.id` would start at `4`)
* Then we can insert data using an Array of Objects

Finally, run `npx knex seed:run` to seed your database with some starter data. Check your database now and you should see your tables populated with some rows.

Updating a seed file doesn't have the same consequences as a migration file. We can easily just edit our seed files and run them again when we need to re-seed.

## Migration Example Scenarios

These example illustrate when migrations might be needed in real-world scenarios

<details><summary>New Feature</summary>

One example of when database migration might occur is when a software application is updated or a new feature is added that requires changes to the database schema. For instance, if an e-commerce website adds a new payment method, the database may need to be updated to include a new table for storing payment information. This change would require a database migration to ensure that the new table is created, any necessary data is migrated to the new table, and the application is able to access the new table and use the new payment method. In such cases, a migration tool would be used to apply the changes to the database schema and ensure that the data remains consistent and accurate.
</details>

<details><summary>Changing Infrastructure</summary>

Another example of when database migration might occur is when a company decides to change the structure of its database to better organize data and make it more efficient to query. For instance, if a company has been using a single large table to store all customer data and decides to split it into separate tables for customers, orders, and payments, this change would require a database migration. The migration process would involve creating new tables, updating existing tables, and moving data from the old table to the new tables in a way that preserves data integrity and consistency. This type of migration could improve database performance, simplify queries, and make it easier to maintain the database over time.
</details>

<details><summary>New Service</summary>

Another example of when database migration might occur is when a company decides to move its database to a new server or cloud-based platform. In this case, the migration process would involve exporting the data from the existing database, transferring it to the new server or platform, and then importing it into the new database. This could involve changes to the database schema, such as updating database connection settings or configuring security settings for the new server or platform. The migration process would need to be carefully planned and executed to ensure that the data remains intact and that there is no loss of data during the transfer. This type of migration is common when a company wants to take advantage of newer or more powerful hardware, or to move its data to a more secure or reliable platform.
</details>

<details><summary>Merging Database</summary>

Another example of when database migration might occur is when a company decides to merge two separate databases into a single unified database. This could occur when two companies merge or when a company acquires another company and needs to combine their databases. The migration process would involve extracting data from both databases, transforming it to ensure consistency and compatibility, and then loading it into the new database. This type of migration is complex and requires a detailed understanding of both databases and their data structures, as well as careful planning and execution to avoid data loss or corruption. The end result is a unified database that allows the company to access and manage all data in a single location.
</details>
