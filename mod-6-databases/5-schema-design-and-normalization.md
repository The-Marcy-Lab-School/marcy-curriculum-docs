# 5. Schema Design & Normalization

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/6-5-schema-design-and-normalization)!
{% endhint %}

You now know how to write SQL queries against existing tables. Before those tables can exist, someone has to design them. That process is **schema design** — deciding what tables to create, what columns they hold, and how they relate to each other.

In this lesson, you'll learn what normalization means, why it matters, and how to translate a well-designed schema into SQL with foreign keys.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [What is Schema Design?](#what-is-schema-design)
- [Normalization](#normalization)
  - [Normalization Rule #1: Unique Primary Keys](#normalization-rule-1-unique-primary-keys)
  - [Normalization Rule #2: Atomic Values](#normalization-rule-2-atomic-values)
    - [Don't Forget The Primary Key!](#dont-forget-the-primary-key)
  - [Normalization Rule #3: Primary Key Dependency](#normalization-rule-3-primary-key-dependency)
    - [Association Tables for Many-To-Many Relationships](#association-tables-for-many-to-many-relationships)
- [Designing a Multi-Table Schema](#designing-a-multi-table-schema)
  - [Create Independent Tables First](#create-independent-tables-first)
  - [Use `REFERENCES` for Foreign Keys](#use-references-for-foreign-keys)
  - [Creation order matters](#creation-order-matters)
  - [`UNIQUE` Constraint on Multiple Columns](#unique-constraint-on-multiple-columns)
  - [Complete Schema](#complete-schema)
  - [Schema Investigation](#schema-investigation)
  - [See It in Action](#see-it-in-action)
- [Practice](#practice)
- [ERDs: Design Before You Code (Extension)](#erds-design-before-you-code-extension)
- [Normalization Rules Challenge](#normalization-rules-challenge)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is normalization and what problems does it prevent?
2. What are the three rules of normalization? What problem does each one solve?
3. What is a database schema and why is it worth designing before writing any code?
4. What is the difference between a one-to-many and a many-to-many relationship? How is each represented in a relational database?
5. How do you add a foreign key constraint to a `CREATE TABLE` statement? Why does the order in which you create tables matter?

## Key Concepts

* **Normalization** — the practice of structuring a database so that every fact is stored in exactly one place, eliminating redundancy and keeping data consistent.
* **Normal forms** — a set of rules for normalization. The three rules covered in this lesson address the most common design problems: unique primary keys, atomic values, and primary key dependency.
* **Schema** — the structure of a database: which tables exist, which columns each table has, the data types of those columns, and the constraints enforced on them.
* **Schema design** — the process of planning that structure before writing any code.
* **One-to-many** — a relationship where one row in table A can be referenced by many rows in table B (e.g., one user has many posts).
* **Many-to-many** — a relationship where rows in each table can reference many rows in the other (e.g., a student can enroll in many classes; a class can have many students).
* **Association/Bridge table** — a table that represents a many-to-many relationship by storing two foreign keys, one referencing each side.
* **ERD (Entity Relationship Diagram)** — a visual diagram showing each table's columns and the relationships between tables. *(See the Extension section.)*

## What is Schema Design?

> sche·ma /ˈskēmə/ (noun) — a representation of a plan in the form of an outline or model.

When you write `CREATE TABLE`, you are declaring your schema in SQL. Consider the SQL below that declares the schema for a database of authors and books:

```sql
CREATE TABLE authors (
  author_id SERIAL PRIMARY KEY,
  first_name TEXT NOT NULL,
  last_name TEXT NOT NULL,
  dob DATE
);

CREATE TABLE books (
  book_id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  published_date DATE,
  author_id INT NOT NULL REFERENCES authors(author_id),
);
```

With this database, we can answer questions like:
- When was the author Toni Morrison born?
- Who wrote the book Between the World and Me?
- How many books did Brandon Sanderson write?

Before you can create tables like this, you have to plan and decide what your schema will look like. **Schema design** is this planning process and involves asking:
- What kinds of questions do I want to be able to answer with my data?
- To answer those questions, which tables will need to exist and what columns will they have?
- What is the best data type for each column?
- Which constraints should be enforced for each column?
- What relationships exist between tables?

## Normalization

The foundation of every well-designed relational database are the guiding principles of **normalization**. A "normalized" database is one that avoids two common database issues:
1. **Data Corruption** — the data becomes inaccurate and inconsistent
2. **Data Redundancy** — values are repeated unnecessarily or appear multiple times

Understanding the principles of normalization will allow you to defend and explain the *why* behind every decision you make in your schema design.

Consider the database table below which stores data for orders from an electronics store:

![A table of orders with order_id, customer_id, customer_name, customer_address, products_purchased, and product_prices.](./img/5-schema-design-normalization/normalization-1.png)

**<details><summary>Q: Where do you see corrupted (inaccurate and/or inconsistent) data? Where do you see redundant data?</summary>**

As you can see, Charles updated their address between orders 3 and 4. This data is now inconsistent making it difficult to accurately answer the question "What is Charles' address?"

```sql
-- This will return two rows that tell us conflicting information!
SELECT customer_address FROM orders WHERE customer_id = 3;
```
In addition, the `customer_name` column is redundant. The `customer_id` column uniquely identifies the customer on its own making the `customer_name` column unnecessary and opens the door for potential data corruption.

When looking at `product_prices`, the data is very repetitive and if we wanted to update the price for a product, we would have to update it in every row.

</details>

There are a number of things wrong with this schema design. Let's fix them by applying the rules of normalization.

### Normalization Rule #1: Unique Primary Keys

The fundamental rule of all relational databases is that there must be a **primary key**: a column that uniquely identifies each row in a table. 

Primary keys also allow for the existence of foreign keys: columns in tables that reference the primary key of another table.

**<details><summary>Q: What is the primary key in the `orders` table? How do you know?</summary>**

`order_id` because it is unique for every single row.

</details>

### Normalization Rule #2: Atomic Values

Atomicity ("being like an atom") means that every cell must contains a single, indivisible value — no comma-separated lists, no arrays, no packed strings. A cell that follows this rule is "atomic".

Consider this `orders` table again and notice that both `products_purchased` and `product_prices` are *not* atomic. 

![A table of orders with order_id, customer_id, customer_name, customer_address, products_purchased, and product_prices.](./img/5-schema-design-normalization/normalization-1.png)

**<details><summary>Q: What difficulties could storing multiple values in one cell create?</summary>**
Storing multiple values in one column feels convenient, but it also makes the database harder to work with:
- **Updates are fragile**: The `product_prices` column is fully dependent on the `products_purchased` column so the order of values must match perfectly. If a customer decides to remove a product from their order, we also have to update the product prices list. If we forget to change both columns, or we make a mistake, it can be difficult to debug.
- **Querying is awkward**: If I want to know the price of a product, I have to query both columns, then use string parsing to separate both columns into parallel arrays, find the index of the product I'm looking for, then grab the matching price. What a headache!
</details>

We can fix this by giving each product in each order its own row:

![The same orders table where products_purchased and product_prices each have one value per row. Every cell is now atomic.](./img/5-schema-design-normalization/normalization-2.png)

We've satisfied the rule of atomicity! But we've caused another issue.

**<details><summary>Q: This solution introduces a new issue, can you see it?</summary>**

The primary key `order_id` is no longer unique! Multiple rows share the same primary key value. Additionally, we have way more repetition in our data!

Technically, we can uniquely identify each row if we treat the combination of the `order_id` and the `product_purchased` as a **composite key**. Composite keys aren't strictly a bad practice but can be inconvenient to work with. A single unique primary key is much better!

</details>

#### Don't Forget The Primary Key!

Remember, every table needs a single unique, primary key to identify each row. We *could* do this by adding a column called `order_product_id`:

{% hint style="danger" %}

![This breaks the third rule of normalization: every column must depend solely on the primary key.](./img/5-schema-design-normalization/normalization-3-bad.png)

Don't do this!

{% endhint %}

The issue with this design is that we now have a lot of redundant and repetitive data. This is caused by having too many columns that are dependent on non-primary key columns.

**<details><summary>Q: Which columns are dependent on a non-primary key column? Look for rows that have pairs of columns whose values are always the same.</summary>**

Both `customer_name` and `customer_address` depend on the on `customer_id` (the customer with `customer_id=1` should always have `customer_name=Avery` and `customer_address='12 Apple Avenue'`)

The `customer_id` value is dependent on `order_id` (every order with `order_id=1` should always have `customer_id=1`).

The `product_price` value is dependent on the `product_purchased` (every Roku TV should always cost 149.99)

None of these columns depend on the primary key `order_product_id`.

</details>

The existence of non-primary key dependencies creates repetitive/redundant data and opportunities for data corruption:

* If Charles (`customer_id=3`) changes their address in the database, then every row with `customer_id=3` must also be changed. Miss one and the data is inconsistent. We can see that this already happened!
* An order can only ever have one customer attached to it making it redundant to list the customer next to the order when listing out the products in an order.
* If the price changes for the Roku TV, we need to update the price in every single row where Roku TV appears.

### Normalization Rule #3: Primary Key Dependency

The third rule of normalization avoids redundancy and repetitive data by requiring that every column depend solely on the primary key — not on some other column.

We fix this by extracting each dependency into its own table:

* **`customers`** tells us details about each customer
* **`products`** tells us details about each product
* **`orders`** tells us who placed each order
* **`order_products`** tells us which products were purchased in each order

![The single "flat" table has been split into four tables](./img/5-schema-design-normalization/normalization-3-good.png)

This new schema has a number of improvements:
* Now, when Charles updates his address, the change only happens in one place. 
* The same is true if we need to update the price of a product.
* The `orders` table only needs to know the `customer_id`, not the name or address.

#### Association Tables for Many-To-Many Relationships

This database schema has a few interesting relationships now:
* **Customers --< Orders**: A customer can place many orders (one to many)
* **Orders >--< Products**: An order can contain main products AND a product can be in many orders (many to many)

The `orders` table is the representation of the **one-to-many** relationship between orders and customers. As you can see, each `order_id` corresponds with a specific `customer_id`, but the same `customer_id` can show up many times.

We can't add a `product` column to the `orders` table since each order can have many products. Instead, we create the `order_products` table to represent the **many-to-many** relationship between orders and products.

This kind of many-to-many table is also called a **bridge table** or **association table**.

## Designing a Multi-Table Schema

Now that we know the rules of normalization, we can apply them when designing a schema from scratch.

Let's practice by building a **school database**. With this database, we want to be able to answer these data questions:

- Which students attend the school?
- Which teachers work at the school?
- What courses are available at the school?
- Which teacher is assigned to each course?
- Which students are enrolled in which courses?

Take a moment now and consider:
- What "entities" or "things" exist in this database system? What kinds of relationships will exist between them?
  - → This will determine our tables, the need for foreign keys, and if we need any association/bridge tables.
- What data does each thing need to know to answer the data questions? 
  - → This will determine the columns in each table, their data types, and constraints.

**<details><summary>Q: What tables do we need? What are their relationships?</summary>**

- `students` — a student exists independently; they have their own name, DOB, etc.
- `teachers` — a teacher exists independently; they have their own name, etc.
- `classes` — a class exists independently; it has a title and an assigned teacher
- `enrollments` — the relationship between students and classes needs its own table. A student can be in many classes and a class can have many students — this is a many-to-many relationship, which requires an association table.

Without `enrollments`, the only alternative would be storing courses as a comma-separated list on the `students` table — a direct violation of atomicity.

</details>

**<details><summary>What columns does each table need?</summary>**

Remember, name primary keys after their table (`student_id`, `teacher_id`, `class_id`). Foreign keys use the exact same name as the primary key they reference.

- `students`: 
  - `student_id` [PK]
  - `first_name`
  - `last_name`
  - `dob`
- `teachers`: 
  - `teacher_id` [PK]
  - `first_name`
  - `last_name`
- `classes`: 
  - `class_id` [PK]
  - `title`
  - `teacher_id` [FK]
- `enrollments`: 
  - `enrollment_id` [PK]
  - `student_id` [FK]
  - `class_id` [FK]

</details>

### Create Independent Tables First

Now let's write the `CREATE TABLE` statements. Let's start with `students` and `teachers` since they exist on their own and won't need to reference other tables. The syntax should be familiar:

```sql
CREATE TABLE students (
  student_id  SERIAL PRIMARY KEY,
  first_name  TEXT   NOT NULL,
  last_name   TEXT   NOT NULL,
  dob         DATE
);

CREATE TABLE teachers (
  teacher_id  SERIAL PRIMARY KEY,
  first_name  TEXT   NOT NULL,
  last_name   TEXT   NOT NULL
);
```

### Use `REFERENCES` for Foreign Keys

`classes` needs a `teacher_id` column that points to a row in `teachers`. In SQL, you declare a foreign key with `REFERENCES`:

```sql
CREATE TABLE classes (
  class_id    SERIAL  PRIMARY KEY,
  title       TEXT    NOT NULL,
  teacher_id  INTEGER REFERENCES teachers(teacher_id)
);
```

### Creation order matters

`REFERENCES teachers(teacher_id)` tells Postgres to enforce the relationship at the database level — it will reject any insert into `classes` with a `teacher_id` that doesn't exist in `teachers`. This is called **referential integrity**.

Because of this enforcement, **referenced tables must exist before the tables that reference them**:

- `students` and `teachers` have no foreign keys — create them first
- `classes` references `teachers` so `teachers` must exist before `classes`
- `enrollments` references both `students` and `classes` so it should be created last

**Dropping tables follows the reverse order.** If you need to reset the schema, you must drop tables from most-dependent to least-dependent — the opposite of creation order. Dropping a parent table while a child still references it will fail with a foreign key constraint error:

```sql
-- Drop in reverse dependency order
DROP TABLE IF EXISTS enrollments;
DROP TABLE IF EXISTS classes;
DROP TABLE IF EXISTS students;
DROP TABLE IF EXISTS teachers;
```

### `UNIQUE` Constraint on Multiple Columns

`enrollments` links a student to a class. A student should only be enrolled in the same class once — but `student_id` and `class_id` individually can (and should) repeat. The constraint we need is on the *combination*:

```sql
CREATE TABLE enrollments (
  enrollment_id  SERIAL  PRIMARY KEY,
  student_id     INTEGER REFERENCES students(student_id),
  class_id       INTEGER REFERENCES classes(class_id),
  UNIQUE (student_id, class_id)
);
```

`UNIQUE (student_id, class_id)` means no two rows can share the same `student_id` and `class_id` pair. A student can be in many classes, a class can have many students — but the same student can't be enrolled in the same class twice.

### Complete Schema

```sql
-- Drop in reverse dependency order
DROP TABLE IF EXISTS enrollments;
DROP TABLE IF EXISTS classes;
DROP TABLE IF EXISTS students;
DROP TABLE IF EXISTS teachers;

-- No foreign keys — create first
CREATE TABLE students (
  student_id  SERIAL PRIMARY KEY,
  first_name  TEXT   NOT NULL,
  last_name   TEXT   NOT NULL,
  dob         DATE
);

-- No foreign keys — create first
CREATE TABLE teachers (
  teacher_id  SERIAL PRIMARY KEY,
  first_name  TEXT   NOT NULL,
  last_name   TEXT   NOT NULL
);

-- References teachers — create after teachers
CREATE TABLE classes (
  class_id    SERIAL  PRIMARY KEY,
  title       TEXT    NOT NULL,
  teacher_id  INTEGER REFERENCES teachers(teacher_id)
);

-- References students and classes — create last
CREATE TABLE enrollments (
  enrollment_id  SERIAL  PRIMARY KEY,
  student_id     INTEGER REFERENCES students(student_id),
  class_id       INTEGER REFERENCES classes(class_id),
  UNIQUE (student_id, class_id)
);
```

### Schema Investigation

**<details><summary>Q: Why does `classes` get the `teacher_id` and not the `teacher_name`?</summary>**

A teacher's name belongs in the `teachers` table. Putting `teacher_name` directly on `classes` would break Normalization Rule #3: the name depends on the teacher, not on the class.

</details>

**<details><summary>Q: Why does the `enrollments` table exist? What would go wrong if you tried to store enrollment data directly on the `students` table instead?</summary>**

A student can be enrolled in many classes and a class can have many students — this is a many-to-many relationship. There is no way to represent that with a single foreign key column on either table.

The only alternative would be storing courses as a comma-separated list (e.g., `courses = "Math, Science, History"`) directly on the `students` table. That breaks the rule of atomicity — multiple values in one cell. Querying it is awkward, updating it is fragile, and you can't enforce foreign keys to the `classes` table.

The `enrollments` association table solves this by giving each student-class pairing its own row. This is normalization in practice: a relationship that produces multiple values per entity gets its own table.

</details>

**<details><summary>Q: What happens if you try to `CREATE TABLE classes` before `CREATE TABLE teachers`?</summary>**

Postgres throws an error: `relation "teachers" does not exist`. The `REFERENCES teachers(teacher_id)` clause needs `teachers` to already exist so Postgres can verify the column it's linking to. Always create parent tables before child tables.

</details>

**<details><summary>Q: What does Postgres do if you try to INSERT a row into `classes` with a `teacher_id` that doesn't exist in `teachers`?</summary>**

Postgres rejects the insert with a foreign key violation error:

```
ERROR: insert or update on table "classes" violates foreign key constraint
DETAIL: Key (teacher_id)=(99) is not present in table "teachers".
```

This is referential integrity — the database itself guarantees that every `teacher_id` in `classes` points to a real teacher. You don't need to check this in your application code because Postgres won't let the bad data in.

</details>

**<details><summary>Q: Why does `enrollments` need `UNIQUE (student_id, class_id)` rather than just making `student_id` or `class_id` individually unique?</summary>**

Making `student_id` unique on `enrollments` would mean each student could only ever be enrolled in one class — clearly wrong. Making `class_id` unique would mean each class could only ever have one student.

The constraint we actually want is on the *pair*: a student can be in many classes, and a class can have many students — but the same student cannot be in the same class twice. `UNIQUE (student_id, class_id)` expresses exactly that rule.

</details>

### See It in Action

The `school-setup.sql` file in the follow-along repo contains exactly the `CREATE TABLE` statements above, plus seed data to query against.

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
SELECT * FROM teachers;
SELECT * FROM classes;
SELECT * FROM enrollments;

-- What classes is Alice enrolled in?
-- (teaser for the next lesson — don't worry about the syntax yet)
SELECT students.first_name, classes.title
FROM students
  INNER JOIN enrollments ON students.student_id = enrollments.student_id
  INNER JOIN classes     ON enrollments.class_id = classes.class_id
WHERE students.first_name = 'Alice';
```

Run `\d enrollments` and compare the output to the `CREATE TABLE enrollments` statement above — the `REFERENCES` constraints and the `UNIQUE` constraint should all appear exactly as written.

## Practice

In groups, design a schema for one of the following and build it directly in SQL. Be ready to explain every structural decision in terms of normalization.

- An online store (users, products, orders, order items)
- A photo-sharing app with comments (users, photos, comments)
- A restaurant reservation system (customers, restaurants, tables, reservations)
- A job board (companies, job listings, applicants, applications)

**Step 1 — Plan your schema**

Work through the design on paper or a whiteboard:
- Which tables do you need? What does each one represent?
- What columns does each table have? Where does each piece of information belong?
- What are the relationships? Which are one-to-many? Which are many-to-many?
- What constraints should exist on the columns? Which should be unique, not null, or have default values?

For each decision, be able to answer:
- **Why is this a separate table?** (What redundancy or violation would occur if it weren't?)
- **Why does this column live here and not somewhere else?** (What does it depend on?)
- **Why does this relationship need a junction table?**
- **Why must this column be unique, be not null, or have a default value?**

**Step 2 — Translate your design to SQL**

Write a `CREATE TABLE` statement for every table. For each statement:
- Use `SERIAL PRIMARY KEY` for the primary key column
- Use `REFERENCES other_table(other_table_id)` for every foreign key column
- Order your statements so that parent tables are created before child tables
- Run your SQL in `psql` and use `\d table_name` to verify the constraints appear as expected

**Extension:** Visualize your schema in [dbdiagram.io](https://dbdiagram.io) using DBML.

## ERDs: Design Before You Code (Extension)

An **Entity Relationship Diagram (ERD)** is a visual representation of your schema — each table's columns and the relationships between them — before you write any SQL.

![An ERD maps contents of database tables (entities) and the relationships between them.](./img/5-schema-design-normalization/authors-books-erd.png)

Creating a diagram like this gives everyone a single source of truth *before* anyone writes a line of application code.

**Database Markup Language (DBML)** is a plain-text format for describing a database schema. When you paste it into [dbdiagram.io](https://dbdiagram.io), it renders a visual ERD you can share with your team.

Try pasting the following into the dbdiagram editor:

```dbml
Table authors {
  author_id integer [pk]
  first_name text
  last_name  text
  dob        date
}

// One-to-many: each author can have many books

Table books {
  book_id integer [pk]
  title text
  author_id integer [ref: > authors.author_id]
  published_date date
}
```

**<details><summary>Q: What is the point of creating a diagram like this vs. using SQL to create the database directly in PostgreSQL?</summary>**

Diagrams are easy to collaborate on and make changes before committing anything to code. As a team, you can align on your design decisions before getting to work where changes will become more costly.

</details>

Here is the complete school database in DBML. Paste it into dbdiagram.io and compare the diagram to the `CREATE TABLE` statements you already wrote:

```dbml
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

// Each class can have one teacher
Table classes {
  class_id   integer [pk]
  title      varchar
  teacher_id integer [ref: - teachers.teacher_id]
}

// Students and classes can each have many enrollments
Table enrollments {
  enrollment_id integer [pk]
  student_id    integer [ref: > students.student_id]
  class_id      integer [ref: > classes.class_id]
}
```

In DBML, relationships use these symbols:
* one-to-one: `[ref: - table.column]`
* many-to-one: `[ref: > table.column]` (use `<` to flip direction)

## Normalization Rules Challenge

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