# Mod 7 Project: Full-Stack PERN Application

- [Overview](#overview)
  - [Learning Objectives](#learning-objectives)
  - [The Case Study and Using AI](#the-case-study-and-using-ai)
  - [Timeline](#timeline)
  - [Suggested Domains](#suggested-domains)
- [Deliverables](#deliverables)
  - [Documentation](#documentation)
  - [Backend](#backend)
  - [Frontend](#frontend)
  - [Scrum Board](#scrum-board)
  - [Project Presentation](#project-presentation)
  - [Bonus](#bonus)
- [Project Grading](#project-grading)
  - [Backend (20 points)](#backend-20-points)
  - [Frontend (15 points)](#frontend-15-points)
  - [Documentation (4 points)](#documentation-4-points)
  - [Project Management (2 points)](#project-management-2-points)
  - [Presentation (3 points)](#presentation-3-points)

## Overview

This is your first chance to build a project using the full extent of the "PERN" tech stack (Postgres, Express, React, Node)!

For this project, you will get a chance to build a full stack project of your own design: you choose the domain, your design schema and API, and you build the frontend.

### Learning Objectives

Through building this project, your **systems-level understanding of PERN software** will become concrete and you will have a portfolio-worthy project to showcase your mastery.

Meanwhile the planning, documentation, and presentation will reflect your **development process** and the **decisions** you made while building it.

By building this project you will:
* Make decisions and tradeoffs as you plan the MVP and stretch features of your application
* Design and implement a database schema, Express API, and React frontend.
* Create documentation and a presentation that highlights your development/design process and your decision-making.
* Deploy a full-stack application
* Apply Agile practices: project plan, scrum board, daily standups
* Deepen your systems-level understanding of full-stack software

### The Case Study and Using AI

While you will build this project of your own design **we expect you to use this case study and AI** to support both your understanding and your implementation of this project.

As always, **you are responsible for being able to explain every single line of code** that you publish as your own.

**The Case Study:**

The Todo app case study ([swe-casestudy-7-todo-app](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app)) is your reference implementation. It represents what we are expecting in terms of scope and complexity for this project: a single one-to-many relationship with users and user-owned resources

The case study also demonstrates a number of best practices and patterns that we expect you to replicate such as authentication and ownership, session rehydration, conditional rendering and file organization. Its API contract, schema, and user stories are the model for what you should produce for your own domain.

In summary: you will choose the project domain but are expected to use the same structure as the case study.

**AI:**

You are also encouraged to use AI to help you build this project. In particular, we encourage you to use AI in the following ways:
* as a thought-partner to help you plan your project's design (decide MVP features, plan database schema, define your API contract, etc...)
* to generate clean Markdown documentation for your project's README
* to generate CSS styling for your React application
* to debug your application
* to better understand the flow of the application's logic

As you use AI, be sure to use it as a **partner, not a substitute** for you doing the work. Describe thoroughly what you are thinking about building and ask for its suggestions or additional questions that you might be overlooking. Ask for feedback on code you've already written and when it makes suggestions, think about them critically and push back when you want to go in a different direction.

### Timeline

Build in this order: schema → seed → models → resource endpoints → auth → frontend → presentation

| Days | Focus                                                                |
| ---- | -------------------------------------------------------------------- |
| 1–2  | **Planning only**: draft your project plan, schema, and API contract |
| 3–4  | Backend build: database, models, controllers, auth                   |
| 5–7  | Frontend build: session rehydration, CRUD, auth UI                   |
| 8    | Polish, debugging, README, bonus features                            |
| 9    | Presentations                                                        |

> **Days 1–2 are planning only. No code until the project plan, schema, and API contract are submitted by 9:00 AM on Day 3.** That being said, this planning will remain "in draft" until your project is complete and you are expected to keep it up-to-date as you make changes.

### Suggested Domains

You will build a very simple full-stack web application for a resource that **belongs to a user**. Think: a user has many things (todos, books, expenses, recipes…).

Here are some suggestions for what you can build

1. **Job Application Tracker** — users + applications (company, role, status: applied/interviewing/offer/rejected, date applied, notes)
2. **Reading List** — users + books (title, author, genre, status: want-to-read/reading/finished, rating, notes)
3. **Expense Tracker** — users + expenses (title, amount, category, date)
4. **Recipe Box** — users + recipes (title, description, ingredients, instructions)
5. **Workout Log** — users + workouts (date, type: cardio/strength/flexibility, duration, notes)

## Deliverables

Your MVP is deliberately minimal — a working app that does a few things well is better than an ambitious app that doesn't run. To complete this project you will need to build:
1. Documentation that clearly explains the technical details of the project
2. A backend server with Express and Postgres
3. A frontend application with Vite and React
4. A scrum board to track and manage your tasks
5. A presentation to showcase your project and your development process

### Documentation

The `README.md` of your project must be a polished and professional landing page for any developer looking at your application. It should include the following:
* a **mission statement** (who is this for and why would they use it)
* a **schema diagram**
* an **API contract** covering every endpoint with request and response details
* **MVP user stories** written as "a user can…" statements.

This should be the first thing that you create before you write any code. In the beginning, this document may be a rough draft. However, you are expected to refine it as the project evolves. By the end of the project, the README must also include
* **setup instructions** (how to create the database, seed it, and run both servers)
* a **roadmap** listing stretch features you'd build next.

Reference the [case study's README](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app/blob/main/README.md) as a model for format and depth.

{% hint style="info" %}
💡 AI is an excellent planning partner and we *expect* and *encourage* you to use it here to refine your idea and generate this documentation.
{% endhint %}

### Backend

Your backend is an Express + Postgres server built around a single **one-to-many relationship**: a `users` table and a resource table whose rows each belong to a user via a `user_id` foreign key. This is the same structure as the case study — the difference is only your domain.

The server exposes two groups of endpoints. 
1. The **auth endpoints** (`POST /api/auth/register`, `POST /api/auth/login`, `DELETE /api/auth/logout`, `GET /api/auth/me`) handle session creation and destruction using cookies. 
2. The **resource endpoints** let a logged-in user list their resources (`GET`), add a new one (`POST`), and remove one (`DELETE`). Resource routes should be protected by authentication middleware so that unauthenticated requests receive a 401 before they reach any model code.

Your models must use `pg` with **parameterized queries** throughout and developer should be able to quickly get set up with a database by running a `seed.js` file that drops, creates, and populates your tables. 

Sensitive values (database credentials, session secret) belong in a `.env` file, with a `.env.template` that documents the required variables without exposing real values.

{% hint style="info" %}
💡 When building your backend, start with the schema and seed, then build your models, then your endpoints, and finally layer authentication and authorization on top. Leverage the case study for guidance!
{% endhint %}

### Frontend

Your frontend is a **Vite + React** app. Its `vite.config.js` should proxy `/api` requests to your Express server so that session cookies work correctly during development.

On load, the app calls `GET /api/auth/me` to **rehydrate the session** — this keeps a returning user logged in after a page refresh without requiring them to log in again. Additionally, the auth status of a user changes what they see: 
* guests see login and register forms
* logged-in users see their resources and a logout button.

The core resource UI consists of three pieces: 
1. a **list** that displays the current user's resources
2. a **create form** that POSTs a new resource and then refetches
3. a **delete button** on each item. 

{% hint style="info" %}
💡 When it comes to styling your application, you are expected and encouraged to use AI! Writing CSS can be tedious to do by hand. We'd rather you spend your time designing and implementing the core server-side and React logic.
{% endhint %}

### Scrum Board

Create a GitHub repo on your personal account and a project scrum board with columns: **Backlog**, **In Progress**, **Done**.

During this project you will practice the [Agile Methodology](https://www.atlassian.com/agile):

* **Daily stand-up** — each morning, share with the class: what you did yesterday, what you plan to do today, any blockers.
* **Scrum board** — visualize and manage your tasks.
* **Timeline** — Days 1–2 are planning, Days 3–7 are build, Day 8 is polish and Day 9 is presentations.
* **Retrospective** — at the end, reflect on what went well and what you'd do differently.

### Project Presentation

On the final day, you will present to the class:

* Include a short demo (under 2 minutes) of the working application. This can be live or recorded.
* Total presentation: no longer than 5 minutes

Cover:
1. What the app does, who it's for, and why you built it
2. One technical challenge you solved and how you solved it
3. One thing you would build next and how you would build it

### Bonus

Once MVP is complete and working:

- `PATCH /api/[resource]/:id` — update a resource, with an edit form on the frontend
- React Router — multiple pages (e.g., list page → detail page)
- Global Context — `currentUser` in Context instead of props drilling

## Project Grading

Each checkbox is worth 1 point (44 points total).

### Backend (20 points)

* [ ] Schema has a one-to-many relationship (users table + resource table with FK)
* [ ] Seed file creates tables and sample data
* [ ] User model uses `pg` to correctly send SQL queries to the database for necessary CRUD operations
* [ ] Resource model uses `pg` to correctly send SQL queries to the database for necessary CRUD operations
* [ ] Parameterized queries are used throughout — no SQL injection
* [ ] Login auth endpoint works correctly and creates a session cookie
* [ ] Register auth endpoint works correctly and creates a session cookie
* [ ] Logout auth endpoint works correctly and deletes the session cookie
* [ ] `/api/auth/me` endpoint works correctly
* [ ] Read resource endpoint returns correct data and status codes
* [ ] Create resource endpoint returns correct data and status codes
* [ ] Delete resource endpoint returns correct data and status codes
* [ ] `checkAuthentication` middleware is used to protect at least one resource route
* [ ] Create and Delete endpoints use the session cookie to confirm ownership
* [ ] Server `index.js` file uses a `logRoutes` middleware to log all incoming routes
* [ ] Server `index.js` file has a catch-all error handler that sends a `5xx` error code for database errors
* [ ] Environment variables are used to protect sensitive data and are stored in a `.env` file with a `.env.template` for other developers to copy
* [ ] Static assets are served from the frontend's `dist` folder
* [ ] `express.json()` middleware is used to parse incoming JSON requests
* [ ] Code is modular and organized into separate folders for controllers, models, and middleware.

### Frontend (15 points)

* [ ] Created using Vite
* [ ] Vite proxy configured correctly
* [ ] Session rehydration on mount
* [ ] Login / register conditionally rendered for guest users and logout UI shown for logged-in users
* [ ] Resources are displayed in a list or grid
* [ ] A form is available to create a resource
* [ ] A delete button is available to delete a resource
* [ ] Mutation fetch requests trigger a refetch
* [ ] `isLoading` state is handled and displayed to the user
* [ ] `error` state is handled and displayed to the user
* [ ] Components demonstrate correct use of `props`
* [ ] Demonstrates correct use of `useState` at least once with state "lifted up" to be shared between components
* [ ] Demonstrates correct use of `useEffect` at least once
* [ ] Code is modular and organized into separate folders for adapters and components
* [ ] CSS creates an aesthetically pleasing user interface

### Documentation (4 points)

* [ ] User stories cover all features
* [ ] Schema diagram is accurate
* [ ] API contract covers all endpoints
* [ ] README includes setup instructions and screenshots

### Project Management (2 points)

* [ ] Planning doc submitted by 9:00 AM Day 3
* [ ] Scrum board maintained throughout the project

### Presentation (3 points)

* [ ] Demo shows a working application
* [ ] Presentation under 5 minutes
* [ ] Presentation addresses all 3 questions
