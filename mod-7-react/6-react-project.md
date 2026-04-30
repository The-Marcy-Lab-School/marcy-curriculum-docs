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
* [Project Planning Artifacts](6-react-project.md#project-planning-artifacts)
  * [Proposal](6-react-project.md#proposal)
  * [Scrum Board](6-react-project.md#scrum-board)
* [Agile Practices](6-react-project.md#agile-practices)
* [Project Presentation](6-react-project.md#project-presentation)
* [Project Grading](6-react-project.md#project-grading)

## Welcome

You have built a full-stack Express + Postgres backend and a React frontend that connects to it. Now you will build one from scratch — your own domain, your own schema, your own API.

The Todo app case study (`swe-casestudy-7-todo-app`) is your reference implementation. Its API contract, schema, and user stories are the model for what you should produce for your own domain.

## Learning Objectives

* Build a complete full-stack application (Express + Postgres backend, React frontend) from scratch
* Implement authentication: register, login, logout, and session rehydration
* Design a one-to-many database schema and write the corresponding SQL queries
* Write a clear API contract before writing any code
* Apply Agile practices: proposal, scrum board, daily standups

## Timeline

| Days  | Focus                                                                 |
| ----- | --------------------------------------------------------------------- |
| 8     | **Planning only**: finalize proposal, schema, and API contract        |
| 9–10  | Backend build: database, models, controllers, auth                   |
| 11–12 | Frontend build: session rehydration, CRUD, auth UI                   |
| 13–14 | Polish, debugging, README, bonus features                             |
| 15    | Presentations                                                         |

> **Day 8 is planning only. No code until the proposal, schema, and API contract are approved.**

## What You Will Build

You will build a full-stack web application for a resource that **belongs to a user**. Think: a user has many things (todos, books, expenses, recipes…).

### Default Option: Todo App

If you are having trouble deciding on a domain, build the Todo app:

* Follow the same spec as the case study (`swe-casestudy-7-todo-app`)
* Your API contract, schema, and user stories should match the reference implementation
* This option still requires building everything from scratch — you cannot copy the case study code

### Suggested Domains

Choose one, or propose your own (subject to instructor approval):

1. **Job Application Tracker** — users + applications (company, role, status: applied/interviewing/offer/rejected, date applied, notes)
2. **Reading List** — users + books (title, author, genre, status: want-to-read/reading/finished, rating, notes)
3. **Expense Tracker** — users + expenses (title, amount, category, date)
4. **Recipe Box** — users + recipes (title, description, ingredients, instructions)
5. **Workout Log** — users + workouts (date, type: cardio/strength/flexibility, duration, notes)

## Technical Requirements

### Backend

* [ ] Express server with `express.json()` middleware
* [ ] PostgreSQL database with a **one-to-many schema** (users → resource of your choice)
* [ ] Auth endpoints:
  * [ ] `POST /api/auth/register`
  * [ ] `POST /api/auth/login`
  * [ ] `DELETE /api/auth/logout`
  * [ ] `GET /api/auth/me`
* [ ] CRUD endpoints for your resource:
  * [ ] `GET /api/[resource]` — list all resources for the current user
  * [ ] `POST /api/[resource]` — create a resource
  * [ ] `PATCH /api/[resource]/:id` — update a resource
  * [ ] `DELETE /api/[resource]/:id` — delete a resource
* [ ] Authentication middleware that protects resource routes
* [ ] Models use parameterized queries (no SQL injection)
* [ ] `seed.js` that creates tables and inserts sample data

### Frontend

* [ ] Vite + React project
* [ ] `vite.config.js` proxy configured for same-origin dev setup
* [ ] Fetch helpers organized by domain (e.g., `auth-helpers.js`, `[resource]-helpers.js`)
* [ ] Session rehydration: `GET /api/auth/me` called on mount via `useEffect([], [])`
* [ ] Auth-dependent data fetch: resource list fetched when `currentUser` changes
* [ ] `currentUser` passed as a prop (Context not required)
* [ ] Login and register forms (controlled) — conditional rendered when not logged in
* [ ] Logout button
* [ ] Display the list of resources
* [ ] Create form (controlled) — POST + refetch
* [ ] Delete button — DELETE + refetch
* [ ] Update/edit form or inline toggle — PATCH + refetch
* [ ] `isLoading` state shown while fetching
* [ ] `error` state displayed when a fetch fails

### Documentation

* [ ] **API contract**: a Markdown table listing every endpoint, method, path, description, and request body (see the case study for format)
* [ ] **Schema diagram**: an entity-relationship diagram showing your tables and their relationships
* [ ] **User stories**: a list of "a user can…" statements covering all features
* [ ] **README**: project description, setup instructions, and screenshots

### Bonus

* [ ] React Router — multiple pages (e.g., list page → detail page)
* [ ] Global Context — `currentUser` in a Context instead of props drilling

## Project Planning Artifacts

### Proposal

Submit your proposal **by the end of Day 8**. It must include:

* Mission statement: who is this for, what does it do, and why would someone use it?
* Schema diagram (tables, columns, relationships)
* API contract (all endpoints)
* MVP user stories ("a user can…")
* Stretch user stories

Reference: `swe-casestudy-7-todo-app/API_CONTRACT.md` as a model for format and detail.

### Scrum Board

Create a GitHub repo and a project scrum board with columns: **Backlog**, **In Progress**, **Done**.

Each morning during stand-up, update the board and assign tasks for the day.

Assign one team member as **Project Manager (PM)**. The PM maintains the scrum board and keeps the team accountable to it.

## Agile Practices

During this project you will practice the [Agile Methodology](https://www.atlassian.com/agile):

* **Daily stand-up** — each morning, share: what you did yesterday, what you plan to do today, any blockers.
* **Scrum board** — visualize and manage your tasks.
* **Timeline** — Day 8 is planning, Days 9–14 are build, Day 15 is presentations.
* **Retrospective** — at the end, reflect on what went well and what you'd do differently.

Write a short reflection (~250 words) on how your team practiced the Agile methodology. Specific questions are on Canvas. One reflection per group.

## Project Presentation

On Day 15 your team will present to the class:

* Each team member has a speaking role
* Include a short demo (under 2 minutes) of the working application
* Total presentation: no longer than 5 minutes

Cover:
1. What the app does and who it's for
2. One technical challenge you solved
3. One thing you would build next

## Project Grading

### Backend (20 points)

* [ ] One-to-many schema with users table (4 pts)
* [ ] All four auth endpoints work correctly (4 pts)
* [ ] CRUD endpoints return correct data and status codes (4 pts)
* [ ] Authentication middleware protects resource routes (4 pts)
* [ ] Parameterized queries — no SQL injection (2 pts)
* [ ] Seed file creates tables and sample data (2 pts)

### Frontend (20 points)

* [ ] Session rehydration on mount (3 pts)
* [ ] Auth-dependent data fetch (3 pts)
* [ ] Login / register / logout UI (3 pts)
* [ ] Full CRUD UI with refetch-after-write (4 pts)
* [ ] `isLoading` and `error` states handled (3 pts)
* [ ] Vite proxy configured correctly (2 pts)
* [ ] Fetch logic in domain-specific helper files (2 pts)

### Documentation (10 points)

* [ ] API contract (3 pts)
* [ ] Schema diagram (3 pts)
* [ ] User stories (2 pts)
* [ ] README with setup instructions and screenshots (2 pts)

### Agile (10 points)

* [ ] Proposal submitted by end of Day 8 (3 pts)
* [ ] Scrum board maintained throughout (4 pts)
* [ ] Agile reflection written (3 pts)

### Presentation (5 points)

* [ ] Each member speaks (2 pts)
* [ ] Demo shows a working application (2 pts)
* [ ] Presentation under 5 minutes (1 pt)
