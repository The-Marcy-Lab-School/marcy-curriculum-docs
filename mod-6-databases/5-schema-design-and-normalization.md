# 5. Schema Design & Normalization

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-5-schema-design-and-normalization)!
{% endhint %}

You now know how to write SQL queries against existing tables. Before those tables can exist, someone has to design them. That process is **schema design** — deciding what tables to create, what columns they hold, and how they relate to each other.

In this lesson, you'll learn what normalization means, why it matters, and how to apply it as you design a schema using DBML and [dbdiagram.io](https://dbdiagram.io).

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [What is Schema Design?](#what-is-schema-design)
- [Normalization](#normalization)
  - [Normalization Rule #1: Unique Primary Keys](#normalization-rule-1-unique-primary-keys)
  - [Normalization Rule #2: Atomic Values](#normalization-rule-2-atomic-values)
  - [Normalization Rule #3: Primary Key Dependency](#normalization-rule-3-primary-key-dependency)
  - [Normalization Rules Challenge](#normalization-rules-challenge)
- [ERDs: Design Before You Code](#erds-design-before-you-code)
  - [The Three-Step Design Process](#the-three-step-design-process)
  - [Step 1 — Identify Tables](#step-1--identify-tables)
  - [Step 2 — Define Columns](#step-2--define-columns)
  - [Step 3 — Determine Relationships](#step-3--determine-relationships)
  - [See It in Action](#see-it-in-action)
- [Practice](#practice)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is normalization and what problems does it prevent?
2. What are the three rules of normalization? What problem does each one solve?
3. What is a database schema and why is it worth designing before writing any code?
4. What is an Entity Relationship Diagram (ERD) and what information does it communicate?
5. What is the difference between a one-to-many and a many-to-many relationship? How is each represented in a relational database?

## Key Concepts

* **Normalization** — the practice of structuring a database so that every fact is stored in exactly one place, eliminating redundancy and keeping data consistent.
* **Normal forms** — a set of rules for normalization. The three rules covered in this lesson address the most common design problems: unique primary keys, atomic values, and primary key dependency.
* **Schema** — the structure of a database: which tables exist, which columns each table has, the data types of those columns, and the constraints enforced on them.
* **Schema design** — the process of planning that structure before writing any code.
* **ERD (Entity Relationship Diagram)** — a visual diagram showing each table's columns and the relationships between tables.
* **DBML (Database Markup Language)** — a plain-text format used by tools like [dbdiagram.io](https://dbdiagram.io) to generate ERDs.
* **One-to-many** — a relationship where one row in table A can be referenced by many rows in table B (e.g., one user has many posts).
* **Many-to-many** — a relationship where rows in each table can reference many rows in the other (e.g., a student can enroll in many classes; a class can have many students).
* **Association/junction table** — a table that represents a many-to-many relationship by storing two foreign keys, one referencing each side.

## What is Schema Design?

> sche·ma /ˈskēmə/ (noun) — a representation of a plan in the form of an outline or model.

A database **schema** describes the structure of your database:

- Which tables exist (`users`, `posts`, `enrollments`...)
- Which columns each table has and their data types (`user_id SERIAL`, `username TEXT`...)
- Which constraints are enforced (`NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `REFERENCES`...)

When you write `CREATE TABLE`, you are declaring your schema in SQL. **Schema design** is the process of deciding *what* to declare before writing any application code.

## Normalization

The foundation of every well-designed database are the rules of **normalization**: a set of guiding principles that help us avoid two common issues with database design:
1. **Data Corruption** — the data becomes inaccurate and inconsistent
2. **Data Redundancy** — values are repeated unnecessarily or appear multiple times

Understanding the principles of normalization will allow you to defend and explain the *why* behind every decision you make in your schema design.

Consider the database table below which stores data for orders from an electronics store:

| order_id | customer_id | customer_name | customer_address     | products_purchased                      |
| -------- | ----------- | ------------- | -------------------- | --------------------------------------- |
| 1        | 1           | Avery         | 12 Apple Avenue      | Roku TV, Apple Macbook, Nintendo Switch |
| 2        | 2           | Blake         | 34 Banana Boulevard  | Roku TV, Nintendo Switch                |
| 3        | 3           | Charles       | 56 Cherry Court      | Roku TV, Apple Macbook                  |
| 4        | 3           | Charles       | 78 Strawberry Street | Nintendo Switch                         |

**<details><summary>Q: Where do you see corrupted (inaccurate and/or inconsistent) data? Where do you see redundant data?</summary>**

As you can see, Charles updated their address between orders 3 and 4. This data is now inconsistent making it difficult to accurately answer the question "What is Charles' address?"

```sql
-- This will return two rows that tell us conflicting information!
SELECT customer_address FROM orders WHERE customer_id = 3;
```
In addition, the `customer_name` column is redundant. The `customer_id` column uniquely identifies the customer on its own making the `customer_name` column unnecessary and opens the door for potential data corruption.

</details>

### Normalization Rule #1: Unique Primary Keys

The fundamental rule of all relational databases is the existence of a primary key: a column that uniquely identifies each row in a table. 

Primary keys also allow for the existence of foreign keys: columns in tables that reference the primary key of another table.

![A table showing primary keys and foreign keys.](./img/1-intro-to-databases-postgres/primary-foreign-keys.png)

### Normalization Rule #2: Atomic Values

Atomicity ("being like an atom") means to that every cell must contains a single, indivisible value — no comma-separated lists, no arrays, no packed strings. A cell that follows this rule is "atomic".

Consider this `orders` table again

| order_id | customer_id | customer_name | customer_address     | products_purchased                      |
| -------- | ----------- | ------------- | -------------------- | --------------------------------------- |
| 1        | 1           | Avery         | 12 Apple Avenue      | Roku TV, Apple Macbook, Nintendo Switch |
| 2        | 2           | Blake         | 34 Banana Boulevard  | Roku TV, Nintendo Switch                |
| 3        | 3           | Charles       | 56 Cherry Court      | Roku TV, Apple Macbook                  |
| 4        | 3           | Charles       | 78 Strawberry Street | Nintendo Switch                         |

Storing multiple values in the `products_purchased` column feels convenient, but it creates real problems:

- **Updates are fragile** — renaming a product means hunting inside comma-separated strings across every row to make the change
- **Querying is awkward** — "find all orders that include a Laptop" requires string parsing (this can't be done easily with a `WHERE` clause)

We can fix this by giving each `product_purchased` value its own row:

| order_id | customer_id | customer_name | customer_address     | product_purchased |
| -------- | ----------- | ------------- | -------------------- | ----------------- |
| 1        | 1           | Avery         | 12 Apple Avenue      | Roku TV           |
| 1        | 1           | Avery         | 12 Apple Avenue      | Apple Macbook     |
| 1        | 1           | Avery         | 12 Apple Avenue      | Nintendo Switch   |
| 2        | 2           | Blake         | 34 Banana Boulevard  | Roku TV           |
| 2        | 2           | Blake         | 34 Banana Boulevard  | Nintendo Switch   |
| 3        | 3           | Charles       | 56 Cherry Court      | Roku TV           |
| 3        | 3           | Charles       | 56 Cherry Court      | Apple Macbook     |
| 4        | 3           | Charles       | 78 Strawberry Street | Nintendo Switch   |

**<details><summary>Q: This solution introduces a new issue, can you see it?</summary>**

The primary key `order_id` is no longer unique! Multiple rows share the same primary key value. Additionally, we have way more repetition in our data!

Technically, we can uniquely identify each row if we treat the combination of the `order_id` and the `product_purchased` as a **composite key**. Composite keys aren't strictly a bad practice but can be inconvenient to work with. A single unique primary key is much better!

</details>

### Normalization Rule #3: Primary Key Dependency

We now need a single, unique, primary key to identify each row in the table to satisfy the first normalization rule. We can do this by adding a column called `order_product_id`:

| order_product_id | order_id | customer_id | customer_name | customer_address     | product_purchased |
| ---------------- | -------- | ----------- | ------------- | -------------------- | ----------------- |
| 1                | 1        | 1           | Avery         | 12 Apple Avenue      | Roku TV           |
| 2                | 1        | 1           | Avery         | 12 Apple Avenue      | Apple Macbook     |
| 3                | 1        | 1           | Avery         | 12 Apple Avenue      | Nintendo Switch   |
| 4                | 2        | 2           | Blake         | 34 Banana Boulevard  | Roku TV           |
| 5                | 2        | 2           | Blake         | 34 Banana Boulevard  | Nintendo Switch   |
| 6                | 3        | 3           | Charles       | 56 Cherry Court      | Roku TV           |
| 7                | 3        | 3           | Charles       | 56 Cherry Court      | Apple Macbook     |
| 8                | 4        | 3           | Charles       | 78 Strawberry Street | Nintendo Switch   |

However, this new table breaks the third rule of normalization: every column must depend solely on the primary key — not on some other column.

**<details><summary>Q: Which columns are dependent on a non-primary key column? Look for rows that have pairs of columns whose values are always the same.</summary>**

Both `customer_name` and `customer_address` depend on the on `customer_id` (every row with `customer_id=1` has `customer_name=Avery` and `customer_address='12 Apple Avenue'`)

Similarly, the `customer_id` value is dependent on `order_id` (every row with `order_id=1` has `customer_id=1`).

None of these columns depend on `order_product_id`.

</details>

The existence of non-primary key dependencies creates repetitive/redundant data and opportunities for data corruption:

* If Charles (`customer_id=3`) changes their address in the database, then every row with `customer_id=3` must also be changed. Miss one and the data is inconsistent. We can see that this already happened!
* An order can only ever have one customer attached to it making it redundant to list the customer next to the order when listing out the products in an order.

We fix this by extracting each dependency into its own table:

**`customers`** tells us details about each customer and nothing else

| customer_id | customer_name | customer_address     |
| ----------- | ------------- | -------------------- |
| 1           | Avery         | 12 Apple Avenue      |
| 2           | Blake         | 34 Banana Boulevard  |
| 3           | Charles       | 78 Strawberry Street |

**`orders`** tells us who placed each order

| order_id | customer_id |
| -------- | ----------- |
| 1        | 1           |
| 2        | 2           |
| 3        | 3           |
| 4        | 3           |

**`order_products`** tells us which products were purchased in each order

| order_product_id | order_id | product_purchased |
| ---------------- | -------- | ----------------- |
| 1                | 1        | Roku TV           |
| 2                | 1        | Apple Macbook     |
| 3                | 1        | Nintendo Switch   |
| 4                | 2        | Roku TV           |
| 5                | 2        | Nintendo Switch   |
| 6                | 3        | Roku TV           |
| 7                | 3        | Apple Macbook     |
| 8                | 4        | Nintendo Switch   |

Now, when Charles updates his address, the change only happens in one place and we've eliminated rows with duplicate data.

### Normalization Rules Challenge

Consider this `employees` table. Which normalization rules does it break? How would you fix it?

| employee_id | employee_name | department_id | department_name | project_names               |
| ----------- | ------------- | ------------- | --------------- | --------------------------- |
| 1           | Ana           | 10            | Engineering     | Website Redesign, API Audit |
| 2           | Ben           | 10            | Engineering     | API Audit                   |
| 3           | Cara          | 20            | Marketing       | Brand Refresh               |
| 4           | Dan           | 20            | Marketing       | Brand Refresh, Social Media |

**<details><summary>Which rules does this table break?</summary>**

**Rule #2 — Atomic Values:** `project_names` stores multiple values in a single cell (e.g., `"Website Redesign, API Audit"`). Each project must get its own row.

**Rule #3 — Primary Key Dependency:** `department_name` depends on `department_id`, not on `employee_id`. If the Engineering department is renamed, every Engineering employee's row must be updated — and it's easy to miss one.

</details>

**<details><summary>How would you fix it?</summary>**

Extract each dependency into its own table and use a junction table for the many-to-many relationship between employees and projects.

**`departments`**

| department_id | department_name |
| ------------- | --------------- |
| 10            | Engineering     |
| 20            | Marketing       |

**`employees`**

| employee_id | employee_name | department_id |
| ----------- | ------------- | ------------- |
| 1           | Ana           | 10            |
| 2           | Ben           | 10            |
| 3           | Cara          | 20            |
| 4           | Dan           | 20            |

**`projects`**

| project_id | project_name     |
| ---------- | ---------------- |
| 1          | Website Redesign |
| 2          | API Audit        |
| 3          | Brand Refresh    |
| 4          | Social Media     |

**`employee_projects`** (junction table)

| employee_project_id | employee_id | project_id |
| ------------------- | ----------- | ---------- |
| 1                   | 1           | 1          |
| 2                   | 1           | 2          |
| 3                   | 2           | 2          |
| 4                   | 3           | 3          |
| 5                   | 4           | 3          |
| 6                   | 4           | 4          |

Now `department_name` lives in exactly one place — rename Engineering once and every employee inherits it automatically. And each project assignment gets its own row with its own primary key, making queries like "find all employees on the API Audit project" trivial.

</details>


## ERDs: Design Before You Code

Normalization is something that you can do to improve the design of an existing database. But it would be much better to use those principles from the start before you write any code or add any data to your database. 

The primary way to design a database schema from scratch is with an **Entity Relationship Diagram (ERD)**:

![An ERD maps contents of database tables (entities) and the relationships between them.](./img/5-schema-design-normalization/authors-books-erd.png)

Creating a diagram like this gives everyone a single source of truth *before* anyone writes a line of application code and serves as an easy-to-read visual representation of the tables, columns, and relationships within our database.

While you can draw ERDs by hand, we can also use software to create beautiful ERDs for us. 

**Database Markup Language (DBML)** is a plain-text format for describing a database schema. When you paste it into [dbdiagram.io](https://dbdiagram.io), it renders a visual ERD you can share with your team.

Try pasting the following into the dbdiagram editor:

```dbml
Table books {
  book_id integer [pk]
  title text
  author_id integer
  published_date date
}

Table authors {
  author_id integer [pk]
  first_name text
  last_name  text
  dob        date
}

// One-to-many: each author can have many books
Ref: "authors"."author_id" < "books"."author_id"
```

### The Three-Step Design Process

A normalized schema emerges naturally from following three steps:

1. **Identify tables** — what distinct types of things does the app need to store? Each type of entity that has its own properties and its own lifecycle gets its own table.
2. **Define columns** — what properties does each entity have? If a property could change independently of the others (like a product name), it belongs in its own table, not repeated as a column on a related table.
3. **Determine relationships** — how do the entities relate? If the relationship produces multiple values per row, you need a junction table (Rule #2). If a column's value is determined by a non-primary column, it needs to move to its own table (Rule #3).

Open [dbdiagram.io](https://dbdiagram.io) and clear the editor — we'll design a school database using the three steps above, with normalization driving every decision.

**User stories for a school application:**

- We can see all students at the school
- We can see all teachers at the school
- We can see all courses available
- We can see which students are enrolled in which courses
- We can see which teacher is assigned to each course

### Step 1 — Identify Tables

Each distinct entity — something with its own properties and its own existence — gets its own table.

**<details><summary>Q: Based on the user stories, what tables do we need?</summary>**

- `students` — a student exists independently; they have their own name, DOB, etc.
- `teachers` — a teacher exists independently; they have their own name, etc.
- `classes` — a class exists independently; it has a title and an assigned teacher
- `enrollments` — the relationship between students and classes needs its own table (a student can be in many classes, a class can have many students — this is a many-to-many, which means a junction table)

Without `enrollments`, the only alternative would be storing courses as a comma-separated list on the `students` table — a direct violation of atomicity.

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

</details>

### Step 2 — Define Columns

For each table, first define a primary key column. Primary keys are named after their table: `student_id`, `teacher_id`, `class_id`. Foreign keys use the exact same name as the primary key they reference.

Then, ask: which additional information is needed to answer my user story questions? Which table does each piece of information belong to? 

In DBML, you can list the name of the column followed by its type. If the column is a primary key, add `[pk]`. For example, here is the books table from before:

```
Table books {
  book_id integer [pk]
  title text
  author_id integer
  published_date date
}
```

**<details><summary>School Solution</summary>**
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
  teacher_id integer    -- foreign key, not teacher_name
}

Table enrollments {
  enrollment_id integer [pk]
  student_id    integer
  class_id      integer
}
```

Notice that `classes` has a `teacher_id` column — not `teacher_name`. A teacher's name belongs in the `teachers` table, determined by `teacher_id`. Putting `teacher_name` directly on `classes` would break Rule #3: the name depends on the teacher, not on the class. If the teacher changes their name, every class they teach would need to be updated.

</details>


### Step 3 — Determine Relationships

Lastly, consider what relationships exist between tables. Ask: is this one-to-many or many-to-many? 

Then, in DBML, use the following syntax for each type of relationship:
* one-to-one:  `Ref: "table1"."table1_id" - "table2"."table1_id"`
* one-to-many:  `Ref: "table1"."table1_id" < "table2"."table1_id"`

For example, **one** author can have **many** books:

```dbml
Table books {
  book_id integer [pk]
  title text
  author_id integer
  published_date date
}

Table authors {
  author_id integer [pk]
  first_name text
  last_name  text
  dob        date
}

// One-to-many: each author can have many books
Ref: "authors"."author_id" < "books"."author_id"
```


**<details><summary>School Solution</summary>**

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

</details>

**<details><summary>Q: Why does the `enrollments` table exist? What would go wrong if you tried to store enrollment data directly on the `students` table instead?</summary>**

A student can be enrolled in many classes and a class can have many students — this is a many-to-many relationship. There is no way to represent that with a single foreign key column on either table.

The only alternative would be storing courses as a comma-separated list (e.g., `courses = "Math, Science, History"`) directly on the `students` table. That breaks the rule of atomicity — multiple values in one cell. Querying it is awkward, updating it is fragile, and you can't enforce foreign keys to the `classes` table.

The `enrollments` junction table solves this by giving each student-class pairing its own row. This is normalization in practice: a relationship that produces multiple values per entity gets its own table.

</details>

### See It in Action

The follow-along repo includes a `school-setup.sql` file that translates this DBML design into SQL — `CREATE TABLE` statements with real data types, constraints, and foreign keys, plus seed data to query against.

Run it to create `school_db`:

{% tabs %}
{% tab title="Mac" %}
```sh
psql -f school-setup.sql
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
sudo -u postgres psql -f school-setup.sql
```
{% endtab %}
{% endtabs %}

Then connect and explore:

{% tabs %}
{% tab title="Mac" %}
```sh
psql school_db
```
{% endtab %}
{% tab title="Windows + WSL" %}
```sh
sudo -u postgres psql school_db
```
{% endtab %}
{% endtabs %}

```sql
-- Inspect a table's columns and constraints
\d students
\d enrollments

-- Query across the schema
SELECT * FROM students;
SELECT * FROM enrollments;

-- What classes is Alice enrolled in?
SELECT students.first_name, classes.title
FROM students
  INNER JOIN enrollments ON students.student_id = enrollments.student_id
  INNER JOIN classes     ON enrollments.class_id = classes.class_id
WHERE students.first_name = 'Alice';
```

Look at the `\d enrollments` output — the `REFERENCES` constraints are the SQL implementation of the foreign key relationships you drew in dbdiagram.io. The ERD is the plan; the SQL is the implementation; normalization is the reason the plan looks the way it does.

## Practice

In groups, use [dbdiagram.io](https://dbdiagram.io) to design a schema for one of the following. Generate a full ERD using all three steps, and be ready to explain every structural decision in terms of normalization.

- An online store (users, products, orders, order items)
- A photo-sharing app with comments (users, photos, comments)
- A restaurant reservation system (customers, restaurants, tables, reservations)
- A job board (companies, job listings, applicants, applications)

For each design decision, be able to answer:
- **Why is this a separate table?** (What redundancy or violation would occur if it weren't?)
- **Why does this column live here and not somewhere else?** (What does it depend on?)
- **Why does this relationship need a junction table?**

For each table:
- Name the primary key after the table (`company_id` in `companies`, etc.)
- Use the same name as the primary key in any foreign key column that references it
- Identify whether each relationship is one-to-many or many-to-many, and explain what that means for the schema
