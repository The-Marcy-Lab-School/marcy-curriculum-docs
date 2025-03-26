# JOIN (Association) SQL Queries

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/8-2-1-associations)!
{% endhint %}

So, what makes Postgres a "relational" database management system? Relationships!

In this lesson, we'll learn how we can establish relationships between tables using primary and foreign keys and how to manipulate related tables with SQL.

**Table of Contents**

- [Terms](#terms)
- [How should I create my database?](#how-should-i-create-my-database)
- [Two Tables and Foreign Keys](#two-tables-and-foreign-keys)
- [Association SQL Queries](#association-sql-queries)
	- [Setup](#setup)
	- [Some Questions To Answer](#some-questions-to-answer)
- [Entity Relation Diagrams and Many To Many Relationships](#entity-relation-diagrams-and-many-to-many-relationships)

## Terms

* **Schema Design** - the process of planning the structure and relationships of the tables in a database.
* **Primary Key** - a column in a table that uniquely identifies each row in the table.
* **Foreign Key** - a column in a table that references the primary key of another table.
* **One-to-Many** - a relationship between two tables in which instances in one table can be referenced by many instances in another table.
* `JOIN` - A SQL statement that combines the columns of two tables.
* **Entity Relationship Diagram** - a diagram that illustrates the relationships between tables.
* **Many-to-Many** - a relationship between two tables in which the instances of each table can be referenced by many instances in the other table.
* **Association/Junction Table** - a table used to create a many-to-many relationship using two foreign keys to reference two tables.

## How should I create my database?

Imagine that you are building a social network app for people and their pets. Users can sign up and add pets to their accounts.

To account for all of the data, you might make a single table that stores each instance of a user-pet relationship:

**SQL:**

```sql
CREATE TABLE all_data (
	id SERIAL PRIMARY KEY,
	owner_name TEXT,
	pet_name TEXT,
	type TEXT
)
```

**`all_data` Table:**

| id  | owner_name     | pet_name   | type |
| --- | -------------- | ---------- | ---- |
| 1   | Ann Duong      | Bora       | bird |
| 2   | Ann Duong      | Tora       | dog  |
| 3   | Ann Duong      | Kora       | dog  |
| 4   | Ben Spector    |            |      |
| 5   | Reuben Ogbonna | Juan Pablo | dog  |
| 6   | Reuben Ogbonna | Pon Juablo | cat  |
| 7   | Carmen Salas   | Khalo      | dog  |
| 8   | Carmen Salas   | Frida      | cat  |

<details>

<summary><strong>Q: What are the issues with storing the data in this way?</strong></summary>

* There is a lot of duplicate data in the `owner_name` column

</details>

## Two Tables and Foreign Keys

Instead of storing all data in a single table, we can break up the table into two related tables:

1. A `people` table to store data about people
2. A `pets` table to store data about pets
   * The `pets` table has an `owner_id` column that _references_ the **primary key** the `people` table.
   * The `pets.owner_id` column is known as a **foreign key**.

```sql
CREATE TABLE people (
	id SERIAL PRIMARY KEY,
	name TEXT NOT NULL
);
CREATE TABLE pets (
	id SERIAL PRIMARY KEY,
	name TEXT NOT NULL, 
	type TEXT NOT NULL, 
	owner_id INTEGER REFERENCES people --foreign key
);
```

**`people` Table:**

| id  | name           |
| --- | -------------- |
| 1   | Ann Duong      |
| 2   | Reuben Ogbonna |
| 3   | Carmen Salas   |
| 4   | Ben Spector    |

**`pets` Table:**

| id  | name       | type | owner_id |
| --- | ---------- | ---- | -------- |
| 1   | Khalo      | dog  | 3        |
| 2   | Juan Pablo | dog  | 2        |
| 3   | Bora       | bird | 1        |
| 4   | Frida      | cat  | 3        |
| 5   | Tora       | dog  | 1        |
| 6   | Pon Juablo | cat  | 2        |
| 7   | Kora       | dog  | 1        |

<details>

<summary><strong>Q: What are the tradeoffs of this schema design?</strong></summary>

We no longer have duplicate data It is not exactly clear anymore the name of the person who owns each pet

</details>


With these two tables, a **one-to-many relationship** has been formed: a person can have many pets. This is one of the most common relationships between tables in a database. Some other examples include:

* A `user` has many `posts`
* A `restaurant` has many `reviews`
* An `album` has many `songs`
* Can you think of any more?

## Association SQL Queries

### Setup

Use the following commands to quickly create the `people` and `pets` tables in a database of your choosing.

```sql
DROP TABLE IF EXISTS pets; -- do this first because pets depends on people
DROP TABLE IF EXISTS people;

CREATE TABLE people (
	id SERIAL PRIMARY KEY,
	name TEXT NOT NULL
);

INSERT INTO people (name) VALUES ('Ann Duong');
INSERT INTO people (name) VALUES ('Reuben Ogbonna');
INSERT INTO people (name) VALUES ('Carmen Salas');
INSERT INTO people (name) VALUES ('Ben Spector');

CREATE TABLE pets (
	id SERIAL PRIMARY KEY,
	name TEXT NOT NULL, 
	type TEXT NOT NULL, 
	owner_id INTEGER REFERENCES people
);

INSERT INTO pets (name, type, owner_id) VALUES ('Khalo', 'dog', 3);
INSERT INTO pets (name, type, owner_id) VALUES ('Juan Pablo', 'dog', 2);
INSERT INTO pets (name, type, owner_id) VALUES ('Bora', 'bird', 1);
INSERT INTO pets (name, type, owner_id) VALUES ('Tora', 'dog', 1);
INSERT INTO pets (name, type, owner_id) VALUES ('Frida', 'cat', 3);
INSERT INTO pets (name, type, owner_id) VALUES ('Pon Juablo', 'cat', 2);
INSERT INTO pets (name, type, owner_id) VALUES ('Kora', 'dog', 1);
```

* What is the primary key for each table?
* Are there any foreign keys?
* How is the relationship between the two tables created using SQL?

### Some Questions To Answer

Now that we're set up, let's answer some questions!

We'll start with a few simple ones. What SQL statements could we write to find out:

* How many people are in our database?
* What are the names of the cats in our database?
* How many birds and dogs are in our database?

<details>

<summary><strong>Answers</strong></summary>

```sql
SELECT COUNT(*)
FROM people;

SELECT name
FROM pets
WHERE type = 'cat';

SELECT COUNT(*)
FROM pets
WHERE type = 'dog' OR type = 'bird';
```

</details>


Now let's try a harder one: What are the names of all the pets owned by Ann?

In order to answer this question, it will be helpful to use a `JOIN`

```sql
SELECT * 
FROM people 
	JOIN pets ON people.id = pets.owner_id;
```

| person.id | name           | pet.id | name       | type | owner_id |
| --------- | -------------- | ------ | ---------- | ---- | -------- |
| 1         | Ann Duong      | 3      | Bora       | bird | 1        |
| 1         | Ann Duong      | 4      | Tora       | dog  | 1        |
| 1         | Ann Duong      | 7      | Kora       | dog  | 1        |
| 2         | Reuben Ogbonna | 2      | Juan Pablo | dog  | 2        |
| 2         | Reuben Ogbonna | 6      | Pon Juablo | cat  | 2        |
| 3         | Carmen Salas   | 1      | Khalo      | dog  | 3        |
| 3         | Maya Salas     | 5      | Frida      | cat  | 3        |

We can modify our `SELECT` and add a `WHERE` clause to answer our question!

```sql
SELECT pets.name 
FROM people 
	JOIN pets ON people.id = pets.owner_id
WHERE people.name = 'Ann Duong';
```

**Let's see if we can answer some other questions:**

* Who is the owner of Frida?
* What are the names of the people who own cats?
* How many pets does Carmen have?

<details>

<summary><strong>Answers</strong></summary>

```sql
SELECT people.name
FROM people 
	JOIN pets ON people.id = pets.owner_id
WHERE pets.name = 'Frida';

SELECT people.name
FROM people 
	JOIN pets ON people.id = pets.owner_id
WHERE pets.type = 'cat';

SELECT COUNT(*)
FROM people 
	JOIN pets ON people.id = pets.owner_id
WHERE	people.name = 'Carmen Salas';
```

</details>


## Entity Relation Diagrams and Many To Many Relationships

We can illustrate the relationships between tables with an **entity relation diagram (ERD)**:

![ERD with one to many and many to many relationships](img/labeled-erd.png)

> _created using https://dbdiagram.io/_

Above, we've introduced a **many-to-many** relationship that is created using _three_ tables: _A student can be enrolled in many classes and a class can enroll many students._

The `enrollments` table in the middle is called an **association/junction table** because its records represent the association of two separate entities. An association/junction table can consist entirely of foreign keys.

**Q: Can you think of other many-to-many relationships in the apps you use?**
