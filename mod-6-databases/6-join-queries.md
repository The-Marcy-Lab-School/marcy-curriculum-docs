# 6. JOIN Queries

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-6-join-queries)!
{% endhint %}

In the previous lesson, you designed multi-table schemas and used foreign keys to connect related data. At the very end, there was a teaser:

```sql
SELECT students.first_name, classes.title
FROM students
  INNER JOIN enrollments ON students.student_id = enrollments.student_id
  INNER JOIN classes     ON enrollments.class_id = classes.class_id
WHERE students.first_name = 'Alice';
```

That query pulls data from three tables in a single statement to answer "What classes is Alice enrolled in?" This lesson is about understanding exactly how that works.

A **JOIN** combines rows from two tables based on a matching column — typically a foreign key equal to a primary key. The `REFERENCES` constraints you wrote in your schema aren't just for data integrity — they define the connections that JOINs traverse.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Setup](#setup)
- [One-to-Many: Users and Posts](#one-to-many-users-and-posts)
- [INNER JOIN](#inner-join)
  - [INNER JOIN Syntax](#inner-join-syntax)
  - [INNER JOIN Practice](#inner-join-practice)
- [LEFT JOIN](#left-join)
  - [When INNER JOIN Is Not Enough](#when-inner-join-is-not-enough)
  - [LEFT JOIN Practice](#left-join-practice)
- [Many-to-Many Relationships](#many-to-many-relationships)
  - [Association Tables and Three-Way JOINs](#association-tables-and-three-way-joins)
- [GROUP BY with JOINs](#group-by-with-joins)
  - [Smart GROUP BY with Primary Keys](#smart-group-by-with-primary-keys)
- [Challenge: JOIN Queries](#challenge-join-queries)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What does `INNER JOIN` do? What rows does it include, and what does it exclude?
2. What is the difference between `INNER JOIN` and `LEFT JOIN`? When do you use each?
3. How do you use `GROUP BY` in a JOIN query? Why do you only need to `GROUP BY` the primary key in Postgres?
4. What is a many-to-many relationship? How do you query one using an association table?

## Key Concepts

* **`INNER JOIN`** — returns only rows where both tables have a matching value on the join condition. Rows with no match on either side are excluded.
* **`LEFT JOIN`** — returns all rows from the *left* table. Rows with no match in the right table have `NULL` for all right-side columns.
* **Join condition** — the `ON` clause specifying which columns must match, usually a foreign key equaling a primary key.
* **Many-to-many** — a relationship where each row in table A can relate to many rows in table B, and vice versa. Represented in SQL with an association table.
* **Association/junction table** — a table that represents a many-to-many relationship by holding (at minimum) two foreign keys, one for each side.

## Setup

The follow-along repo includes a `setup.sql` file. Create a `social_db` database and then run `setup.sql` to create the `users`, `posts`, `tags`, and `post_tags` tables pre-loaded with sample data.

{% tabs %}
{% tab title="Mac" %}
```sh
# Create the database
createdb social_db

# Run the setup SQL file
psql -f setup.sql

# Connect to social_db
psql social_db
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
# Create the database
sudo -u postgres createdb social_db

# Run the setup SQL file
sudo -u postgres psql -f setup.sql

# Connect to social_db
sudo -u postgres psql social_db
```
{% endtab %}
{% endtabs %}

## One-to-Many: Users and Posts

Consider the "flat" table of users and the posts they've made:

| post_id | title             | user_id | username  | email              |
| ------- | ----------------- | ------- | --------- | ------------------ |
| 1       | My First Post     | 1       | ann_duong | ann@example.com    |
| 2       | Learning SQL      | 1       | ann_duong | ann@example.com    |
| 3       | Postgres Tips     | 2       | reuben_o  | reuben@example.com |
| 4       | Why I Love Coding | 3       | carmen_s  | carmen@example.com |
| 5       | Advanced Joins    | 2       | reuben_o  | reuben@example.com |

With this table, we can answer questions like:

- What posts did `ann_duong` write?
- Who wrote the post titled `'Advanced Joins'`?
- How many posts has each user written?

**<details><summary>Q: What is the problem with storing all of this data in a single flat table instead of two separate tables?</summary>**

A flat table like this breaks the normalization rule requiring every column to be dependent on the primary key. `username` and `email` are dependent on the `user_id`, not on the `post_id`. The following issues inefficiencies:
- `username` and `email` would be duplicated on every post row for the same user
- If a user changes their email, you'd need to update every post they've ever written
- A user with no posts would have no row at all, or a row with `NULL` post columns

Splitting into two tables eliminates all of this. The user's info lives in one row in `users`. Posts reference it via `user_id`. Change the email once — all posts see the update automatically through the foreign key.

</details>

To create a more normalized database schema, we can split the data into two tables: `users` and `posts`. These tables have a **one-to-many** relationship: one user can author many posts. The `posts` table holds a `user_id` foreign key that references `users.user_id`.

**`users` table:**

| user_id | username  | email              |
| ------- | --------- | ------------------ |
| 1       | ann_duong | ann@example.com    |
| 2       | reuben_o  | reuben@example.com |
| 3       | carmen_s  | carmen@example.com |
| 4       | ben_s     | ben@example.com    |

**`posts` table:**

| post_id | title             | user_id |
| ------- | ----------------- | ------- |
| 1       | My First Post     | 1       |
| 2       | Learning SQL      | 1       |
| 3       | Postgres Tips     | 2       |
| 4       | Why I Love Coding | 3       |
| 5       | Advanced Joins    | 2       |

Notice: `ben_s` (user_id 4) has no posts. This will matter when we compare INNER JOIN and LEFT JOIN.

With two separate tables, you can easily answer questions about each one independently:

```sql
-- Who are all the users?
SELECT * FROM users;

-- What posts exist?
SELECT * FROM posts;
```

But what about those questions that cross the boundary between tables?

- What posts did `ann_duong` write?
- Who wrote the post titled `'Advanced Joins'`?
- How many posts has each user written?

None of these can be answered from a single table. `posts` knows the `user_id` of each post's author, but not their username — that lives in `users`. To connect them, you need a JOIN.

There are six different kinds of `JOIN` queries in SQL but the two most commonly used are:
* `INNER JOIN`
* `LEFT JOIN` (and `RIGHT JOIN`)

## INNER JOIN

### INNER JOIN Syntax

`INNER JOIN` combines rows from two tables where the join condition is true. Rows with no match on either side are excluded from the result.

For example, we can see all users who made a post, excluding those who did not.

```sql
SELECT *
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id;
```

The `ON` keyword defines the join condition.

Result:

| user_id | username  | email              | post_id | title             | user_id |
| ------- | --------- | ------------------ | ------- | ----------------- | ------- |
| 1       | ann_duong | ann@example.com    | 1       | My First Post     | 1       |
| 1       | ann_duong | ann@example.com    | 2       | Learning SQL      | 1       |
| 2       | reuben_o  | reuben@example.com | 3       | Postgres Tips     | 2       |
| 3       | carmen_s  | carmen@example.com | 4       | Why I Love Coding | 3       |
| 2       | reuben_o  | reuben@example.com | 5       | Advanced Joins    | 2       |

`ben_s` does not appear — they have no posts, so there is no matching row in `posts`.

Select specific columns and qualify names with `table.column` when both tables share a column name:

```sql
SELECT
  users.username,
  posts.post_id,
  posts.title
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id;
```

Result:

| username  | post_id | title             |
| --------- | ------- | ----------------- |
| ann_duong | 1       | My First Post     |
| ann_duong | 2       | Learning SQL      |
| reuben_o  | 3       | Postgres Tips     |
| carmen_s  | 4       | Why I Love Coding |
| reuben_o  | 5       | Advanced Joins    |

{% hint style="info" %}
`JOIN` without a type is an alias for `INNER JOIN`. Always write `INNER JOIN` explicitly — the type is part of the meaning. When you learn `LEFT JOIN`, `RIGHT JOIN`, and `FULL JOIN`, you'll already be in the habit of naming the type on every join.
{% endhint %}

### INNER JOIN Practice

For each of the questions below, use a SQL query with `INNER JOIN` and specific column names to find the answer:

**<details><summary>Q: What are the titles of all posts written by `ann_duong`?</summary>**

```sql
SELECT posts.title
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id
WHERE users.username = 'ann_duong';
```

</details>

**<details><summary>Q: Which user wrote the post titled `'Advanced Joins'`?</summary>**

```sql
SELECT users.username
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id
WHERE posts.title = 'Advanced Joins';
```

</details>

**<details><summary>Q: How many posts has `reuben_o` written?</summary>**

```sql
SELECT COUNT(*) AS post_count
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id
WHERE users.username = 'reuben_o';
```

</details>

## LEFT JOIN

### When INNER JOIN Is Not Enough

`INNER JOIN` only returns rows where both tables have a match. Users with no posts are silently excluded. Sometimes that's what you want — but when you need to include everyone regardless of whether they have related rows, use `LEFT JOIN`.

`LEFT JOIN` returns **all rows from the left table**. For rows with no match in the right table, the right-side columns come back as `NULL`.

```sql
SELECT
  users.username,
  posts.post_id,
  posts.title
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id;
```

Result:

| username  | post_id | title             |
| --------- | ------- | ----------------- |
| ann_duong | 1       | My First Post     |
| ann_duong | 2       | Learning SQL      |
| reuben_o  | 3       | Postgres Tips     |
| carmen_s  | 4       | Why I Love Coding |
| reuben_o  | 5       | Advanced Joins    |
| ben_s     | NULL    | NULL              |

`ben_s` now appears with `NULL` for all `posts` columns.

**<details><summary>Q: How would you use a `LEFT JOIN` to find all users who have *not* written any posts?</summary>**

When a `LEFT JOIN` finds no match in the right table, the right-side columns are `NULL`. Filter for exactly those rows:

```sql
SELECT users.username
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
WHERE posts.post_id IS NULL;
```

`posts.post_id IS NULL` is true only for users with no matching post row.

</details>

### LEFT JOIN Practice

**<details><summary>Q: Write a query that returns every user's `username` and their post count, including users with zero posts. Order by post count descending.</summary>**

```sql
SELECT users.username, COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id
ORDER BY post_count DESC;
```

`COUNT(posts.post_id)` counts non-NULL values, so users with no posts get `0`. Using `COUNT(*)` instead would count the NULL row as 1, giving the wrong answer for users with no posts.

</details>

## Many-to-Many Relationships

### Association Tables and Three-Way JOINs

Some relationships are many-to-many. In `social_db`, a post can have many tags, and a tag can appear on many posts. This is the same pattern as `enrollments` connecting `students` and `classes` in the previous lesson — a many-to-many relationship needs an association table because there's no place to put a single foreign key that would capture both sides.

The four tables in `social_db` and how they connect:

- `users` — one row per user; independent table
- `posts` — one row per post; references `users` via `user_id` (the one-to-many you've been working with)
- `tags` — one row per tag (`sql`, `databases`, `beginner`, etc.); independent table
- `post_tags` — one row per post-tag pairing; holds `post_id` and `tag_id` as foreign keys (the association table)

**`tags` table:**

| tag_id | name       |
| ------ | ---------- |
| 1      | javascript |
| 2      | sql        |
| 3      | databases  |
| 4      | beginner   |

**`post_tags` table:**

| post_tag_id | post_id | tag_id |
| ----------- | ------- | ------ |
| 1           | 1       | 4      |
| 2           | 2       | 2      |
| 3           | 2       | 3      |
| 4           | 3       | 2      |
| 5           | 3       | 3      |
| 6           | 5       | 2      |

To query across a many-to-many relationship, JOIN through the association table:

```sql
-- All tags on a specific post
SELECT posts.title, tags.name AS tag
FROM posts
  INNER JOIN post_tags ON posts.post_id = post_tags.post_id
  INNER JOIN tags ON post_tags.tag_id = tags.tag_id
WHERE posts.title = 'Learning SQL';
```

```sql
-- All posts with a given tag, including the author's username
SELECT posts.title, users.username
FROM posts
  INNER JOIN post_tags ON posts.post_id = post_tags.post_id
  INNER JOIN tags      ON post_tags.tag_id = tags.tag_id
  INNER JOIN users     ON posts.user_id = users.user_id
WHERE tags.name = 'sql';
```

**<details><summary>Q: Why can't you represent a many-to-many relationship with just a foreign key on one of the two main tables?</summary>**

A foreign key column holds one value per row — it can reference exactly one row in the other table. If you put `tag_id` on `posts`, a post could only have one tag. If you put `post_id` on `tags`, a tag could only belong to one post.

Neither captures "many on both sides." The association table solves this by giving each post-tag pairing its own row, allowing any post to have any number of tags and any tag to appear on any number of posts.

</details>

## GROUP BY with JOINs

You already know `GROUP BY` from the aggregates lesson. In a JOIN query, `GROUP BY` lets you produce per-entity summaries — for example, counting posts per user.

```sql
SELECT
  users.username,
  COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id
ORDER BY post_count DESC;
```

### Smart GROUP BY with Primary Keys

In standard SQL, every non-aggregate column in `SELECT` must appear in `GROUP BY`. That would mean listing both `users.user_id` and `users.username` in the clause.

Postgres relaxes this rule: **when you `GROUP BY` a primary key, you can include other columns from that same table in `SELECT` without listing them in `GROUP BY`**. Postgres knows that if you've grouped by `user_id`, `username` is uniquely determined by it — there can only be one username per group.

```sql
-- Standard SQL: must list every selected column
SELECT users.user_id, users.username, COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id, users.username;

-- Postgres smart GROUP BY: listing only the primary key is enough
SELECT users.user_id, users.username, COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id;
```

{% hint style="info" %}
The smart `GROUP BY` optimization applies to the table whose primary key you're grouping by. Columns from the *joined* table (e.g., `posts`) still need to be inside an aggregate function or explicitly listed in `GROUP BY`.
{% endhint %}

**<details><summary>Q: Write a query that returns each user's `user_id`, `username`, and `email` along with the number of posts they've written. Include users with zero posts, ordered by post count descending.</summary>**

```sql
SELECT
  users.user_id,
  users.username,
  users.email,
  COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id
ORDER BY post_count DESC;
```

Grouping by `users.user_id` (the primary key) allows `users.username` and `users.email` in `SELECT` without listing them in `GROUP BY`.

</details>

**<details><summary>Q: How many posts use the `'sql'` tag? Write the query.</summary>**

```sql
SELECT COUNT(DISTINCT post_tags.post_id) AS post_count
FROM tags
  INNER JOIN post_tags ON tags.tag_id = post_tags.tag_id
WHERE tags.name = 'sql';
```

Or using `GROUP BY`:

```sql
SELECT tags.name, COUNT(post_tags.post_id) AS post_count
FROM tags
  INNER JOIN post_tags ON tags.tag_id = post_tags.tag_id
GROUP BY tags.tag_id
HAVING tags.name = 'sql';
```

</details>

## Challenge: JOIN Queries

Write each query yourself before opening the solution.

**<details><summary>Q: List every user's `username` alongside the titles of their posts. Users with no posts should not appear.</summary>**

```sql
SELECT users.username, posts.title
FROM users
  INNER JOIN posts ON users.user_id = posts.user_id;
```

</details>

**<details><summary>Q: List every user's `username` and their post count. Include users with zero posts. Order by post count descending.</summary>**

```sql
SELECT users.username, COUNT(posts.post_id) AS post_count
FROM users
  LEFT JOIN posts ON users.user_id = posts.user_id
GROUP BY users.user_id
ORDER BY post_count DESC;
```

</details>

**<details><summary>Q: Which tags does the post `'Learning SQL'` have? Return only the tag names.</summary>**

```sql
SELECT tags.name
FROM posts
  INNER JOIN post_tags ON posts.post_id = post_tags.post_id
  INNER JOIN tags      ON post_tags.tag_id = tags.tag_id
WHERE posts.title = 'Learning SQL';
```

</details>

**<details><summary>Q: Which posts are tagged `'databases'`? Return the post title and the author's `username`.</summary>**

```sql
SELECT posts.title, users.username
FROM posts
  INNER JOIN post_tags ON posts.post_id = post_tags.post_id
  INNER JOIN tags      ON post_tags.tag_id = tags.tag_id
  INNER JOIN users     ON posts.user_id = users.user_id
WHERE tags.name = 'databases';
```

</details>

**<details><summary>Q: For each tag, how many posts use it? Include tags used on zero posts. Order by post count descending.</summary>**

```sql
SELECT tags.name, COUNT(post_tags.post_id) AS post_count
FROM tags
  LEFT JOIN post_tags ON tags.tag_id = post_tags.tag_id
GROUP BY tags.tag_id
ORDER BY post_count DESC;
```

</details>
