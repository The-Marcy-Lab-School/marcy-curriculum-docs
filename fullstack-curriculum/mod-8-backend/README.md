# Mod 8 - Backend

Backend, the final boss of Marcy's Fullstack Software Engineering curriculum. Backend encompasses quite a lot and requires you to shift your thinking to a higher level. Instead of worrying about every tiny detail, you must expand your view to learn entire systems of functions that interact with each other to create an entire application. There will be many things that you don't fully understand and you will learn to be alright with that. To succeed in this module, you must hone your skill of **Pattern Recognition**

In this unit, we'll learn about:

* **Backend** — the part of the application that the user can't access. For our purposes, this means a server application and a database.
  * **Server Applications** — Application to receive requests and send back resources.
    * **Express** — The npm package we'll use to build a server application.
  * **Databases** — A place to put data and have it persist, even if the server turns off.
    * **Postgres** — The type of database we'll use (tables, records, fields/properties).
    * **SQL** — The language used to execute database commands.
    * **Knex** — The npm package we'll use to send SQL queries to our Postgres database from our server application.
  * **Model-Controller Design** — A framework for organizing the layers of software that make up the backend.
  * **Authentication** — Ensuring that only valid users can access protected content.
  * **Authorization** — Ensuring that a given user has permission to modify content.

By the end of this unit you will be able to build an application using the [React + Express + Auth Template](https://github.com/The-Marcy-Lab-School/react-express-auth).

## Lessons

{% content-ref url="./1-intro-to-express.md" %} Intro to Express {% endcontent-ref %}
{% content-ref url="./2-building-a-static-web-server-with-middleware.md" %} Building a Static Web Server with Middleware {% endcontent-ref %}
{% content-ref url="./3-securing-api-keys-with-environment-variables.md" %} Securing API Keys with Environment Variables {% endcontent-ref %}
{% content-ref url="./4-building-a-rest-api-with-mvc.md" %} Building a RESTful API with MVC {% endcontent-ref %}
{% content-ref url="./5-sql-and-databases.md" %} SQL and Databases {% endcontent-ref %}
{% content-ref url="./6-join-sql-queries.md" %} JOIN (Association) SQL Queries {% endcontent-ref %}
{% content-ref url="./7-knex.md" %} Knex {% endcontent-ref %}
{% content-ref url="./8-your-first-fullstack-app.md" %} Your First Fullstack App! {% endcontent-ref %}
{% content-ref url="./9-migrations-and-seeds.md" %} Migrations & Seeds {% endcontent-ref %}
{% content-ref url="./10-schema-design-and-normalization.md" %} Schema Design & Normalization {% endcontent-ref %}
{% content-ref url="./11-hashing-passwords-with-bcrypt.md" %} Hashing Passwords with Bcrypt {% endcontent-ref %}