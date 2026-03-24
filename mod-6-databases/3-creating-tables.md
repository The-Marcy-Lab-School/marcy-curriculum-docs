# 3. Creating Tables

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-3-creating-tables)!
{% endhint %}

So far you've been querying a database that was already set up for you. In this lesson, you'll create your own database and tables from scratch using `CREATE DATABASE` and `CREATE TABLE` — the SQL statements that define the structure of your data.

By the end, you'll have designed and built a real database, inserted data into it, and queried it yourself.

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

```sh
createdb pet_shelter      -- Mac
```

```sh
sudo -u postgres createdb pet_shelter   -- Windows/WSL
```
{% endhint %}

## CREATE TABLE

`CREATE TABLE` defines the structure of a table — its name, what columns it has, what type of data each column stores, and any rules (constraints) that data must follow:

```sql
CREATE TABLE pets (
  pet_id    SERIAL        PRIMARY KEY,
  name      TEXT          NOT NULL,
  species   TEXT          NOT NULL,
  age       INT,
  is_adopted BOOLEAN      DEFAULT FALSE
);
```

Breaking this down:
- `pets` — the table name (plural noun, `lower_snake_case`)
- Each line inside the parentheses defines one column: `column_name  data_type  constraints`
- `pet_id SERIAL PRIMARY KEY` — the primary key, auto-assigned by Postgres
- `NOT NULL` — this column must always have a value
- `DEFAULT FALSE` — if no value is provided for `is_adopted`, Postgres uses `FALSE`

### Data Types

Postgres has many data types. These are the ones you'll use most:

| Type | Use for | Example values |
| ---- | ------- | -------------- |
| `TEXT` | Any length string | `'Alice'`, `'A long description...'` |
| `VARCHAR(n)` | String with a max length | `'Alice'` (max 100 chars) |
| `INT` | Whole numbers | `42`, `-7`, `0` |
| `NUMERIC` | Decimal numbers | `9.99`, `3.14` |
| `BOOLEAN` | True/false values | `TRUE`, `FALSE` |
| `SERIAL` | Auto-incrementing integer (for PKs) | `1`, `2`, `3`, ... |
| `TIMESTAMPTZ` | Date and time with timezone | `2024-03-15 10:30:00+00` |

{% hint style="info" %}
**`TEXT` vs `VARCHAR(n)`**: Use `TEXT` unless you have a specific reason to enforce a character limit. In Postgres, `TEXT` is just as efficient as `VARCHAR` and is simpler to work with.
{% endhint %}

<details>

<summary><strong>Q: A table stores product prices. Should the column type be <code>INT</code> or <code>NUMERIC</code>?</strong></summary>

`NUMERIC` (or `NUMERIC(10, 2)` for exactly 2 decimal places). `INT` only stores whole numbers, so a price like `$9.99` would be truncated to `9`. `NUMERIC` stores exact decimal values, making it appropriate for prices, percentages, and any value where precision matters.

</details>

### Constraints

Constraints enforce rules on the data in a column. Postgres rejects any `INSERT` or `UPDATE` that would violate them.

| Constraint | What it enforces |
| ---------- | --------------- |
| `NOT NULL` | The column must always have a value — it cannot be empty |
| `UNIQUE` | No two rows can have the same value in this column |
| `DEFAULT value` | If no value is provided, use this default |
| `PRIMARY KEY` | Combines `NOT NULL` and `UNIQUE` — uniquely identifies each row |
| `REFERENCES other_table(col)` | Foreign key — value must exist in the referenced table |

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

<summary><strong>Q: You try to insert two users with the same email. What happens?</strong></summary>

Postgres returns an error: `ERROR: duplicate key value violates unique constraint "users_email_key"`. The second insert is rejected — the row is not added. Constraints are enforced at write time.

</details>

### The Primary Key Column

Every table should have a primary key — a column that uniquely identifies each row. In Postgres, the standard pattern is:

```sql
table_name_id  SERIAL  PRIMARY KEY
```

- **`SERIAL`** tells Postgres to create a sequence and automatically assign the next integer value whenever a row is inserted. You never need to provide this value in your `INSERT` statements.
- **`PRIMARY KEY`** enforces `NOT NULL` and `UNIQUE` on the column.
- **Named after the table** (`pet_id`, `user_id`, `post_id`) — this makes queries much clearer when multiple tables are involved, and it's the convention used throughout this course.

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

During development, you'll often need to recreate a table after changing its structure. Use `DROP TABLE IF EXISTS` before `CREATE TABLE`:

```sql
DROP TABLE IF EXISTS pets;

CREATE TABLE pets (
  pet_id    SERIAL   PRIMARY KEY,
  name      TEXT     NOT NULL,
  species   TEXT     NOT NULL,
  age       INT,
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

<summary><strong>Example solution: a pet shelter</strong></summary>

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
