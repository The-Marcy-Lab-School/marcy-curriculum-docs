# 2. Intro to SQL

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-2-intro-to-sql)!
{% endhint %}

In the previous lesson, you set up Postgres and used `psql` and TablePlus to explore a database. Now it's time to learn the language that powers all of it: **SQL**.

SQL is what you use to ask Postgres for data, add new data, change existing data, and delete data. In this lesson, you'll learn the core SQL operations by running queries against the `films_db` database you already have.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Review: How does PostgreSQL Work?](#review-how-does-postgresql-work)
- [What is SQL?](#what-is-sql)
- [SQL Syntax Rules](#sql-syntax-rules)
  - [Rule 1: The Semicolon Terminator](#rule-1-the-semicolon-terminator)
  - [Rule 2: Capitalize Keywords](#rule-2-capitalize-keywords)
  - [Rule 3: Single Quotes for Strings](#rule-3-single-quotes-for-strings)
  - [Rule 4: Logical Clause Order](#rule-4-logical-clause-order)
- [SELECT — Reading Data](#select--reading-data)
  - [Selecting Specific Columns](#selecting-specific-columns)
  - [Filtering with WHERE](#filtering-with-where)
  - [Sorting with ORDER BY](#sorting-with-order-by)
  - [Limiting Results with LIMIT](#limiting-results-with-limit)
- [INSERT — Creating Data](#insert--creating-data)
- [UPDATE — Modifying Data](#update--modifying-data)
- [DELETE — Removing Data](#delete--removing-data)
- [Putting It Together: CRUD in SQL](#putting-it-together-crud-in-sql)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is SQL and what is it used for?
2. What are the four SQL syntax rules you must follow to avoid errors?
3. How do you retrieve all rows from a table? How do you retrieve specific columns?
4. How do you filter rows with `WHERE`? Sort with `ORDER BY`? Limit results with `LIMIT`?
5. How do you insert, update, and delete rows?

## Key Concepts

* **SQL (Structured Query Language)** — the language used to interact with a relational database. SQL is declarative: you describe *what* you want, and the database figures out *how* to get it.
* **Statement** — a complete SQL instruction ending with a semicolon (`;`).
* **Clause** — a component of a SQL statement that modifies its behavior (e.g., `WHERE`, `ORDER BY`, `LIMIT`).
* **`SELECT`** — retrieves rows from a table.
* **`INSERT INTO`** — adds a new row to a table.
* **`UPDATE`** — modifies existing rows in a table.
* **`DELETE`** — removes rows from a table.
* **`WHERE`** — filters rows based on a condition.
* **`ORDER BY`** — sorts results by one or more columns.
* **`LIMIT`** — restricts the number of rows returned.

## Review: How does PostgreSQL Work?

Database management systems (DBMS) like PostgreSQL function similarly to web servers:

* **Networking**: PostgreSQL runs on a specific host and port (defaulting to `5432`).
* **Interpretation**: It listens for incoming connections and interprets the SQL commands sent by the client.
* **Execution**: It processes the incoming commands and sends the resulting data back to the requester.

![A diagram showing databases running as a separate process from the Express application](./img/1-intro-to-databases-postgres/full-stack-diagram.png)

The primary distinction lies in the type of request being handled: while an Express server is built to parse HTTP requests, PostgreSQL is built to parse and execute **Structured Query Language (SQL)**.

## What is SQL?

**SQL** is the universal language that relational database management systems are designed to parse and execute. The goal of SQL is to be easily readable by a human but structured to be consistently parsed by the DBMS.

For example, consider these SQL statements. What do you think they do?

```sql
INSERT INTO users (name) VALUES ('Alice');

SELECT * FROM users;

SELECT name FROM users;

SELECT name FROM users WHERE id = 1;

UPDATE users SET name = 'Alex' WHERE id = 1;

DELETE FROM users WHERE id = 2;
```

**<details><summary>Q: What do the SQL statements above do?</summary>**

1. Create a new record in the `users` table
2. Get the data in all columns from the `users` table
3. Get only the `name` data for all records in the `users` table
4. Get only the `name` data for the record with the id `1` in the `users` table
5. Update `name` of the the user with the id `1` in the `users` table to `'Alex'`
6. Delete the user with the id `2` from the `users` table. 

</details>

SQL statements are made up of **clauses** and **keywords**.

* A **keyword** is a reserved word that has a fixed meaning in SQL (e.g. The keyword  `SELECT` tells PostgreSQL to retrieve data from the database)
* A **clause** is the combination of keywords and data values that specify how the action should be carried out. Clauses can be combined to form complex SQL queries. (e.g. `SELECT name FROM users` and `WHERE id = 1` specifies that we want to see the data in the `name` column of the `users` table for the row where the user's id is 1.)

## SQL Syntax Rules

SQL is highly structured. The database acts as a strict interpreter that expects instructions in a specific format. Keep these four rules in mind to avoid syntax errors.

You can practice these rules using `psql` by connecting to the `films_db` database that you created in [the previous lesson](./1-intro-to-databases-postgres.md#the-psql-cli).

{% tabs %}
{% tab title="Mac" %}
```sh
psql films_db
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
sudo -u postgres psql films_db
```
{% endtab %}
{% endtabs %}


### Rule 1: The Semicolon Terminator

Every SQL statement must end with a semicolon (`;`). It tells Postgres that a statement is complete and ready to execute.

```sql
-- ✅ Correct
SELECT * FROM films;

-- ❌ Missing semicolon — psql waits for more input (you have to enter ; on the next line)
SELECT * FROM films
```

### Rule 2: Capitalize Keywords

SQL is case-insensitive — `select` and `SELECT` do the same thing. But by convention, **SQL keywords are written in uppercase** and table/column names in lowercase. This makes it easy to tell commands apart from data at a glance.

```sql
-- ✅ Easy to read
SELECT title, genre FROM films WHERE genre = 'sci-fi';

-- ❌ Works but harder to scan
select title, genre from films where genre = 'sci-fi';
```

### Rule 3: Single Quotes for Strings

String values (text data) use **single quotes**. Double quotes are reserved for identifiers (table and column names), so using them for string values will cause an error.

```sql
-- ✅ Correct
SELECT * FROM films WHERE genre = 'sci-fi';

-- ❌ Postgres looks for a column named sci-fi
SELECT * FROM films WHERE genre = "sci-fi";
```

### Rule 4: Logical Clause Order

SQL clauses must appear in a specific order. For a `SELECT` statement the order is:

```sql
SELECT   columns
FROM     table
WHERE    condition
ORDER BY column
LIMIT    n;
```

```sql
-- ✅ Correct order
SELECT title FROM films WHERE genre = 'sci-fi' ORDER BY year LIMIT 3;

-- ❌ WHERE must come before ORDER BY
SELECT title FROM films ORDER BY year WHERE genre = 'sci-fi';
```

{% hint style="info" %}
**Indentation tip**: For complex queries, put each clause on its own line. It's easier to read and the semicolon goes at the very end:

```sql
SELECT title, genre
FROM films
WHERE genre = 'sci-fi'
ORDER BY year DESC
LIMIT 3;
```
{% endhint %}

## SELECT — Reading Data

`SELECT` retrieves rows from a table. The `*` wildcard means "all columns":

```sql
SELECT * FROM films;
```

This returns every row and every column in the `films` table.

### Selecting Specific Columns

List only the columns you need instead of using `*`:

```sql
SELECT title, genre FROM films;
SELECT title, year, director FROM films;
```

<details>

<summary><strong>Q: Why would you choose specific columns over <code>SELECT *</code>?</strong></summary>

`SELECT *` returns more data than you might need, which is slower on large tables and makes your code harder to understand. Specifying columns makes your intent clear and is more efficient. In production code, `SELECT *` is generally avoided.

</details>

### Filtering with WHERE

`WHERE` filters rows by a condition (conditions are sometimes called **"predicates"**). Only rows where the condition is true are returned:

```sql
-- Films from a specific genre
SELECT * FROM films WHERE genre = 'sci-fi';

-- Films released after 2015
SELECT * FROM films WHERE year > 2015;

-- A specific film by title
SELECT * FROM films WHERE title = 'Parasite';
```

**Common comparison operators:**

| Operator          | Meaning                           |
| ----------------- | --------------------------------- |
| `=`               | Equal to                          |
| `<>` or `!=`      | Not equal to                      |
| `>` / `<`         | Greater / less than               |
| `>=` / `<=`       | Greater / less than or equal      |
| `LIKE 'pattern%'` | Pattern match (`%` is a wildcard) |

```sql
-- Films whose title starts with 'The'
SELECT * FROM films WHERE title LIKE 'The%';

-- Films that are NOT sci-fi
SELECT * FROM films WHERE genre <> 'sci-fi';
```

**Combining conditions with AND / OR:**

```sql
-- Sci-fi films released after 2010
SELECT * FROM films WHERE genre = 'sci-fi' AND year > 2010;

-- Horror or thriller films
SELECT * FROM films WHERE genre = 'horror' OR genre = 'thriller';
```

### Sorting with ORDER BY

`ORDER BY` sorts the results by one or more columns. The default direction is ascending (`ASC`). Use `DESC` for descending:

```sql
-- Oldest to newest
SELECT * FROM films ORDER BY year ASC;

-- Newest to oldest
SELECT * FROM films ORDER BY year DESC;

-- Alphabetically by title
SELECT * FROM films ORDER BY title ASC;
```

### Limiting Results with LIMIT

`LIMIT` caps the number of rows returned. Useful for previewing large tables or showing "top N" results:

```sql
-- First 3 results
SELECT * FROM films LIMIT 3;

-- 3 most recent films
SELECT * FROM films ORDER BY year DESC LIMIT 3;
```

<details>

<summary><strong>Q: What does this query do? <code>SELECT title FROM films WHERE genre = 'sci-fi' ORDER BY year DESC LIMIT 2;</code></strong></summary>

It returns the titles of the 2 most recently released sci-fi films, sorted from newest to oldest.

</details>

## INSERT — Creating Data

`INSERT INTO` adds a new row to a table. You specify which columns to fill and provide corresponding values:

```sql
INSERT INTO films (title, director, year, genre)
VALUES ('Dune: Part Two', 'Denis Villeneuve', 2024, 'sci-fi');
```

* Column names and values must be in the same order
* The `film_id` column is omitted — it's a `SERIAL` primary key and Postgres assigns it automatically
* String values use single quotes; numbers do not

**Inserting multiple rows at once:**

```sql
INSERT INTO films (title, director, year, genre)
VALUES
  ('Arrival', 'Denis Villeneuve', 2016, 'sci-fi'),
  ('Us', 'Jordan Peele', 2019, 'horror');
```

<details>

<summary><strong>Q: You try to insert a film but forget to include the <code>title</code> column, which has a <code>NOT NULL</code> constraint. What happens?</strong></summary>

Postgres returns an error: `ERROR: null value in column "title" violates not-null constraint`. The row is not inserted. Constraints are enforced at write time — the database rejects any data that violates the rules defined when the table was created.

</details>

## UPDATE — Modifying Data

`UPDATE` modifies existing rows. **Always use a `WHERE` clause** — without it, you update every row in the table.

```sql
-- Fix a typo in a title
UPDATE films
SET title = 'Everything Everywhere All at Once'
WHERE film_id = 6;

-- Change a genre
UPDATE films
SET genre = 'drama'
WHERE title = 'Parasite';
```

**Updating multiple columns at once:**

```sql
UPDATE films
SET genre = 'sci-fi', year = 2023
WHERE film_id = 3;
```

{% hint style="warning" %}
**Always include a `WHERE` clause with `UPDATE`.**

```sql
-- ❌ This updates EVERY row in the table
UPDATE films SET genre = 'drama';
```

Before running an `UPDATE`, run a `SELECT` with the same `WHERE` clause first to confirm you're targeting the right rows.
{% endhint %}

## DELETE — Removing Data

`DELETE` removes rows from a table. Like `UPDATE`, **always include a `WHERE` clause**.

```sql
-- Remove a specific film
DELETE FROM films WHERE film_id = 7;

-- Remove all films from a certain year
DELETE FROM films WHERE year = 1999;
```

{% hint style="warning" %}
**Always include a `WHERE` clause with `DELETE`.**

```sql
-- ❌ This deletes EVERY row in the table
DELETE FROM films;
```

{% endhint %}

## Putting It Together: CRUD in SQL

Here's the mapping between CRUD operations and SQL statements:

| CRUD Operation | SQL Statement    |
| -------------- | ---------------- |
| **Create**     | `INSERT INTO`    |
| **Read**       | `SELECT`         |
| **Update**     | `UPDATE ... SET` |
| **Delete**     | `DELETE FROM`    |

Try these practice queries against your `films_db` database in `psql` or TablePlus:

```sql
-- 1. Get all films
SELECT * FROM films;

-- 2. Get only the title and director of all films
SELECT title, director FROM films;

-- 3. Get all sci-fi films, newest first
SELECT * FROM films WHERE genre = 'sci-fi' ORDER BY year DESC;

-- 4. Get the 3 oldest films
SELECT * FROM films ORDER BY year ASC LIMIT 3;

-- 5. Add a new film
INSERT INTO films (title, director, year, genre)
VALUES ('Dune', 'Denis Villeneuve', 2021, 'sci-fi');

-- 6. Update the genre for a specific film
UPDATE films SET genre = 'drama' WHERE title = 'Moonlight';

-- 7. Delete a film by ID
DELETE FROM films WHERE film_id = 1;

-- 8. Verify your changes
SELECT * FROM films;
```

<details>

<summary><strong>Q: You delete a row with <code>film_id = 3</code> and then insert a new film. What <code>film_id</code> does the new film get?</strong></summary>

The new film gets the next value in the `SERIAL` sequence — it does **not** reuse the deleted ID. If the sequence was at 7, the new film gets `film_id = 8`. Primary key values are never reused. This is intentional: reusing IDs could cause bugs if anything (a foreign key, a cached reference) still points to the old row.

</details>
