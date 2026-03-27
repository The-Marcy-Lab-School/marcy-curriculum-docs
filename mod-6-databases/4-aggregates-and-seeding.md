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
  - [SUM, AVG, MIN, MAX and AS](#sum-avg-min-max-and-as)
- [GROUP BY](#group-by)
  - [Filtering Groups with WHERE and HAVING](#filtering-groups-with-where-and-having)
- [Aggregate Functions Challenge](#aggregate-functions-challenge)
- [Seeding a Database](#seeding-a-database)
  - [Why Seeding Matters](#why-seeding-matters)
  - [Anatomy of a Seed File](#anatomy-of-a-seed-file)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is an aggregate function? What does each of `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` do?
2. What does `GROUP BY` do? When do you use it with aggregate functions?
3. What is the difference between `WHERE` and `HAVING`?
4. What is a seed file? Why is seeding important for development?

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
  game_id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  genre TEXT NOT NULL,
  platform TEXT NOT NULL,
  release_year INT NOT NULL,
  rating NUMERIC(3,1) NOT NULL,
  price NUMERIC(5,2) NOT NULL
);

INSERT INTO games (title, genre, platform, release_year, rating, price) VALUES
  ('The Legend of Zelda: Breath of the Wild', 'action-adventure', 'Switch', 2017, 9.7, 59.99),
  ('Elden Ring', 'action-rpg', 'PC', 2022, 9.5, 59.99),
  ('Hades', 'roguelike', 'PC', 2020, 9.3, 24.99),
  ('Celeste', 'platformer', 'PC', 2018, 9.4, 19.99),
  ('The Witcher 3', 'action-rpg', 'PC', 2015, 9.6, 39.99),
  ('Hollow Knight', 'platformer', 'PC', 2017, 9.1, 14.99),
  ('God of War', 'action-adventure', 'PS4', 2018, 9.8, 49.99),
  ('Stardew Valley', 'simulation', 'PC', 2016, 9.2, 14.99),
  ('Disco Elysium', 'rpg', 'PC', 2019, 9.5, 39.99),
  ('Animal Crossing: New Horizons', 'simulation', 'Switch', 2020, 9.0, 59.99),
  ('Metroid Dread', 'action-adventure', 'Switch', 2021, 8.9, 59.99),
  ('Return of the Obra Dinn', 'puzzle', 'PC', 2018, 9.1, 19.99),
  ('Monster Hunter: World', 'action-rpg', 'PC', 2018, 9.2, 29.99),
  ('Dead Cells', 'roguelike', 'PC', 2018, 9.0, 24.99),
  ('Outer Wilds', 'adventure', 'PC', 2019, 9.4, 24.99);
```

Once seeded, connect to it in `psql`:

```sh
psql -d games_db
```

And look at the data:

```sql
SELECT * FROM games;
```

## Aggregate Functions

In JavaScript, we have practiced using functions like `.reduce()` to calculate a single value from an array of data. For example, we might want to know the total price of items in a cart:

```js
const cart = [
  { item: 'Apple', price: 1, quantity: 3},
  { item: 'Banana', price: 0.5, quantity: 2},
  { item: 'Corn', price: 1.5, quantity: 1},
]

const cartTotal = cart.reduce((total, item) => total + (item.price * item.quantity), 0);
```

This kind of data analysis is exactly what SQL was built to do. Rather than performing these calculations using JavaScript, we can perform use SQL's **aggregate functions**:
* `COUNT`
* `SUM`
* `AVG`
* `MIN`
* `MAX`

### COUNT

`COUNT` returns the number of rows matching a query:

```sql
-- Total number of games
SELECT COUNT(*) FROM games;

-- Number of PC games
SELECT COUNT(*) FROM games WHERE platform = 'PC';
```

`COUNT(*)` counts all rows. `COUNT(column_name)` counts only rows where that column is not null.

### SUM, AVG, MIN, MAX and AS

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

## GROUP BY

On its own, an aggregate function collapses all rows into a single result. For example, we can count the number of games in our database like this:

```sql
SELECT COUNT(*) from games;
```

Result:

```
 count 
-------
    15
```

But we can also count the number of games broken down by genre using `GROUP BY` which applies aggregate functions *per group* — one result per unique value in a column.

```sql
-- How many games are in each genre?
SELECT genre, COUNT(*) AS game_count
FROM games
GROUP BY genre;
```

Result:

```
      genre       | game_count 
------------------+------------
 roguelike        |          2
 platformer       |          2
 action-adventure |          3
 action-rpg       |          3
 rpg              |          1
 adventure        |          1
 puzzle           |          1
 simulation       |          2
```

More examples:

```sql
-- Most expensive game per platform
SELECT platform, MAX(price) AS max_price
FROM games
GROUP BY platform;

-- Number of games released each year, sorted by year
SELECT release_year, COUNT(*) AS games_released
FROM games
GROUP BY release_year
ORDER BY release_year ASC;

-- Most expensive game per platform per year, sorted by year
SELECT 
    platform, 
    release_year, 
    MAX(price) AS max_price
FROM games
GROUP BY 
    platform, 
    release_year
ORDER BY 
    release_year DESC, 
    platform;
```

**The rule**: any column in your `SELECT` that is not inside an aggregate function must appear in your `GROUP BY` clause. 
* In the final example, you can see that we use the `MAX` aggregate function on the `price` column and are selecting the `platform` and `release_year` columns. Those two columns must be included in the GROUP BY` clause.

**Why does this rule exist?** Because `GROUP BY` collapses many rows into one — if you `SELECT title` while grouping by `genre`, a genre might have 5 different titles. SQL doesn't know which one to show.

### Filtering Groups with WHERE and HAVING

`WHERE` filters individual rows *before* grouping:

```sql
-- Count the genres for games released before 2020 (games after 2020 aren't included in the count)
SELECT genre, COUNT(*) AS game_count_before_2020
FROM games
WHERE release_year <= 2020
GROUP BY genre;
```

`HAVING` filters groups *after* aggregation.

```sql
-- Only show genres with more than 2 games
SELECT genre, COUNT(*) AS game_count
FROM games
GROUP BY genre
HAVING COUNT(*) > 2;

-- Only show platforms with an average price of over $30
SELECT platform, AVG(price) AS avg_price
FROM games
GROUP BY platform
HAVING AVG(price) > 30;

-- Among PC games only, show genres with avg rating above 9.0
SELECT genre, AVG(rating) AS avg_rating
FROM games
WHERE platform = 'PC'       -- filter rows first: only PC games
GROUP BY genre
HAVING AVG(rating) > 9.0;   -- then filter groups: only high-rated genres
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

**<details><summary>Q: What's the difference between `WHERE` and `HAVING`?</summary>**

`WHERE` runs before grouping — it filters which rows are included in the calculation at all.

`HAVING` runs after grouping — it filters which groups appear in the final result based on the aggregate result.

You can use both in the same query

</details>

## Aggregate Functions Challenge

**<details><summary>Q: How many games have a rating above 9.5?</summary>**

Query:

```sql
SELECT COUNT(*) FROM games WHERE rating >= 9.5;
```

Results: 5 games!

</details>

**<details><summary>Q: How many PC games have a rating above 9.5?</summary>**

Query:

```sql
SELECT COUNT(*) FROM games WHERE rating >= 9.5 AND platform = 'PC';
```

Results: 3 games!

</details>

**<details><summary>Q: What is the average rating and average price of PC games?</summary>**

Query:

```sql
SELECT
  AVG(rating) AS avg_rating,
  AVG(price) AS avg_price
FROM games 
WHERE platform = 'PC';
```

Results:

```
     avg_rating     |      avg_price      
--------------------+---------------------
 9.3000000000000000 | 28.6263636363636364
```

</details>

**<details><summary>Q: What is the total price of Switch Games?</summary>**

Query:

```sql
SELECT SUM(price) FROM games WHERE platform = 'Switch';
```

Results:

```
  sum
--------
 179.97
```

</details>

**<details><summary>Q: What is the average price of games in each genre?</summary>**

Query:

```sql
SELECT genre, AVG(price) AS avg_price
FROM games
GROUP BY genre;
```

Result:

```
      genre       |      avg_price      
------------------+---------------------
 roguelike        | 24.9900000000000000
 platformer       | 17.4900000000000000
 action-adventure | 56.6566666666666667
 action-rpg       | 43.3233333333333333
 rpg              | 39.9900000000000000
 adventure        | 24.9900000000000000
 puzzle           | 19.9900000000000000
 simulation       | 37.4900000000000000
```

</details>

**<details><summary>Q: Among PC games, which genres have an average price of more than $30?</summary>**

Query:

```sql
SELECT genre, AVG(price) AS avg_price
FROM games
WHERE platform = 'PC'       -- filter rows first: only PC games
GROUP BY genre
HAVING AVG(price) > 30;   -- then filter groups: only genres with average price over $30
```

Result:

```
   genre    |      avg_price      
------------+---------------------
 action-rpg | 43.3233333333333333
 rpg        | 39.9900000000000000
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

**<details><summary>Q: Why use `DROP DATABASE IF EXISTS` at the top of a seed file rather than just `DROP TABLE IF EXISTS`?</summary>**

`DROP TABLE IF EXISTS` only removes one table at a time. If your schema has multiple tables (which it will once you learn about relationships), you'd need to drop them in the right order to avoid foreign key constraint errors.

`DROP DATABASE IF EXISTS` removes the entire database at once — no ordering issues, guaranteed clean slate. The tradeoff is that you lose everything in the database, but that's exactly what a seed file is designed for: rebuilding from scratch.

</details>
