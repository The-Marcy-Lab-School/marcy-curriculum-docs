# Mod 7 Project: Full-Stack React Application

* [Welcome](6-react-project.md#welcome)
* [Learning Objectives](6-react-project.md#learning-objectives)
* [Timeline](6-react-project.md#timeline)
* [What You Will Build](6-react-project.md#what-you-will-build)
  * [Default Option: Todo App](6-react-project.md#default-option-todo-app)
  * [Suggested Domains](6-react-project.md#suggested-domains)
* [Technical Requirements](6-react-project.md#technical-requirements)
  * [Backend](6-react-project.md#backend)
  * [Frontend](6-react-project.md#frontend)
  * [Documentation](6-react-project.md#documentation)
  * [Bonus](6-react-project.md#bonus)
* [Project Artifacts](6-react-project.md#project-artifacts)
  * [Scrum Board](6-react-project.md#scrum-board)
  * [Planning Documentation](6-react-project.md#planning-documentation)
  * [Project Presentation](6-react-project.md#project-presentation)
* [Project Grading](6-react-project.md#project-grading)

## Welcome

You have built a full-stack Express + Postgres backend and a React frontend that connects to it. Now you will build one from scratch — your own domain, your own schema, your own API.

The Todo app case study ([swe-casestudy-7-todo-app](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app)) is your reference implementation. Its API contract, schema, and user stories are the model for what you should produce for your own domain.

## Learning Objectives

* Build a complete full-stack application (Express + Postgres backend, React frontend) from scratch
* Implement authentication: register, login, logout, and session rehydration
* Design a one-to-many database schema and write the corresponding SQL queries
* Write a clear API contract before writing any code
* Apply Agile practices: proposal, scrum board, daily standups

## Timeline

| Days | Focus                                                          |
| ---- | -------------------------------------------------------------- |
| 1    | **Planning only**: finalize proposal, schema, and API contract |
| 2–4  | Backend build: database, models, controllers, auth             |
| 5–7  | Frontend build: session rehydration, CRUD, auth UI             |
| 8    | Polish, debugging, README, bonus features                      |
| 9    | Presentations                                                  |

> **Day 1 is planning only. No code until the proposal, schema, and API contract are complete.**

## What You Will Build

You will build a full-stack web application for a resource that **belongs to a user**. Think: a user has many things (todos, books, expenses, recipes…).

### Suggested Domains

Choose one, or propose your own (subject to instructor approval):

1. **Job Application Tracker** — users + applications (company, role, status: applied/interviewing/offer/rejected, date applied, notes)
2. **Reading List** — users + books (title, author, genre, status: want-to-read/reading/finished, rating, notes)
3. **Expense Tracker** — users + expenses (title, amount, category, date)
4. **Recipe Box** — users + recipes (title, description, ingredients, instructions)
5. **Workout Log** — users + workouts (date, type: cardio/strength/flexibility, duration, notes)

## Technical Requirements

Your MVP is deliberately minimal — a working app that does a few things well is better than an ambitious app that doesn't run. Build in this order: schema → seed → auth → resource endpoints → frontend.

### Backend

Your Express + Postgres server needs:

- A **one-to-many schema**: a `users` table and a resource table with a `user_id` foreign key
- **Auth endpoints**: `POST /api/auth/register`, `POST /api/auth/login`, `DELETE /api/auth/logout`, and `GET /api/auth/me`
- **Resource endpoints**: list all resources for the logged-in user (`GET`), create a resource (`POST`), and delete a resource (`DELETE`). Updating a resource (`PATCH`) is not a requirement.
- **Authentication middleware** that protects all resource routes — unauthenticated requests get a 401
- **Parameterized queries** in every model (no string interpolation in SQL)
- A **`seed.js`** that drops, creates, and populates your tables

### Frontend

Your Vite + React app needs:

- A **`vite.config.js` proxy** forwarding `/api` requests to Express so cookies work in development
- **Fetch adapters** split into domain files (e.g., `auth-adapters.js`, `[resource]-adapters.js`)
- **Session "rehydration"**: call `GET /api/auth/me` on mount so a returning user is still logged in after a page refresh
- **Auth UI**: login and register forms shown when logged out; logout button shown when logged in
- **Resource list**: display the logged-in user's resources
- **Create form**: controlled form that POSTs and refetches
- **Delete button**: removes a resource and refetches
- **`isLoading` state**: show a loading indicator while any fetch is in flight
- **`error` state**: display a message when a fetch fails

### Documentation

Before writing any code, add to your README:

- **User stories**: "a user can…" statements for every feature
- **Schema diagram**: your tables, columns, and relationships
- **API contract**: a table of every endpoint (method, path, request body, response)

By the time you submit, also include **setup instructions** (how to create the database, seed it, and start both servers) and **screenshots** of the working app.

### Bonus

Once MVP is complete and working:

- `PATCH /api/[resource]/:id` — update a resource, with an edit form on the frontend
- React Router — multiple pages (e.g., list page → detail page)
- Global Context — `currentUser` in Context instead of props drilling

## Project Artifacts

### Scrum Board

Create a GitHub repo on your personal account and a project scrum board with columns: **Backlog**, **In Progress**, **Done**.

During this project you will practice the [Agile Methodology](https://www.atlassian.com/agile):

* **Daily stand-up** — each morning, share with the class: what you did yesterday, what you plan to do today, any blockers.
* **Scrum board** — visualize and manage your tasks.
* **Timeline** — Day 1 is planning, Days 2–7 are build, Day 8 is polish and Day 9 is presentations.
* **Retrospective** — at the end, reflect on what went well and what you'd do differently.

### Planning Documentation

At the root of your repo, create a `README.md` file with the outline of your project **by 9:00 AM of Day 2**. It must include:

* Mission statement: who is this for, what does it do, and why would someone use it?
* Schema diagram (tables, columns, relationships)
* API contract (all endpoints)
* MVP user stories ("a user can…")
* Stretch user stories (what you will build if you complete MVP)

Before you complete the project, you will also need to add setup steps.

Reference the [case study's README](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app/blob/main/README.md) as a model for format and detail.

{% hint style="info" %}
💡 **Tip:** AI is an excellent planning partner and we *expect* and *encourage* you to use it here to refine your idea and to generate this documentation.
{% endhint %}

### Project Presentation

On the final day, you will present to the class:

* Include a short demo (under 2 minutes) of the working application. This can be live or recorded.
* Total presentation: no longer than 5 minutes

Cover:
1. What the app does, who it's for, and why you built it.
2. One technical challenge you solved
3. One thing you would build next

## Project Grading

Each checkbox is worth 1 point (22 points total).

### Backend (6 points)

* [ ] One-to-many schema: users table + resource table with FK
* [ ] All four auth endpoints work correctly
* [ ] CRD resource endpoints return correct data and status codes
* [ ] Authentication middleware protects resource routes
* [ ] Parameterized queries — no SQL injection
* [ ] Seed file creates tables and sample data

### Frontend (7 points)

* [ ] Session rehydration on mount
* [ ] Login / register / logout UI with conditional rendering
* [ ] Resource list, create form, and delete button with refetch-after-write
* [ ] `isLoading` and `error` states handled
* [ ] Vite proxy configured correctly
* [ ] Fetch logic in domain-specific adapter files
* [ ] Correct use of `useState`, `useEffect`, and props

### Documentation (4 points)

* [ ] User stories cover all features
* [ ] Schema diagram is accurate
* [ ] API contract covers all endpoints
* [ ] README includes setup instructions and screenshots

### Agile (2 points)

* [ ] Planning doc submitted by 9:00 AM Day 2
* [ ] Scrum board maintained throughout

### Presentation (3 points)

* [ ] Demo shows a working application
* [ ] Presentation under 5 minutes
* [ ] Presentation addresses all 3 questions
