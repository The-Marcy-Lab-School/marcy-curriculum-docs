# 4. Aggregates & Seeding

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-4-aggregates-and-seeding)!
{% endhint %}

So far you've written queries that return individual rows. But often you want to ask questions about your data as a whole: "How many films are in each genre?" "What's the average price of a product?" "Which category has the most items?"

These are **aggregate queries** — and they're one of the most powerful features of SQL.

In this lesson, you'll also learn about **seeding**: the practice of populating a database from a `.sql` file, which makes your development environment reproducible and your data consistent.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Setting Up: Run the Seed File](#setting-up-run-the-seed-file)
- [Aggregate Functions](#aggregate-functions)
  - [COUNT](#count)
  - [SUM, AVG, MIN, MAX](#sum-avg-min-max)
- [GROUP BY](#group-by)
  - [Postgres Smart GROUP BY](#postgres-smart-group-by)
- [Filtering Groups with HAVING](#filtering-groups-with-having)
- [Seeding a Database](#seeding-a-database)
  - [Why Seeding Matters](#why-seeding-matters)
  - [Anatomy of a Seed File](#anatomy-of-a-seed-file)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is an aggregate function? What does each of `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` do?
2. What does `GROUP BY` do? When do you use it with aggregate functions?
3. In Postgres, if you `GROUP BY` a primary key, why don't you need to list every other column in the `GROUP BY` clause?
4. What is the difference between `WHERE` and `HAVING`?
5. What is a seed file? Why is seeding important for development?

## Key Concepts

* **Aggregate function** — a function that computes a single result from a set of rows (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`).
* **`GROUP BY`** — divides rows into groups based on a column's value, so aggregate functions are applied per group.
* **Postgres smart `GROUP BY`** — when you `GROUP BY` a primary key, Postgres allows you to include other columns from that table in `SELECT` without listing them in `GROUP BY`, because the primary key uniquely determines them.
* **`HAVING`** — filters groups after aggregation (like `WHERE` for groups).
* **Seed file** — a `.sql` file containing `DROP`, `CREATE`, and `INSERT` statements that builds and populates a database from scratch.
* **Seeding** — the act of running a seed file to create a reproducible database state.

## Setting Up: Run the Seed File

This lesson uses a `games_db` database. Run the seed file from the follow-along repo to set it up:

{% tabs %}
{% tab title="Mac" %}
```sh
psql -f games_seed.sql
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
sudo -u postgres psql -f games_seed.sql
```
{% endtab %}
{% endtabs %}

The seed file creates a `games` table with columns for title, genre, platform, release year, rating, and price:

```sql
DROP DATABASE IF EXISTS games_db;
CREATE DATABASE games_db;
\c games_db

CREATE TABLE games (
  game_id       SERIAL        PRIMARY KEY,
  title         TEXT          NOT NULL,
  genre         TEXT          NOT NULL,
  platform      TEXT          NOT NULL,
  release_year  INT           NOT NULL,
  rating        NUMERIC(3,1)  NOT NULL,
  price         NUMERIC(5,2)  NOT NULL
);

INSERT INTO games (title, genre, platform, release_year, rating, price) VALUES
  ('The Legend of Zelda: Breath of the Wild', 'action-adventure', 'Switch',      2017, 9.7, 59.99),
  ('Elden Ring',                              'action-rpg',       'PC',          2022, 9.5, 59.99),
  ('Hades',                                  'roguelike',        'PC',          2020, 9.3, 24.99),
  ('Celeste',                                'platformer',       'PC',          2018, 9.4, 19.99),
  ('The Witcher 3',                          'action-rpg',       'PC',          2015, 9.6, 39.99),
  ('Hollow Knight',                          'platformer',       'PC',          2017, 9.1, 14.99),
  ('God of War',                             'action-adventure', 'PS4',         2018, 9.8, 49.99),
  ('Stardew Valley',                         'simulation',       'PC',          2016, 9.2, 14.99),
  ('Disco Elysium',                          'rpg',              'PC',          2019, 9.5, 39.99),
  ('Animal Crossing: New Horizons',          'simulation',       'Switch',      2020, 9.0, 59.99),
  ('Metroid Dread',                          'action-adventure', 'Switch',      2021, 8.9, 59.99),
  ('Return of the Obra Dinn',                'puzzle',           'PC',          2018, 9.1, 19.99),
  ('Monster Hunter: World',                  'action-rpg',       'PC',          2018, 9.2, 29.99),
  ('Dead Cells',                             'roguelike',        'PC',          2018, 9.0, 24.99),
  ('Outer Wilds',                            'adventure',        'PC',          2019, 9.4, 24.99);
```

Once seeded, connect to it in `psql`:

```sql
\c games_db
SELECT * FROM games;
```

## Aggregate Functions

Aggregate functions compute a single value from a set of rows. Run these against the `games` table:

### COUNT

`COUNT` returns the number of rows matching a query:

```sql
-- Total number of games
SELECT COUNT(*) FROM games;

-- Number of PC games
SELECT COUNT(*) FROM games WHERE platform = 'PC';
```

`COUNT(*)` counts all rows. `COUNT(column_name)` counts only rows where that column is not null.

### SUM, AVG, MIN, MAX

```sql
-- Total value of all games (sum of all prices)
SELECT SUM(price) FROM games;

-- Average rating across all games
SELECT AVG(rating) FROM games;

-- Cheapest and most expensive game
SELECT MIN(price), MAX(price) FROM games;

-- Newest and oldest release year
SELECT MIN(release_year), MAX(release_year) FROM games;
```

You can give aggregate results a readable name using `AS`:

```sql
SELECT
  AVG(rating)  AS avg_rating,
  MIN(price)   AS cheapest,
  MAX(price)   AS most_expensive
FROM games;
```

<details>

<summary><strong>Q: What's the difference between <code>COUNT(*)</code> and <code>COUNT(column_name)</code>?</strong></summary>

`COUNT(*)` counts every row regardless of its values, including rows with `NULL` in any column.

`COUNT(column_name)` counts only rows where that column is not `NULL`. If 3 out of 10 rows have a `NULL` rating, `COUNT(*) = 10` but `COUNT(rating) = 7`.

In practice, `COUNT(*)` is the most common — use `COUNT(column_name)` only when you specifically want to count non-null values.

</details>

## GROUP BY

On its own, aggregate functions collapse all rows into a single result. `GROUP BY` lets you apply aggregate functions *per group* — one result per unique value in a column.

```sql
-- How many games are in each genre?
SELECT genre, COUNT(*) AS game_count
FROM games
GROUP BY genre;
```

Result:

```
  genre            | game_count
-------------------+-----------
 action-adventure  |         3
 action-rpg        |         3
 roguelike         |         2
 platformer        |         2
 simulation        |         2
 rpg               |         1
 puzzle            |         1
 adventure         |         1
```

More examples:

```sql
-- Average price per genre
SELECT genre, AVG(price) AS avg_price
FROM games
GROUP BY genre;

-- Most expensive game per platform
SELECT platform, MAX(price) AS max_price
FROM games
GROUP BY platform;

-- Number of games released each year
SELECT release_year, COUNT(*) AS games_released
FROM games
GROUP BY release_year
ORDER BY release_year ASC;
```

**The rule**: any column in your `SELECT` that is not inside an aggregate function must appear in your `GROUP BY` clause — unless Postgres can infer the value from the group. The next section explains when you can skip that.

### Postgres Smart GROUP BY

In standard SQL, every non-aggregate column in `SELECT` must be listed in `GROUP BY`. **Postgres relaxes this rule when you group by a primary key.**

When you group by a table's primary key, Postgres knows that every other column in that table is *uniquely determined* by that key. There's only one `title`, one `genre`, one `price` per `game_id` — so you don't need to list them all in `GROUP BY`.

```sql
-- Standard SQL would require: GROUP BY game_id, title, genre, platform
-- Postgres only needs the primary key:
SELECT game_id, title, genre, platform, rating
FROM games
GROUP BY game_id;
```

This becomes especially useful when doing `JOIN` queries (covered in the next lesson) where you're counting or aggregating related records per row in a parent table.

{% hint style="info" %}
This behavior applies specifically to grouping by the **primary key** of a table. If you group by a non-PK column (like `genre`), you still need to list every other selected non-aggregate column in `GROUP BY`.
{% endhint %}

<details>

<summary><strong>Q: Why does grouping by the primary key let you skip other columns in GROUP BY?</strong></summary>

A primary key uniquely identifies each row. If you group by `game_id`, there's only one possible row per group — Postgres knows that every other column (`title`, `genre`, `price`) can only have one value for that group. Since there's no ambiguity about which value to use, Postgres doesn't require you to explicitly list them. This is called **functional dependency**: other columns are *functionally dependent* on the primary key.

</details>

## Filtering Groups with HAVING

`WHERE` filters individual rows *before* grouping. `HAVING` filters groups *after* aggregation.

```sql
-- Only show genres with more than 2 games
SELECT genre, COUNT(*) AS game_count
FROM games
GROUP BY genre
HAVING COUNT(*) > 2;

-- Only show platforms where the average price is over $30
SELECT platform, AVG(price) AS avg_price
FROM games
GROUP BY platform
HAVING AVG(price) > 30;
```

The clause order for a full aggregate query is:

```sql
SELECT   column(s), aggregate_function
FROM     table
WHERE    row-level condition      -- filters rows before grouping
GROUP BY column
HAVING   group-level condition    -- filters groups after aggregation
ORDER BY column
LIMIT    n;
```

<details>

<summary><strong>Q: What's the difference between <code>WHERE</code> and <code>HAVING</code>?</strong></summary>

`WHERE` runs before grouping — it filters which rows are included in the calculation at all.

`HAVING` runs after grouping — it filters which groups appear in the final result based on the aggregate result.

You can use both in the same query:

```sql
-- Among PC games only, show genres with avg rating above 9.0
SELECT genre, AVG(rating) AS avg_rating
FROM games
WHERE platform = 'PC'       -- filter rows first: only PC games
GROUP BY genre
HAVING AVG(rating) > 9.0;   -- then filter groups: only high-rated genres
```

</details>

## Seeding a Database

### Why Seeding Matters

Every time you've run a `.sql` file in this lesson, you've been seeding. **Seeding** is the practice of populating a database from a file so that:

- **Any developer can recreate the same database** — just run the seed file
- **You can reset to a known state** at any time — drop and re-seed
- **Your schema and your data stay in sync** — the seed file defines both

Without seeding, every developer on a team would need to manually run SQL commands to set up their local database — and they'd end up with slightly different data, making bugs hard to reproduce.

### Anatomy of a Seed File

A well-structured seed file follows this pattern:

```sql
-- 1. Drop and recreate the database (clean slate)
DROP DATABASE IF EXISTS games_db;
CREATE DATABASE games_db;
\c games_db

-- 2. Drop and recreate tables (ensures schema is up to date)
DROP TABLE IF EXISTS games;

CREATE TABLE games (
  game_id      SERIAL        PRIMARY KEY,
  title        TEXT          NOT NULL,
  genre        TEXT          NOT NULL,
  platform     TEXT          NOT NULL,
  release_year INT           NOT NULL,
  rating       NUMERIC(3,1)  NOT NULL,
  price        NUMERIC(5,2)  NOT NULL
);

-- 3. Insert seed data
INSERT INTO games (title, genre, platform, release_year, rating, price) VALUES
  ('Hades', 'roguelike', 'PC', 2020, 9.3, 24.99),
  ('Celeste', 'platformer', 'PC', 2018, 9.4, 19.99);
  -- ... more rows
```

**Running a seed file:**

{% tabs %}
{% tab title="Mac" %}
```sh
psql -f seed.sql
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
sudo -u postgres psql -f seed.sql
```
{% endtab %}
{% endtabs %}

The `DROP ... IF EXISTS` at the top ensures that re-running the file always starts from a clean state. This is the pattern you'll use throughout the rest of the module — every project will have a seed file.

<details>

<summary><strong>Q: Why use <code>DROP DATABASE IF EXISTS</code> at the top of a seed file rather than just <code>DROP TABLE IF EXISTS</code>?</strong></summary>

`DROP TABLE IF EXISTS` only removes one table at a time. If your schema has multiple tables (which it will once you learn about relationships), you'd need to drop them in the right order to avoid foreign key constraint errors.

`DROP DATABASE IF EXISTS` removes the entire database at once — no ordering issues, guaranteed clean slate. The tradeoff is that you lose everything in the database, but that's exactly what a seed file is designed for: rebuilding from scratch.

</details>
