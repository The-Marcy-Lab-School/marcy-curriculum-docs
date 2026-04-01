# 5. Schema Design & Normalization

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-5-schema-design-and-normalization)!
{% endhint %}

You now know how to write SQL queries against existing tables. Before those tables can exist, someone has to design them. That process is **schema design** — deciding what tables to create, what columns they hold, and how they relate to each other.

Bad schema design is painful to undo once your application is built around it. A column that stores comma-separated values instead of a proper relationship, or a table that duplicates data that should live elsewhere, creates inconsistency and bugs that multiply over time. Getting the design right upfront is far easier than migrating a broken one later.

In this lesson, you'll follow a four-step design process, use DBML and [dbdiagram.io](https://dbdiagram.io) to generate Entity Relationship Diagrams, and normalize your schema to eliminate redundancy.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [What Is a Schema?](#what-is-a-schema)
  - [Why Design Before You Code](#why-design-before-you-code)
  - [The Four-Step Design Process](#the-four-step-design-process)
- [Database Markup Language and dbdiagram.io](#database-markup-language-and-dbdiagramio)
  - [Step 1 — Identify Tables](#step-1--identify-tables)
  - [Step 2 — Define Columns](#step-2--define-columns)
  - [Step 3 — Determine Relationships](#step-3--determine-relationships)
- [Practice](#practice)
- [Step 4 — Normalizing Tables](#step-4--normalizing-tables)
  - [First Normal Form (1NF)](#first-normal-form-1nf)
  - [Second Normal Form (2NF)](#second-normal-form-2nf)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is a database schema and why is it worth designing before writing any code?
2. What is an Entity Relationship Diagram (ERD) and what information does it communicate?
3. What are the four steps for designing a database schema?
4. What is normalization and what problems does it prevent?
5. What is the difference between a one-to-many and a many-to-many relationship? How is each represented in a relational database?

## Key Concepts

* **Schema** — the structure of a database: which tables exist, which columns each table has, the data types of those columns, and the constraints enforced on them.
* **Schema design** — the process of planning that structure before writing any code.
* **ERD (Entity Relationship Diagram)** — a visual diagram showing each table's columns and the relationships between tables.
* **DBML (Database Markup Language)** — a plain-text format used by tools like [dbdiagram.io](https://dbdiagram.io) to generate ERDs.
* **One-to-many** — a relationship where one row in table A can be referenced by many rows in table B (e.g., one user has many posts).
* **Many-to-many** — a relationship where rows in each table can reference many rows in the other (e.g., a student can enroll in many classes; a class can have many students).
* **Association/junction table** — a table that represents a many-to-many relationship by storing two foreign keys, one referencing each side.
* **Normalization** — the process of restructuring a schema to eliminate redundant data and ensure data consistency.
* **Normal forms** — a set of rules for normalization. First Normal Form (1NF) and Second Normal Form (2NF) address the most common problems.

## What Is a Schema?

> sche·ma /ˈskēmə/ (noun) — a representation of a plan in the form of an outline or model.

A database **schema** describes the structure of your database:

- Which tables exist (`users`, `posts`, `enrollments`...)
- Which columns each table has and their data types (`user_id SERIAL`, `username TEXT`...)
- Which constraints are enforced (`NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `REFERENCES`...)

When you write `CREATE TABLE`, you are declaring your schema in SQL. Schema design is the process of deciding *what* to declare before writing any code.

### Why Design Before You Code

Jumping straight into code without planning your schema leads to predictable problems:

- **Data redundancy** — the same fact (e.g., a username) is stored in multiple rows. When it changes, every copy must be updated — and if any are missed, the data becomes inconsistent.
- **Missing relationships** — you realize mid-build that a `posts` table needs a `user_id` column that was never added. Fixing this means writing a migration, backfilling existing rows, and updating every query that touches posts.
- **Team confusion** — without a shared diagram, engineers may write code that assumes different column names or table structures.

A schema ERD gives everyone on the team a single source of truth *before* anyone writes a line of application code.

**<details><summary>Q: You're two days into building an app and realize the `orders` table should have had a `customer_id` foreign key from the start. What does fixing this now require? What code is impacted by this change?</summary>**

1. Write a "migration" that updates the `orders` table with a `customer_id` column
2. Backfill existing rows with the correct `customer_id` values
3. Update every query, model, controller, and test that reads from `orders`

All of this is avoidable if the relationship was modeled into the schema before writing code.

</details>

### The Four-Step Design Process

1. **Identify tables** — what types of things does the app need to store?
2. **Define columns** — what properties does each entity have? What data types?
3. **Determine relationships** — how do the entities relate? One-to-many? Many-to-many?
4. **Normalize** — eliminate redundancy and inconsistency

## Database Markup Language and dbdiagram.io

**Database Markup Language (DBML)** is a plain-text format for describing a database schema. When you paste it into [dbdiagram.io](https://dbdiagram.io), it renders a visual ERD you can share with your team.

Open [dbdiagram.io](https://dbdiagram.io) and clear the editor — we'll design a school database from scratch.

**User stories for a school application:**

- We can see all students at the school
- We can see all teachers at the school
- We can see all courses available
- We can see which students are enrolled in which courses
- We can see which teacher is assigned to each course

### Step 1 — Identify Tables

**<details><summary>Q: Based on the user stories above, what tables do we need?</summary>**

- `students` — one row per student
- `teachers` — one row per teacher
- `classes` — one row per class offered
- `enrollments` — one row per student-class pairing (tracks which students are in which class)

</details>

In DBML, declare each table with its primary key:

```
Table students {
  student_id integer [pk]
}

Table teachers {
  teacher_id integer [pk]
}

Table classes {
  class_id integer [pk]
}

Table enrollments {
  enrollment_id integer [pk]
}
```

Paste this into dbdiagram.io to see four tables appear.

{% hint style="info" %}
Primary keys are named after their table: `student_id`, `teacher_id`, `class_id`. This avoids ambiguity when you JOIN multiple tables and see `student_id` alongside `teacher_id` in the same result set. Foreign keys use the exact same name as the primary key they reference — so a foreign key pointing to `students.student_id` is also called `student_id`.
{% endhint %}

### Step 2 — Define Columns

Add a name (in `lower_snake_case`) and a data type to each column:

```
Table students {
  student_id integer [pk]
  first_name varchar
  last_name  varchar
  dob        date
}

Table teachers {
  teacher_id integer [pk]
  first_name varchar
  last_name  varchar
}

Table classes {
  class_id   integer [pk]
  title      varchar
  teacher_id integer
}

Table enrollments {
  enrollment_id integer [pk]
  student_id    integer
  class_id      integer
}
```

### Step 3 — Determine Relationships

Relationships are declared with `Ref` in DBML. The `<` means one-to-many (smaller side is "one"); `-` means one-to-one.

```dbml
// One-to-one: each class has exactly one teacher
Ref: "classes"."teacher_id" - "teachers"."teacher_id"

// One-to-many: one class has many enrollments
Ref: "classes"."class_id" < "enrollments"."class_id"

// One-to-many: one student has many enrollments
Ref: "students"."student_id" < "enrollments"."student_id"
```

After adding these `Ref` lines, dbdiagram.io draws connecting lines between tables. Labels show cardinality: `1` for the "one" side and `*` for the "many" side.

The complete DBML for the school schema:

```
Table students {
  student_id integer [pk]
  first_name varchar
  last_name  varchar
  dob        date
}

Table teachers {
  teacher_id integer [pk]
  first_name varchar
  last_name  varchar
}

Table classes {
  class_id   integer [pk]
  title      varchar
  teacher_id integer
}

Table enrollments {
  enrollment_id integer [pk]
  student_id    integer
  class_id      integer
}

Ref: "classes"."teacher_id"  - "teachers"."teacher_id"
Ref: "classes"."class_id"    < "enrollments"."class_id"
Ref: "students"."student_id" < "enrollments"."student_id"
```

**<details><summary>Q: The `enrollments` table connects `students` and `classes`. What kind of relationship does this create between those two tables, and why?</summary>**

A **many-to-many** relationship. A student can be enrolled in many classes (many rows in `enrollments` with the same `student_id`), and a class can have many students (many rows with the same `class_id`).

`enrollments` is an **association/junction table** — it exists specifically to represent this many-to-many relationship. Its rows contain only foreign keys (plus its own primary key) and carry no other independent data.

</details>

## Practice

In groups, use [dbdiagram.io](https://dbdiagram.io) to design a schema for one of the following. Follow all four steps and generate a full ERD before writing any SQL.

- An online store (users, products, orders, order items)
- A photo-sharing app with comments (users, photos, comments)
- A restaurant reservation system (customers, restaurants, tables, reservations)
- A job board (companies, job listings, applicants, applications)

For each table:
- Name the primary key after the table (`company_id` in `companies`, etc.)
- Use the same name as the primary key in any foreign key column that references it
- Identify whether each relationship is one-to-many or many-to-many

## Step 4 — Normalizing Tables

**Normalization** is the process of restructuring a schema to eliminate redundant data and keep data consistent. The rules that guide it are called **normal forms**. The first two cover the most common design problems.

### First Normal Form (1NF)

A table is in **First Normal Form** if:

- Every cell contains a single, indivisible value — no comma-separated lists
- Every column has a unique name
- Row order does not carry meaning

The most common violation is storing multiple values in one cell:

| order_id | customer_name | products                |
| -------- | ------------- | ----------------------- |
| 1        | Avery         | Laptop, Mouse, Keyboard |
| 2        | Blake         | Laptop, Monitor         |
| 3        | Charles       | Monitor, Trackpad       |

Problems with this:
- **Querying is awkward** — "find all customers who ordered a Laptop" requires string parsing rather than a `WHERE` clause
- **Updates are fragile** — renaming a product means searching inside comma-separated strings and hoping every occurrence is caught
- **No foreign keys possible** — you cannot reference a product that lives inside a string

Fix it by giving each value its own row:

| order_id | customer_name | product  |
| -------- | ------------- | -------- |
| 1        | Avery         | Laptop   |
| 1        | Avery         | Mouse    |
| 1        | Avery         | Keyboard |
| 2        | Blake         | Laptop   |
| 2        | Blake         | Monitor  |
| 3        | Charles       | Monitor  |
| 3        | Charles       | Trackpad |

**<details><summary>Q: The table below violates 1NF. How do you know, and how would you fix it?</summary>**

| student_id | student_name | courses                       |
| ---------- | ------------ | ----------------------------- |
| 1          | Alice        | Math, Science, History        |
| 2          | Bob          | Science, English, Mathematics |
| 3          | Carol        | History, Math, English        |

The `courses` column stores multiple values in one cell, violating the atomic-value rule.

**Fix:** Create a separate row for each student-course pair. A new `enrollment_id` primary key is needed because `student_id` alone no longer uniquely identifies each row:

| enrollment_id | student_id | course  |
| ------------- | ---------- | ------- |
| 1             | 1          | Math    |
| 2             | 1          | Science |
| 3             | 1          | History |
| 4             | 2          | Science |
| 5             | 2          | English |
| ...           | ...        | ...     |

</details>

### Second Normal Form (2NF)

A table is in **Second Normal Form** if:

- It is already in 1NF
- Every non-key column depends entirely on the primary key — not on some other non-key column

The most common violation: a column whose value is determined by *another column* rather than the primary key. This forces the same fact to repeat across many rows.

This `order_details` table violates 2NF:

| order_detail_id | order_id | product_id | product_name | customer_id | customer_name |
| --------------- | -------- | ---------- | ------------ | ----------- | ------------- |
| 1               | 1        | 1          | Laptop       | 1           | Avery         |
| 2               | 1        | 2          | Monitor      | 1           | Avery         |
| 3               | 2        | 1          | Laptop       | 2           | Blake         |
| 4               | 3        | 3          | Trackpad     | 3           | Charles       |

The violations:
- `product_name` depends on `product_id`, not on `order_detail_id`
- `customer_name` depends on `customer_id`, not on `order_detail_id`

If "Laptop" is renamed "Laptop Pro", every row containing `product_id = 1` must be updated. Miss one and the data is inconsistent.

Fix by extracting each dependency into its own table:

**`products`**

| product_id | product_name |
| ---------- | ------------ |
| 1          | Laptop       |
| 2          | Monitor      |
| 3          | Trackpad     |

**`customers`**

| customer_id | customer_name |
| ----------- | ------------- |
| 1           | Avery         |
| 2           | Blake         |
| 3           | Charles       |

**`orders`**

| order_id | customer_id |
| -------- | ----------- |
| 1        | 1           |
| 2        | 2           |
| 3        | 3           |

**`order_items`**

| order_item_id | order_id | product_id |
| ------------- | -------- | ---------- |
| 1             | 1        | 1          |
| 2             | 1        | 2          |
| 3             | 2        | 1          |
| 4             | 3        | 3          |

Now `product_name` lives in exactly one row in `products`. Rename "Laptop" once — every `order_items` row referencing `product_id = 1` reflects the update automatically.

**<details><summary>Q: The table below violates 2NF. How do you know, and how would you fix it?</summary>**

| enrollment_id | student_id | student_name | course  |
| ------------- | ---------- | ------------ | ------- |
| 1             | 1          | Alice        | Math    |
| 2             | 1          | Alice        | Science |
| 3             | 1          | Alice        | History |
| 4             | 2          | Bob          | Science |
| 5             | 2          | Bob          | English |
| 6             | 3          | Carol        | History |

`student_name` depends on `student_id`, not on `enrollment_id`. Alice's name is repeated on every enrollment row — if she changes her name, every row must be updated.

**Fix:** Pull `student_name` into a `students` table:

**`students`**

| student_id | student_name |
| ---------- | ------------ |
| 1          | Alice        |
| 2          | Bob          |
| 3          | Carol        |

**`enrollments`**

| enrollment_id | student_id | course  |
| ------------- | ---------- | ------- |
| 1             | 1          | Math    |
| 2             | 1          | Science |
| 3             | 1          | History |
| 4             | 2          | Science |
| 5             | 2          | English |
| 6             | 3          | History |

</details>
