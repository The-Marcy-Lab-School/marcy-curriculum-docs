# 3. Creating Tables

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-3-creating-tables)!
{% endhint %}

So far you've been querying a database that was already set up for you. In this lesson, you'll create your own database and tables from scratch using `CREATE DATABASE` and `CREATE TABLE` and learn about the choices you can make when designing your database tables.

When you build an Express app with an in-memory model, the structure of your data is implied by your JavaScript objects — but nothing enforces it. If someone calls `User.create()` with a missing field, JavaScript might just create an object with `undefined` values and carry on. A database table is different: it's a **contract**. When you define a table, you're declaring that every record stored there will have exactly these fields, of exactly these types, following exactly these rules — and the database will reject anything that doesn't comply, no matter where it comes from.

This matters because your database is shared. Multiple server instances, admin scripts, background jobs, and future developers will all read and write to it. You can't rely on all of them to validate data the same way in code. Defining the structure in the database itself is the only guarantee that holds across all of them.

By the end of this lesson, you'll have designed and built a real database, inserted data into it, and queried it yourself.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Creating a Database](#creating-a-database)
- [CREATE TABLE](#create-table)
  - [Data Types](#data-types)
  - [Constraints](#constraints)
  - [The Primary Key Column](#the-primary-key-column)
- [Dropping and Recreating Tables](#dropping-and-recreating-tables)
- [Practice: Design and Build Your Own Database](#practice-design-and-build-your-own-database)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. How do you create a new database in Postgres?
2. What is the syntax for `CREATE TABLE`? What does each part do?
3. What data types are available in Postgres and when would you use each?
4. What constraints can you apply to columns and what do they enforce?
5. What is `SERIAL` and why do we use it for primary keys?
6. What does `DROP TABLE IF EXISTS` do and when would you use it?

## Key Concepts

* **`CREATE DATABASE`** — creates a new, empty database.
* **`CREATE TABLE`** — defines a new table: its name, columns, data types, and constraints.
* **Data type** — the kind of data a column accepts (`TEXT`, `INT`, `BOOLEAN`, `SERIAL`, `TIMESTAMPTZ`).
* **Constraint** — a rule enforced on a column (`NOT NULL`, `UNIQUE`, `DEFAULT`, `PRIMARY KEY`).
* **`SERIAL`** — a Postgres shorthand for an auto-incrementing integer. Used for primary keys so Postgres assigns a unique ID to each row automatically.
* **`DROP TABLE IF EXISTS`** — deletes a table (and all its data) if it exists. Used to cleanly recreate a table during development.
* **`DROP DATABASE IF EXISTS`** — deletes a database and all its tables if it exists.

## Creating a Database

Before creating tables, you need a database to put them in. Use `CREATE DATABASE`:

```sql
CREATE DATABASE pet_shelter;
```

Then connect to it in `psql`:

```sql
\c pet_shelter
```

You should see the prompt change to `pet_shelter=#`.

{% hint style="info" %}
You can also create a database from your terminal without opening `psql` first:

**MacOS:**
```sh
createdb pet_shelter
```

**Windows/WSL:**
```sh
sudo -u postgres createdb pet_shelter
```
{% endhint %}

## CREATE TABLE

`CREATE TABLE` defines the structure of a table — its name, what columns it has, what type of data each column stores, and any rules (constraints) that data must follow:

```sql
CREATE TABLE pets (
  pet_id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  species TEXT NOT NULL,
  age INT,
  is_adopted BOOLEAN DEFAULT FALSE
);
```

Breaking this down:
- `pets` — the table name (plural noun, `lower_snake_case`)
- Each line inside the parentheses defines one column: `column_name  data_type  constraints`
- `pet_id SERIAL PRIMARY KEY` — the **primary key**, auto-assigned a value by Postgres (1, 2, 3, etc...)
- `NOT NULL` — this column cannot be blank
- `DEFAULT FALSE` — if no value is provided for `is_adopted`, Postgres uses `FALSE`

Let's look more closely at the decisions you can make when designing your database tables.

### Data Types

Every column has a type, and the type determines what values Postgres will accept. Choosing the right type isn't just about storage — it affects correctness, sorting, and what operations you can perform on the data.

For example, if you store prices as `TEXT` instead of `NUMERIC`, Postgres will accept `'twelve dollars'` as a valid price, `SUM(price)` will throw an error, and sorting will be alphabetical (`'9'` comes after `'10'` because `1 < 9`). Conversely, if you store an age as `TEXT`, comparisons like `WHERE age > 18` won't work as expected. The type is part of the contract — it tells Postgres what the data means, not just what shape it is.

These are the types you'll use most:

| Type          | Use for                             | Example values                                |
| ------------- | ----------------------------------- | --------------------------------------------- |
| `TEXT`        | Any length string                   | `'Alice'`, `'A long description...'`          |
| `VARCHAR(n)`  | String with a max length            | `'123-555-8910'` (phone number, max 12 chars) |
| `INT`         | Whole numbers                       | `42`, `-7`, `0`                               |
| `NUMERIC`     | Decimal numbers                     | `9.99`, `3.14`                                |
| `BOOLEAN`     | True/false values                   | `TRUE`, `FALSE`                               |
| `SERIAL`      | Auto-incrementing integer (for PKs) | `1`, `2`, `3`, ...                            |
| `TIMESTAMPTZ` | Date and time with timezone         | `2024-03-15 10:30:00+00`                      |

{% hint style="info" %}
**`TEXT` vs `VARCHAR(n)`**: Use `TEXT` unless you have a specific reason to enforce a character limit (like for a phone number). In Postgres, `TEXT` is just as efficient as `VARCHAR` and is simpler to work with.
{% endhint %}

<details>

**<summary>Q: A table stores product prices. Should the column type be `INT` or `NUMERIC`?</summary>**

`NUMERIC` (or `NUMERIC(10, 2)` for exactly 2 decimal places). `INT` only stores whole numbers, so a price like `$9.99` would be truncated to `9`. `NUMERIC` stores exact decimal values, making it appropriate for prices, percentages, and any value where precision matters.

</details>

### Constraints

You might be thinking: "Can't I just validate data in my JavaScript before inserting it?" Yes — and you should. But JavaScript validation is your first line of defense, not your only one. It can have bugs. A future developer might add a new route and forget to add validation. Someone might run a script directly against the database to fix data. Constraints are the **last line of defense** — they live in the database itself and are enforced no matter how data gets in.

Consider a `users` table without a `UNIQUE` constraint on `email`. A bug in your registration endpoint — maybe a race condition, maybe a missed check — could create two accounts with the same email. Your app breaks. Support tickets pile up. With a `UNIQUE` constraint, that bug is impossible: Postgres rejects the duplicate before it ever lands in the table.

Constraints also serve as **documentation**. A `NOT NULL` constraint tells every future developer reading your schema: this field is required, always. That's information that would otherwise have to live in a comment, a code review, or someone's memory.

Postgres rejects any `INSERT` or `UPDATE` that would violate a constraint:

| Constraint                    | What it enforces                                                |
| ----------------------------- | --------------------------------------------------------------- |
| `NOT NULL`                    | The column must always have a value — it cannot be empty        |
| `UNIQUE`                      | No two rows can have the same value in this column              |
| `DEFAULT value`               | If no value is provided, use this default                       |
| `PRIMARY KEY`                 | Combines `NOT NULL` and `UNIQUE` — uniquely identifies each row |
| `REFERENCES other_table(col)` | Foreign key — value must exist in the referenced table          |

```sql
CREATE TABLE users (
  user_id   SERIAL  PRIMARY KEY,
  username  TEXT    NOT NULL UNIQUE,
  email     TEXT    NOT NULL UNIQUE,
  is_active BOOLEAN DEFAULT TRUE
);
```

Here, `username` and `email` are both `NOT NULL` (required) and `UNIQUE` (no duplicates). `is_active` defaults to `TRUE` if not provided.

<details>

**<summary>Q: You try to insert two users with the same email. What happens?</summary>**

Postgres returns an error: `ERROR: duplicate key value violates unique constraint "users_email_key"`. The second insert is rejected — the row is not added. Constraints are enforced at write time.

</details>

### The Primary Key Column

Every table should have a **primary key** — a column that uniquely identifies each row. In Postgres, the standard pattern is:

```sql
table_name_id SERIAL PRIMARY KEY
```

- **`SERIAL`** tells Postgres to create a sequence and automatically assign the next integer value whenever a row is inserted. You never need to provide this value in your `INSERT` statements.
- **`PRIMARY KEY`** enforces `NOT NULL` and `UNIQUE` on the column automatically
- **Named after the table** (`pet_id`, `user_id`, `post_id`) — this makes queries much clearer when multiple tables are involved, and it's the convention used here at Marcy.

```sql
-- No need to provide pet_id — Postgres handles it
INSERT INTO pets (name, species, age) VALUES ('Luna', 'cat', 3);
INSERT INTO pets (name, species, age) VALUES ('Mango', 'dog', 1);

SELECT * FROM pets;
-- pet_id | name  | species | age | is_adopted
-- -------+-------+---------+-----+-----------
--      1 | Luna  | cat     |   3 | f
--      2 | Mango | dog     |   1 | f
```

## Dropping and Recreating Tables

During development, you'll often need to recreate a table after changing its structure. For example, the `pets` table currently allows `NULL` values for the `age` column. We want to add in a `NOT NULL` constraint.

Use `DROP TABLE IF EXISTS` before `CREATE TABLE`:

```sql
DROP TABLE IF EXISTS pets;

CREATE TABLE pets (
  pet_id    SERIAL   PRIMARY KEY,
  name      TEXT     NOT NULL,
  species   TEXT     NOT NULL,
  age       INT      NOT NULL,
  is_adopted BOOLEAN DEFAULT FALSE
);
```

`IF EXISTS` prevents an error if the table doesn't exist yet. Without it, running `DROP TABLE` on a non-existent table would throw an error.

{% hint style="warning" %}
`DROP TABLE` permanently deletes the table **and all its data**. In development this is fine — you're rebuilding from scratch. Never run this on a production database without a backup.
{% endhint %}

Similarly, to start fresh with a database:

```sql
DROP DATABASE IF EXISTS pet_shelter;
CREATE DATABASE pet_shelter;
```

## Practice: Design and Build Your Own Database

Choose a scenario from the list below (or propose your own). Design a single table, create it in Postgres, insert at least 5 rows, and then write queries to explore it.

**Scenario ideas:**
- A pet shelter tracking available animals
- A library tracking books available to borrow
- A music playlist tracking songs
- A restaurant menu tracking dishes

**Steps:**

1. **Design your table** — decide on a name, columns, data types, and constraints. Think about:
   - What is the primary key?
   - Which columns should be `NOT NULL`?
   - Are there any columns that should be `UNIQUE`?
   - Are there columns where a `DEFAULT` value makes sense?

2. **Create the database and connect to it:**

   ```sql
   CREATE DATABASE my_app;
   \c my_app
   ```

3. **Write your `CREATE TABLE` statement** and run it.

4. **Insert at least 5 rows** with `INSERT INTO`.

5. **Query your data:**

   ```sql
   -- See all rows
   SELECT * FROM your_table;

   -- Filter by a condition
   SELECT * FROM your_table WHERE some_column = 'some_value';

   -- Sort results
   SELECT * FROM your_table ORDER BY some_column ASC;
   ```

6. **Try updating and deleting a row**, then verify with `SELECT`.

<details>

**<summary>Example solution: a pet shelter</summary>**

```sql
DROP TABLE IF EXISTS pets;

CREATE TABLE pets (
  pet_id     SERIAL   PRIMARY KEY,
  name       TEXT     NOT NULL,
  species    TEXT     NOT NULL,
  breed      TEXT,
  age        INT      NOT NULL,
  is_adopted BOOLEAN  DEFAULT FALSE
);

INSERT INTO pets (name, species, breed, age) VALUES
  ('Luna',   'cat', 'Domestic Shorthair', 3),
  ('Mango',  'dog', 'Labrador Mix',       1),
  ('Pepper', 'cat', 'Siamese',            5),
  ('Scout',  'dog', 'Beagle',             2),
  ('Olive',  'dog', 'Golden Retriever',   4);

-- See all pets
SELECT * FROM pets;

-- See only cats
SELECT * FROM pets WHERE species = 'cat';

-- Youngest pets first
SELECT name, age FROM pets ORDER BY age ASC;

-- Mark Luna as adopted
UPDATE pets SET is_adopted = TRUE WHERE name = 'Luna';

-- See who is still available
SELECT * FROM pets WHERE is_adopted = FALSE;
```

</details>
