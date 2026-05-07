# Mod 7 Project: Full-Stack PERN Application

- [Overview](#overview)
  - [Learning Objectives](#learning-objectives)
  - [The Case Study and Using AI](#the-case-study-and-using-ai)
  - [Timeline](#timeline)
  - [Suggested Domains](#suggested-domains)
- [Technical Requirements](#technical-requirements)
  - [Backend](#backend)
  - [Frontend](#frontend)
  - [Documentation](#documentation)
  - [Bonus](#bonus)
- [Project Artifacts](#project-artifacts)
  - [Scrum Board](#scrum-board)
  - [Project Presentation](#project-presentation)
- [Project Grading](#project-grading)
  - [Backend (6 points)](#backend-6-points)
  - [Frontend (8 points)](#frontend-8-points)
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

| Days  | Focus                                                                |
| ----- | -------------------------------------------------------------------- |
| 1–2   | **Planning only**: draft your project plan, schema, and API contract |
| 3–4   | Backend build: database, models, controllers, auth                   |
| 5–7   | Frontend build: session rehydration, CRUD, auth UI                   |
| 8     | Polish, debugging, README, bonus features                            |
| 9     | Presentations                                                        |

> **Days 1–2 are planning only. No code until the project plan, schema, and API contract are submitted by 9:00 AM on Day 3.** That being said, this planning will remain "in draft" until your project is complete and you are expected to keep it up-to-date as you make changes.

### Suggested Domains

You will build a very simple full-stack web application for a resource that **belongs to a user**. Think: a user has many things (todos, books, expenses, recipes…).

Here are some suggestions for what you can build

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

{% hint style="info" %}
💡 When building your backend, start with implementing the schema and seeds, then build your models, then your endpoints, and finally layer authentication and authorization on top.
{% endhint %}

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
- **CSS**: the application is generally nice-looking (AI usage is encouraged here!)

### Documentation

Before writing any code, add to the `README.md` at the root of your project repo with the following:

* **Mission statement:** who is this for, what does it do, and why would someone use it?
* **Schema diagram** (tables, columns, relationships)
* **API contract** (all endpoints, request details, response details)
* **MVP user stories** ("a user can…")

Your Days 1–2 version of this documentation can be rough. You are expected to update it as your project evolves.

By the time you submit, you must also include **setup instructions** (how to create the database, seed it, and start both servers) and a **roadmap** with future stretch features that you plan on implementing.

Reference the [case study's README](https://github.com/The-Marcy-Lab-School/swe-casestudy-7-todo-app/blob/main/README.md) as a model for format and detail.

{% hint style="info" %}
💡 **Tip:** AI is an excellent planning partner and we *expect* and *encourage* you to use it here to refine your idea and to generate this documentation.
{% endhint %}

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
* **Timeline** — Days 1–2 are planning, Days 3–7 are build, Day 8 is polish and Day 9 is presentations.
* **Retrospective** — at the end, reflect on what went well and what you'd do differently.

### Project Presentation

On the final day, you will present to the class:

* Include a short demo (under 2 minutes) of the working application. This can be live or recorded.
* Total presentation: no longer than 5 minutes

Cover:
1. What the app does, who it's for, and why you built it.
2. One technical challenge you solved
3. One thing you would build next

## Project Grading

Each checkbox is worth 1 point (23 points total).

### Backend (6 points)

* [ ] One-to-many schema: users table + resource table with FK
* [ ] All four auth endpoints work correctly
* [ ] CRD resource endpoints return correct data and status codes
* [ ] Authentication middleware protects resource routes
* [ ] Parameterized queries — no SQL injection
* [ ] Seed file creates tables and sample data

### Frontend (8 points)

* [ ] Session rehydration on mount
* [ ] Login / register / logout UI with conditional rendering
* [ ] Resource list, create form, and delete button with refetch-after-write
* [ ] `isLoading` and `error` states handled
* [ ] Vite proxy configured correctly
* [ ] Fetch logic is separated into domain-specific adapter files 
* [ ] Correct use of `useState`, `useEffect`, and props
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
