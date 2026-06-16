# Session 2 exercise: Learning and Building with Claude Code

{% hint style="info" %}
Follow along with the repo [here](https://github.com/The-Marcy-Lab-School/fastapi-ai-practice)!
{% endhint %}

**Table of Contents:**
- [Overview](#overview)
  - [Learning While Building with AI](#learning-while-building-with-ai)
- [What is FastAPI? How is it similar to Express? What is Pydantic?](#what-is-fastapi-how-is-it-similar-to-express-what-is-pydantic)
- [What you're building](#what-youre-building)
  - [The API Contract](#the-api-contract)
  - [Resource: Todo](#resource-todo)
  - [Endpoints](#endpoints)
  - [Additional behavior](#additional-behavior)
  - [Seed data](#seed-data)
- [Before you write a single prompt](#before-you-write-a-single-prompt)
- [The prompts](#the-prompts)
  - [The context-setting prompt](#the-context-setting-prompt)
  - [The Getting Started prompt](#the-getting-started-prompt)
    - [Comment Your Code!](#comment-your-code)
  - [When you don't understand something](#when-you-dont-understand-something)
  - [When you want to confirm your understanding](#when-you-want-to-confirm-your-understanding)
  - [Setting the Long-Term Plan](#setting-the-long-term-plan)
- [Reflection](#reflection)


## Overview

You're going to build a working API server for a Todo App in Python using a framework called **FastAPI**. FastAPI is the framework you'll be using for your Capstone project. It's one of the fastest-growing backend frameworks in the industry, and it's the direction the job market is moving for Python backend work. Learning it is not optional — but how you learn it is something you get to practice today.

Today we will introduce a prompting strategy and approach to leveraging Claude Code that will enable you to rapidly learn an unfamiliar framework like FastAPI and build with it at the same time.

### Learning While Building with AI

Before Claude Code existed, picking up a new framework meant constantly context-switching in a slow and dizzying loop:
1. write some code
2. hit a wall
3. Google the error
4. find a Stack Overflow answer from four years ago
5. read documentation that assumed knowledge you don't have yet
6. try to adapt something that almost fits. 
7. And on and on...

The prompting strategy in this exercise compresses that loop. Instead of stopping to Google, you stay in the code and ask Claude to explain the unfamiliar thing in terms you already understand. You keep building while you're learning. That's genuinely new, and it's a professional skill worth developing deliberately.

But it only works if you use it right. The same tool that can accelerate your learning can also let you skip it entirely (copy the output, run it, move on, never understand what you built). That catches up with you fast: in the assessment, in the Capstone, in a job interview where someone asks you to explain code you wrote.

The prompting strategy in this exercise is designed specifically to prevent that by following a few essential principles:
- Ask for one thing at a time. 
- Require an explanation before you accept code. 
- Write what you understood in your own words before moving on.

These aren't just good AI habits, they're good learning habits.

One more thing to name: some of you may feel like using Claude to help you build this is somehow cheating, or that it means you're not really learning. Others may feel the opposite — tempted to just ask Claude to build the whole thing and copy the output. Both of those instincts will hold you back. The professional skill is in the middle: using Claude to understand and accelerate your work, while staying in the driver's seat. That's what this exercise is designed to practice.

---

## What is FastAPI? How is it similar to Express? What is Pydantic?

FastAPI is a Python web framework for building APIs. If you know Express, the core idea is familiar: you define routes, attach handler functions, and the framework takes care of the HTTP layer.

The differences are real and worth understanding as you build. But the mental model you already have from Express will carry over. So rest assured: you're not starting from zero.

One thing you'll notice immediately: FastAPI uses Python's type annotation system heavily, and it has a library called **Pydantic** built in for data validation. You haven't seen either of those yet. Claude will introduce them to you as you need them. Your job is to understand what they're doing before you move on — not just that they work.

And lastly, FastAPI has an amazing feature: a built-in UI for testing your routes in the browser at http://localhost:8000/docs.

---

## What you're building

A **TODO API** — a server that manages a list of tasks. No frontend. Just a server you can hit with `curl`.

You'll build it in stages. Each stage adds one layer of complexity. You don't have to finish all stages today — getting through Stage 1 and into Stage 2 with genuine understanding is a complete, valuable outcome.

---

### The API Contract

This is the full spec for what the finished server should do. Read it now, before you build anything. You've seen API contracts before — this one works the same way.

### Resource: Todo

```
{
  "id": 1,
  "title": "Learn FastAPI",
  "completed": false
}
```

### Endpoints

| Method   | URL           | Description       | Request Body                                                          | Success Response               |
| -------- | ------------- | ----------------- | --------------------------------------------------------------------- | ------------------------------ |
| `GET`    | `/todos`      | Get all todos     | —                                                                     | `200` + array of todos         |
| `GET`    | `/todos/{id}` | Get one todo      | —                                                                     | `200` + todo object, or `404`  |
| `POST`   | `/todos`      | Create a new todo | `{ "title": "..." }`                                                  | `201` + created todo           |
| `PATCH`  | `/todos/{id}` | Update a todo     | `{ "title": "...", "completed": true }` (either field, both optional) | `200` + updated todo, or `404` |
| `DELETE` | `/todos/{id}` | Delete a todo     | —                                                                     | `204` no body, or `404`        |

### Additional behavior

- `GET /todos` supports an optional query parameter: `?completed=true` or `?completed=false` to filter results. Without the parameter, return all todos.
- The server should log every incoming request (method, path, timestamp) — like the `logRoutes` middleware you've used in Express.
- All write routes (`POST`, `PATCH`, `DELETE`) require a header `X-API-Key: marcy-secret`. Missing or wrong key returns `401`.

### Seed data

Start with these three todos already in memory when the server boots:

```
{ "id": 1, "title": "Read the FastAPI docs", "completed": false }
{ "id": 2, "title": "Build something with Python", "completed": false }
{ "id": 3, "title": "Understand Pydantic models", "completed": false }
```

---

## Before you write a single prompt

Check your Python version. FastAPI requires **Python 3.8+** and works best on **3.10+**. In your terminal:

```bash
python3 --version
# Look for "Python 3.8.x" or higher
```

You will need two separate terminals for this exercise: one to run your server and one to run your Claude Code session.

In one terminal at the root of the project, start a new session with `claude`:

```bash
# Inside the root of the fastapi-ai-practice directory
claude
```

Then open a second terminal and move into the `server` directory to begin working.

---

## The prompts

### The context-setting prompt

**Copy and paste this as your very first message in a new Claude Code conversation. Don't skip it — it shapes every response you'll get for the rest of the session.**

> I'm a software engineer who knows JavaScript and Express well. I recently started learning the basics of Python and now I'm learning FastAPI for the first time. I want to learn and build at the same time — not just get working code I don't understand. When you show me Python or FastAPI code, always explain it by comparing it to the Express equivalent. Show me one concept at a time — don't build the whole server at once. When I ask you to add something new, first explain what it does and why, then show me the code, then explain what each part means. If there's no direct equivalent to something in Express, tell me that and explain what problem it's solving instead.

---

### The Getting Started prompt

After you've set context, send this to get started:

> I want to build a FastAPI server. I know that with Node and Express I         
  typically start by installing dependencies with `npm i express` and then run  
  the server with `node index.js`. What is the Python and FastAPI equivalent? 

Notice that this prompt does a few things:
* States the objective (I want to build a FastAPI server)
* States what you know (I know that with Node and Express...)
* Asks to explain how to do something similar in Python and FastAPI

Once you are able to get dependencies installed, Claude will likely suggest a next step. You can follow its lead or continue directing it with a prompt like this:

> I know how to create an Express server with `const app = express(); app.listen(8080)`. What's the FastAPI equivalent? Show me just the minimal server with no routes yet, and explain what each line does compared to Express.

#### Comment Your Code!

At each stage, before you move on, add a comment block to your code. Include things like:
* Descriptions of key functionality
* Similarities and differences between Python/FastAPI and Node/Express
* Any lingering questions to return to

For example:

```python
# Import the FastAPI function from the fastapi module
# Similar to `const express = require('express')` but the the module comes first and the value is second
from fastapi import FastAPI

# Creates the application instance, like `const app = express()`
app = FastAPI()

# Apparently uvicorn handles the app.listen() part but I still don't understand why
```

After you have a running server, **continue the same conversation — don't start a new one.** Ask Claude to add things one step at a time. 

But before you continue building, check out the scenario prompts below.

---

### When you don't understand something

If Claude shows you code and something doesn't make sense, use this:

> You showed me this code: [paste the specific lines]. I don't understand what [specific part] is doing. Don't rewrite it — just explain that part in terms of JavaScript or Express. If there's no direct equivalent, tell me that and explain what problem it's solving.

The key phrase is **"don't rewrite it."** When you ask Claude to simplify something, it often replaces the confusing part with something different rather than explaining what's already there. You want to understand what's in front of you.

---

### When you want to confirm your understanding

Use this before moving from one stage to the next:

> Before I move on: here's what I think this FastAPI code is doing compared to Express — [your explanation in your own words]. Is that right? What am I missing or getting wrong?

If you can't write that explanation, you're not ready to move on. Go back and ask more questions. This isn't a gate to slow you down — it's the mechanism that makes the learning stick.

### Setting the Long-Term Plan

Now you're ready to start building. Rather than building the project all at once, it is best to break down the project into small digestible chunks. This is especially important when you want to slow down and ensure that you are learning along the way. Remember, the project itself is not the end goal: developing a strong understanding of FastAPI is.

Here a prompt that you can use to provide clear guidance to Claude on where you'd like to go and how you'd like to break down the project into smaller chunks:

> Let's work through these in order. Before moving on to the next stage, ask me to explain the current one to you.
>  
>  **Stage 1 — Get the server running and return todos**
>  - Server starts and listens on a port
>  - `GET /todos` returns the full list
>  - `GET /todos/{id}` returns one todo or a 404
> 
>  **Stage 2 — Add write operations**
>  - `POST /todos` creates a new todo
>  - `PATCH /todos/{id}` updates one (any combination of fields)
>  - `DELETE /todos/{id}` deletes one
> 
>  **Stage 3 — Add filtering**
>  - `GET /todos?completed=true` filters the list
> 
>  **Stage 4 — Add middleware and auth**
>  - Log every request (method, path, timestamp)
>  - Require `X-API-Key: marcy-secret` on write routes


Note: Claude won't strictly enforce these stages. It'll acknowledge the instruction and then happily move on whenever you ask it to. This prompt just sets Claude's behavior, but the actual progress gate (not moving on until you can explain it) is on *you*, not on Claude.

---

## Reflection

After the session, complete a short write-up and submit it on Google Classroom:

1. **Prompts:** Share the first Stage 1 prompt you wrote yourself (not the starter prompt). What made it specific or vague? What would you change?
2. **Equivalence Notes:** Share your comment blocks — at least two — highlighting the similarities/differences between Express and FastAPI.
3. **👏:** Share one thing Claude got right and how you verified it.
4. **🤔:** One thing Claude got wrong or that surprised you and how you caught it.
5. **Lingering Question:** about FastAPI or how it works.