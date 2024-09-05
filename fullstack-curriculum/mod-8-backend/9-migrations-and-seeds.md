# Database Migrations and Seeds

So far, we've been setting up our databases by hand, executing SQL queries using `psql` or TablePlus. While this works when working on a project by yourself, it doesn't scale well. You would need to send them the exact SQL statements you ran and the order that you ran them in. If you ever make any changes, you would need to share those changes too!

Migration files are a "formal" way to define a database schema and to update it over time. Let's learn!

## Terms

* **Migration File** - a file defining a change to the structure of your database (creating, updating, deleting tables)
* **Seed File** - a file for inserting an initial dataset into a database
* **Rollback** - the act of reverting a system to a previous state 

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

## Migrations & Seeds: Why do we need them?

The main reason you want to use migrations and seeds is for **maintainability**.

Imagine this:
- you're working on a team of developers that share a code-base. 
- You all push and pull from the same remote Github repo.
- Let's say you add a new feature that requires a database, so you create one on your local computer. 
- You finish building out your feature and push your new code up! 
- However, if your team members were to pull your code down, they would not get your database.

When working with other devs, they need to be able to reproduce your database structure and starting information. We could have everyone run the same SQL query to create the entire database manually on their computers. However, this does not scale, so in the real world it's much more common for companies to use **migration files** to keep track of their DB structure and **seed files** to populate their databases.

## Migrations
Migrations are special files that run queries on your DB to perform structural updates, or in some cases, data updates. In node land, one of the more common ways to do this is by using the query builder KNEX. This is a simple library that allows you to create and run migrations files with ease.

### Creating New Migrations

Running the command `npx knex migrate:make init` will generate a file like: `20240417181815_init` located **in your new `/migrations` folder**. 
* That bit at the front is a timecode that the migration uses to track what migrations exist. 
* Every time you want to create another migration: `npx knex migrate:make example_file`. 

Every migration file will look like this:

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

The file exports two functions: `up` and `down`. 
- The `up` function defines the changes you want to make to your database schema including adding, updating, or deleting tables
- The `down` function defines how to undo those changes in `up`

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
To undo your migrations (and execute the `down` function): `npx knex migrate:rollback`

We can add some scripts to our `package.json` to make things a little easier:

```json
"scripts": {
  "migrate:run" : "npx knex migrate:latest",
  "migrate:rollback" : "npx knex migrate:rollback",
}
```

At this point, if you view your database, you'll see it has one table: `fellows`.

Notice the line of code `table.foreign('fellow_id').references('id').inTable('fellows');`

This is creating a foreign key reference. We will not be allowed to have a post unless it has a reference to a record in the `fellows` table.

### Updating Migrations

Often, you will need to make changes to your database schema in the middle of a project. Suppose I want to change the column name of `posts.content` to `posts.post_content`.

You *could* rollback, edit the original migration file, and then run the migration again.

**Q: What issues could arise if you were to do this ^? Consider how you would communicate this change with other developers**

The best practice is to create a new migration file: `npx knex migrate:make change_post_content_column`:

```js
exports.up = async function (knex) {
  return knex.schema.alterTable('posts', function (table) {
    table.dropColumn('content');
    table.string('post_content');
  });
};

// notice how the `down` function undoes the `up` function's changes
exports.down = async function (knex) {
  knex.schema.alterTable('posts', function (table) {
    table.dropColumn('post_content');
    table.string('content');
  });
};
```

### Why do I need knex for migrations?
Technically you don't. But There's a reason companies use React instead of Vanilla js: there's no point in constantly reinventing the wheel. You *would* need to create a migration system, and it'd be a hell of a lot less battle-tested than Knex. So companies will likely use Knex or some other library with migration capabilities like an ORM like Sequelize.

So, if migrations build up our DB, how do we populate it?

## Seeds

A seed file is the easiest way to fill your DB up with starter data. All a seed file really does is clear the database of all existing data and repopulate it with starter data. 

### Creating Seed Files

`npx knex seed:make 01_seeds`, which would make `01_seeds.js` in the designated seed file. To start, here's what that file would look like:

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
