# 9. Your First Fullstack App!

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-1-0-express-rest-api-model)!
{% endhint %}

**Table of Contents:**
- [Part 1 - Adding a DB for the fellows](#part-1---adding-a-db-for-the-fellows)
- [Part 2 — Refactor!](#part-2--refactor)
- [Part 3 - Adding posts](#part-3---adding-posts)

Remember, the **model** is the layer of the application that directly manipulates the data. It provides methods that the controllers can use to execute those changes whenever the frontend makes the appropriate request.

![Thus far, our server application used an in-memory array as a "database".](./img/8-model-view-controller/express-middleware-model.svg)

Thus far, our server application used an in-memory array as a "database". But this data is not persistent — each time we restart the server, all data created during that running session is lost.

Now that we have a database and can connect to it using Knex, we can refactor our model to use that database and have our data persist.

## Part 1 - Adding a DB for the fellows

Open the repository and start with the `2-fellow-tracker-final-mvc/` version of the project.

We need to create a database and then replace all logic associated with the in-memory `fellows` array used in the `server/model/Fellow` file with `knex` code to connect with our new database.

1. Start your Postgres server and create a database called `fellows_tracker` using TablePlus or the `psql` CLI.
2.  Execute the SQL statement below to create the table structure

    ```sql
    CREATE TABLE fellows (
      id SERIAL PRIMARY KEY,
      name TEXT NOT NULL
    )
    ```
3.  Return to the repository and install `pg` and `knex` into the server directory. Then initialize `knexfile.js`:

    ```sh
    cd server
    npm i pg knex # install pg and knex
    npx knex init # create knexfile.js
    ```
4.  Modify `knexfile.js` to use `pg` and the `fellow-tracker` database with a valid username and password:

    ```js
    development: {
      client: 'pg',
      connection: {
        user: 'postgres',
        password: 'postgres',
        database: 'fellow-tracker', 
      }
    },
    ```
5.  Create a `knex.js` file in the `model/` directory that exports a `knex` object. Remember, this object will be shared by every model in our application to execute SQL statements to the database.

    * make sure the import statement for `knexfile.js` is accurate

    ```js
    // Set the deployment environment variable
    // Depending on where it is deployed, this could be "staging" or "production"
    const env = process.env.NODE_ENV || 'development';

    // Grab the corresponding knex configuration object from knexfile.js
    const knexConfig = require('../knexfile.js')[env];

    // Create the knex connection object using that config
    const knex = require('knex')(knexConfig);

    module.exports = knex;
    ```

## Part 2 — Refactor!

Knex is configured to connect to our database! Now we can execute SQL commands to the database using the `knex.raw()` method.

Head over to the `server/model/Fellow.js` file and remove all logic associated with the in-memory `fellows` array, replacing it with the appropriate `knex` and SQL logic.

Remember, `knex.raw()` returns a promise that resolves to an object with a `rows` array property, even if there is only one value returned by the query!

```js
const getPets = async () => {
  // knex.raw returns a query result object
  let result = await knex.raw("SELECT * FROM pets");
  
  // .rows is an array containing the query data
  return result.rows;
};
```

Here's what you need to do:

1. Import `knex` into the `Fellow` model
2. Remove the import of the `getId` function, the database will handle automatically setting the `id` property
3. Remove the `fellows` array
4. All methods need to be `async` to use `await knex.raw()`
5. Every function needs to return _something_ (use `RETURNING *` on all non-`SELECT` queries)
   1. `Fellow.create` should return the new fellow
   2. `Fellow.list` should return an array of all fellows
   3. `Fellow.find` should return a single fellow object (the first object in the `rows` array)
   4. `Fellow.editName` should return the updated fellow object
   5. `Fellow.delete` should return the deleted fellow object
6. Refactor `fellowControllers.js` to `await` everything and double check that all values returned from the model are being handled properly.

## Part 3 - Adding posts

Here's where things get fun! We can add a `posts` table to our database that has a one-to-many relationship with our `fellows` table. Each post must have a `fellow_id` reference pointing to the fellow that created it.

1. Create a posts table that references the fellows table
2. Create a `Post` model with methods:
   * `create(content, fellowId)`
   * `findPostsByFellowId(fellowId)`
   * `delete(id)`
   * `deleteAllPostsForFellow(fellowId)`
   * `list()` — only used for testing
   * `findById(id)` — only used for testing
3. Make the `postControllers`
4. Define endpoints in `index.js`. Make sure to follow REST principles! Since every post is owned by a fellow, the endpoint should communicate that:
   * `POST /api/fellows/:fellowId/posts` → `Post.create`
   * `GET /api/fellows/:fellowId/posts` → `Post.findPostsByFellowId`
   * `DELETE /api/fellows/:fellowId/posts/:postId` → `Post.delete`
   * `DELETE /api/fellows/:fellowId/posts/` → `Post.deleteAllPostsForFellow`
5. Test with postman
6. Add frontend components and fetching on the `FellowDetails` page
   * You'll need the following components:
     * A form for adding a new post
     * A list of posts with delete buttons for each post
   * Event handlers / fetching:
     * `useEffect` now also fetches fellow posts -> `GET /api/fellows/:fellowId/posts`
     * new post form on submit -> `POST /api/fellows/:fellowId/posts/`
     * list item delete button on click -> `DELETE /api/posts/:id`
7. When deleting a fellow, we need to delete posts first. How can we ensure that this happens in the right order?
