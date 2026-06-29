# Applied AI Engineering Residency Field Guide

This document is a reference, not a tutorial. It will not teach you Python or FastAPI. What it will do is help you see exactly how large the problem is — and it is smaller than you think — and give you a strategy for tackling it efficiently.

You already know how to program. You know what a loop is, what a function does, what an object is, what a database relationship looks like. None of that changes. The question you are answering over the next five weeks is simply: **how does Python express what I already know how to do in JavaScript?**

---

## Table of Contents
- [Table of Contents](#table-of-contents)
- [Why Learn Python](#why-learn-python)
- [Part 1: Learning Python](#part-1-learning-python)
  - [The Big Idea](#the-big-idea)
  - [How to Learn Python Syntax](#how-to-learn-python-syntax)
  - [Syntax Differences (learn these fast)](#syntax-differences-learn-these-fast)
  - [Design Decisions (spend real time here)](#design-decisions-spend-real-time-here)
- [Part 2: Learning a Web Framework](#part-2-learning-a-web-framework)
  - [Your Task](#your-task)
  - [How to Learn Your Framework](#how-to-learn-your-framework)
  - [Weeks 3-5: Research and Build!](#weeks-3-5-research-and-build)
  - [Ideas for Throwaway Apps to Build](#ideas-for-throwaway-apps-to-build)
- [Part 3: Building Your Project](#part-3-building-your-project)
  - [The Product Specification Document](#the-product-specification-document)
  - [Capstone Project Team Roles](#capstone-project-team-roles)
    - [Shared Responsibilities](#shared-responsibilities)
  - [The PR Workflow](#the-pr-workflow)
- [Using AI as a Learning Tool](#using-ai-as-a-learning-tool)
  - [When to Use It](#when-to-use-it)
  - [When Not to Use It](#when-not-to-use-it)
  - [A Useful Test](#a-useful-test)


---

## Why Learn Python

You are already a JavaScript engineer. Why does learning Python matter?

> *You stop thinking in JavaScript and start thinking in programming*
> 

**Learning a second language makes you a better programmer.** The most valuable thing about learning Python is not Python. It is what learning any second language teaches you about programming itself. When you have to think carefully about how Python expresses something you already know how to do in JavaScript, you develop a deeper understanding of the underlying concepts. *You stop thinking in JavaScript and start thinking in programming*. That transferable thinking is what allows strong engineers to pick up new languages quickly throughout their careers.

**Python is everywhere and in demand.** It consistently ranks as one of the most widely used programming languages in the world and in 2026, it is the most in demand skill for software engineers. It powers backend systems at companies like Instagram, Spotify, Dropbox, and Reddit. It is the dominant language in data science, machine learning, and AI engineering. If you want to work in any of those areas, or work alongside people who do, Python fluency is a baseline expectation. 

**AI runs on Python.** The tools and libraries that power modern AI — TensorFlow, PyTorch, LangChain, Hugging Face — are all Python-first. As AI becomes a more central part of software engineering, knowing Python means you can read, understand, and work with the systems underneath the tools you use. You stop being a consumer of AI and start being an engineer who understands it.

**Python is a strong second language for a software engineer.** JavaScript and Python together cover an enormous range of job opportunities. JavaScript gets you into frontend and full-stack web development. Python gets you into backend engineering, data engineering, automation, scripting, and AI. Many engineering teams use both. Being comfortable in both makes you a more versatile and more hireable engineer.

**You will be able to speak to it in interviews.** Being able to say "I'm comfortable working in both JavaScript and Python" and then demonstrate it is a meaningful signal to a hiring manager. It shows adaptability, curiosity, and a willingness to learn. Those are qualities that get people hired.

---

## Part 1: Learning Python

### The Big Idea

Learning a second programming language is not like learning the first one. The first time, you were learning to think like a programmer. This time, you are learning how to express those same thoughts with new syntax.

Almost everything you need to learn falls into one of two categories:

1. **Syntax differences** — the same concept expressed differently (e.g., indentation instead of curly braces, `def` instead of `function`)
2. **Design decisions** — places where Python made a fundamentally different choice than JavaScript (e.g., strong vs. weak typing, how scope works, how errors are handled)

Category 1 you can pick up in a few hours. Category 2 is worth spending real time on, because it changes how you think about writing code in Python, not just how you write it.

### How to Learn Python Syntax

You do not need a course. You need a good reference and a problem to solve. In order of usefulness:

- **Python Cheatsheet** ([https://labex.io/pythoncheatsheet/](https://labex.io/pythoncheatsheet/)) — fast, scannable, great for syntax lookup. Links to classes and tutorials.
- **LeetCode** ([leetcode.com](https://leetcode.com/problemset/)) — do easy problems you already know how to solve in JavaScript. Your only job is to translate.
- **Official Python documentation** ([docs.python.org](https://docs.python.org/3/tutorial/index.html)) — dense but authoritative. Use the tutorial section to start.

### Syntax Differences (learn these fast)

Work through this list. For each concept, your goal is to be able to answer the guiding question confidently.

**Variables and Data Types**

- How do you declare a variable in Python? How is this different from JavaScript?
- What are Python's primitive data types? How do they map to JavaScript's?
- *Vocab:* `int`, `float`, `str`, `bool`, `None`, dynamic typing

**Functions**

- How do you define a function in Python?
- How does Python handle default parameter values?
- What is the difference between `return` in Python vs. JavaScript?
- *Vocab:* `def`, keyword arguments, positional arguments, `args`, `*kwargs`

**Conditionals**

- How do you write an `if/else` statement in Python?
- What replaces `===` in Python? What about `&&`, `||`, `!`?
- *Vocab:* `elif`, `and`, `or`, `not`, `==`, `is`

**Loops**

- How do you write a `for` loop in Python? How is it different from JavaScript's `for...of`?
- What is a `while` loop in Python?
- How do you loop with an index when you need one?
- *Vocab:* `for`, `while`, `range()`, `enumerate()`, `break`, `continue`

**Lists (Python's Arrays)**

- How do you create, access, and modify a list in Python?
- What list methods should you know? How do they compare to JavaScript array methods?
- What is list comprehension and when would you use it?
- *Vocab:* `list`, `append()`, `pop()`, `len()`, `sorted()`, `map()`, `filter()`, list comprehension

**Dictionaries (Python's Objects)**

- How do you create a dictionary in Python?
- How do you access, add, and remove keys?
- How do you loop over a dictionary?
- *Vocab:* `dict`, `keys()`, `values()`, `items()`, `.get()`

**String Manipulation**

- How does Python handle string formatting? What are f-strings?
- What common string methods should you know?
- *Vocab:* f-strings, `.split()`, `.join()`, `.strip()`, `.replace()`, `.upper()`, `.lower()`

**Error Handling**

- How does Python handle errors? How is this similar to and different from JavaScript's `try/catch`?
- *Vocab:* `try`, `except`, `finally`, `raise`, `Exception`

**File I/O**

- How do you read from and write to a file in Python?
- *Vocab:* `open()`, `with`, `read()`, `write()`, `readlines()`

**Importing Modules**

- How do you import code from another file or library in Python?
- How is this different from JavaScript's `require` and `import`?
- *Vocab:* `import`, `from`, `as`, standard library, `pip`

**Classes and Object-Oriented Programming**

You know OOP from JavaScript. Python's OOP will feel familiar but has its own conventions.

- How do you define a class in Python?
- What is `self` and why does every method take it as the first argument?
- How does inheritance work in Python?
- How do dunder methods work and what are the most important ones?
- *Vocab:* `class`, `__init__`, `self`, `super()`, dunder methods (`__str__`, `__repr__`, `__eq__`), inheritance

---

### Design Decisions (spend real time here)

These are the concepts where Python made a different architectural choice than JavaScript. Most, if not all, programming languages made decisions for each of these areas. Understanding what these choices are will not only make you a more fluent Python programmer, it will help you understand the range of decisions one can make when designing a programming language.

**Scope**

In JavaScript, scope is created by curly braces `{}`. A variable declared inside a block stays inside that block. In Python, scope is created by indentation — but the rules are subtly different. A variable declared inside a `for` loop, for example, is still accessible after the loop ends.

```jsx
// JavaScript
for (let i = 0; i < 3; i++) {
  let x = i * 2;
}
console.log(x); // ReferenceError: x is not defined
```

```python
# Python
for i in range(3):
    x = i * 2
print(x) # prints 4 — x is still accessible
```

- What creates a new scope in Python?
- What is the difference between local, enclosing, global, and built-in scope (LEGB rule)?
- *Vocab:* LEGB rule, `global`, `nonlocal`, namespace

**Strong vs. Weak Typing**

JavaScript is weakly typed — it will try to make sense of almost anything you give it, often converting types automatically in ways that produce surprising results. Python is strongly typed — it will raise an error rather than silently convert types for you.

```jsx
// JavaScript
let val = 'hello';
val += 10;
console.log(val); // prints hello10 — coerces the number 10 into a string
```

```python
# Python
val = 'hello'
val = val + 5 # TypeError: can only concatenate str (not "int") to str
```

- What is the difference between strong and weak typing?
- What is the difference between static and dynamic typing? (Python is both strongly typed and dynamically typed — make sure you understand what that means)
- Why does this matter when you are writing code?
- *Vocab:* strong typing, weak typing, static typing, dynamic typing, type coercion, `type()`, `isinstance()`

**Mutability**

Python makes a clear distinction between mutable and immutable data types. This matters more than you might expect when you start passing data between functions.

- What is the difference between mutable and immutable types in Python?
- What types are mutable? What types are immutable?
- What is a tuple and when would you use one instead of a list?
- *Vocab:* mutable, immutable, `tuple`, `frozenset`, shallow copy, deep copy

**Virtual Environments and Dependency Management**

In JavaScript you have `npm` and `package.json`. Python has its own equivalent system that you need to understand before you can build anything real.

- What is a virtual environment and why do you need one?
- How do you create and activate a virtual environment?
- What is `pip` and how does it compare to `npm`?
- What is a `requirements.txt` file and how does it compare to `package.json`?
- *Vocab:* `venv`, `pip`, `requirements.txt`, `pip freeze`, `activate`

---

## Part 2: Learning a Web Framework

### Your Task

One of the major goals of your Residency is to build a full stack application using Python for your server-side application. Once you’ve learned the basics of Python, the next step is learning one of Python’s web frameworks: **FastAPI**.

Just as JavaScript has frameworks like Express for building web servers, Python has its own web frameworks. The three most popular are Django, FastAPI, and Flask and each were built with different goals in mind:

- **Flask** is a microframework. It gives you almost nothing out of the box — just routing and request handling — and expects you to add what you need. Maximum flexibility, minimum magic.
- **Django** is a full-featured framework. It comes with an ORM, an admin panel, authentication, and a lot of conventions. Maximum structure, less flexibility, faster to build standard things.
- **FastAPI** is a modern, fast framework built specifically for APIs. It uses Python type hints to automatically generate documentation and validate requests. Excellent for building APIs, particularly ones that will connect to AI services.

While each of these frameworks can be used to build a web server, we will all be learning **FastAPI** for a few reasons:

- FastAPI is popular: adoption jumped from 29% to 38% in a single year according to the 2025 JetBrains/Python Software Foundation survey, and job postings for FastAPI grew 150% year-over-year — particularly strong in fintech and AI companies. The job market currently favors Django and FastAPI roughly equally overall, but the trajectory is clearly FastAPI's.
- FastAPI will feel conceptually familiar to Express — you define routes, handle requests, return responses.
    - Django's "batteries included" design (ORM, admin, auth all baked in) is powerful but introduces a lot of magic that conflicts with the mental model-building you prioritize.
    - Flask is barebones and easy to set up but isn’t as relevant in the industry for full stack development meaning you won’t learn common patterns that you will see on the job.

Learning FastAPI (or any of these frameworks for that matter) is necessary for building a full stack application in Python. However, as an academic exercise it will allow you to draw connections between all web server applications, regardless of which language or framework it was built upon. This will allow you to transcend from a software engineer who can build PERN applications to one who can build full stack applications with any language and framework.

---

### How to Learn Your Framework

### Weeks 3-5: Research and Build!

Your goal by end of Week 5 is to have a deployed MVP version of your application built, laying a strong foundation for your final project. This means user authentication and a single resource that users can manage.

Getting to that point with a new framework in a language that you are still learning may seem daunting but learning and building go hand in hand! Here are some steps that we recommend you take to quickly get up to speed with **FastAPI** and get a head start on building your project.

**Step 1: Read the official documentation introduction**

Every framework has a "getting started" or "quickstart" section. Read it before you watch any videos. This gives you the framework's own mental model for how it works.

- FastAPI: https://fastapi.tiangolo.com/tutorial/

**Step 2: Build the simplest possible thing**

Build a to-do app, a notes app, or a simple blog. This could be the foundation of your final project or it could be something unrelated. Build something that forces you to touch the core parts of the framework: routing, JSON responses, and a database connection.

The important thing is that you do not get trapped just reading documentation or watching YouTube videos and instead you actually start writing code.

**Step 3: Ask the right questions about your framework**

As you build, make sure you can answer these questions:

- How does routing work? How do you define a URL and connect it to a function?
- How does the framework handle requests and responses?
- How does it connect to a database? Does it have its own ORM or do you bring your own?
- How does it handle authentication? Is it built in or do you add a library?
- How does it compare to Express? What concepts map directly? What is genuinely different?
- What kind of project is this framework best suited for?
- What are its limitations?

**Step 4: Find two or three good resources**

Do not collect twenty resources. Find two or three that are high quality and go deep on them.

Good resource types for framework learning:

- The official documentation (always start here)
- One video tutorial that builds a real app from scratch (not a 10-minute overview)
- One written tutorial that goes deeper on a specific concept you found confusing

Resources to be cautious about:

- Anything more than 3 years old (frameworks change fast)
- Tutorials that skip setting up a database or auth (those are the hard parts)
- AI-generated code you do not understand line by line

---

### Ideas for Throwaway Apps to Build

If you aren’t sure yet about how to build your final project, start with a generic throwaway app. The best throwaway app is one that forces you to touch the parts of the framework you will need for your real project. Once you’ve built this application, it should be easy to translate your learnings to your final project. Here are some ideas:

- **A simple notes app** — create, read, update, delete notes. Covers basic CRUD and routing.
- **A blog with comments** — posts have many comments. Covers a one-to-many relationship.
- **A user authentication app** — register, login, logout, protected routes. Covers auth.

---

## Part 3: Building Your Project

### The Product Specification Document

Your product specification document develops continuously first across the first five weeks and then is refined throughout the remainder of the Residency. It is not a document you write once — it is a living artifact that gets sharper as your understanding of the project deepens.

**Week 2: Project Proposal**

Before any framework code is written, your team submits a Project Proposal that includes a project overview, user personas, a feasibility check, and a first draft of user stories. This is not a final spec. It is evidence that you have thought seriously about who you are building for and why. This proposal serves as the foundation of your project’s documentation that will evolve and grow throughout these 10 weeks.

**Weeks 3-4: Informal Development**

While you are building your throwaway apps and making your framework decision, your team should be having ongoing conversations about the actual project. Specifically:

- **Schema:** What tables will you need? What are the relationships? Sketch this out on paper or a whiteboard. It does not need to be formal yet.
- **API contract:** What endpoints will your server expose? What does a request look like? What does the response look like? Talk through this as a team.
- **Wireframes:** Sketch the key screens. They do not need to be polished — rough sketches on paper are fine. The goal is to make sure your team has a shared picture of what you are building before you start building it.

These conversations inform your Week 5 spec. By the time you sit down to write the formal document, you should not be starting from scratch.

**Week 5: Full Product Spec**

Alongside your MVP build, your team formalizes the product spec. The final document includes:

- **Revised personas and user stories** — updated based on anything you learned during Weeks 3-4
- **Schema design** — the structure of your data, finalized
- **API contract** — all endpoints, HTTP methods, request bodies, and expected responses
- **Wireframes** — visual mockups of all key screens
- **Third-party APIs and libraries** — anything external you plan to integrate, with a brief rationale for each

**Weeks 6+: Refinement**

As your project grows and evolves, so should your Product Specification document. Think of this document as the source of truth that reflects the current state of your project.

### Capstone Project Team Roles

Every Capstone project team has three members, each with a distinct role. These roles are fixed for the duration of the project. They are not just titles — each role comes with real responsibilities that the team depends on you to fulfill.

No role is more important than the others. A technically brilliant application that misses its deadlines, loses track of its tickets, or drifts from its user's actual needs is not a successful project. All three roles are essential.

If there is a disagreement between the Tech Lead and the Product Leader that the team cannot resolve, bring it to your Engineering Manager. Do not let it stall your work.

{% tabs %}

{% tab title="Scrum Master / Project Manager" %}

You are responsible for keeping the team organized, on track, and accountable. While everyone on the team builds the product, you make sure the team is always clear on what needs to get done, who is doing it, and when it is due.

**You own the process.**

**Responsibilities**:

- **Sprint Planning**
  - Lead sprint planning at the start of each sprint
  - Work with the team to define sprint goals and break them into specific tasks
  - Create tickets on the GitHub Projects board for every task, with clear descriptions and acceptance criteria
  - Assign tickets to team members and set due dates
- **During the Sprint**
  - Keep the GitHub Projects board up to date — tickets should reflect reality at all times
  - Follow up with teammates on the status of their tickets, especially as deadlines approach
  - Flag any tasks that are at risk of not being completed so the team can adjust
  - Take notes during team meetings and Engineering Manager check-ins
- **Engineering Manager Check-In**
  - You run the weekly Engineering Manager check-in
  - Come prepared with a status update on the sprint board: what is done, what is in progress, what is blocked
  - Flag any deadlines at risk and any unresolved team disagreements that need the Engineering Manager's input
- **Deadlines**
  - Track all Capstone deadlines — not just sprint deadlines but submission deadlines, deck due dates, and user testing sessions
  - Give the team at least 48 hours of notice before any major deadline

**What Good Looks Like**
- The GitHub Projects board is always current and anyone could look at it and understand exactly where the project stands
- No deadline surprises — the team always knows what is coming
- Blockers are surfaced early, not the day before something is due

{% endtab %}

{% tab title="Product Leader" %}

You are responsible for the product vision and the user experience. You make sure the team is always building the right thing — something that actually solves the problem for the people it is designed to serve.

**You own the "what" and the "why."**

**Responsibilities**

- **Product Vision**
  - Maintain and communicate a clear vision for the product throughout the project
  - Ensure every feature the team builds connects back to the user's actual needs
  - Make final decisions on product questions — what features to prioritize, what to cut, what the user experience should feel like

- **User Research and Feedback**
  - Serve as the primary point of contact with the Industry Stakeholder for your Civic Tech Challenge Area
  - Communicate with the Industry Stakeholder via email to gather feedback on product direction and validate that the solution addresses the real problem
  - Incorporate feedback from user testing sessions into product decisions for the next sprint
  - Bring user insights back to the team in a clear, actionable way

- **Product Spec**
  - Lead the development of the Product Spec Sheet — personas, user stories, API contract, schema design, and wireframes
  - Ensure the spec reflects the team's current understanding of the product and is updated as the product evolves

- **Engineering Manager Check-In**
  - Speak to product decisions made since the last check-in
  - Raise any open questions about product direction that need input

**What Good Looks Like**
- The team always has a clear answer to "who is this for and why would they use it"
- Product decisions are made deliberately and communicated clearly to the team
- User feedback from testing sessions is translated into specific, actionable changes

{% endtab %}

{% tab title="Tech Lead" %}

You are responsible for the technical quality of the codebase. You make final decisions on how the team builds things and you are accountable for the overall health of the repository.

**You own the "how."**

**Responsibilities**

- **Technical Decision-Making**
  - Make final decisions on technical questions — architecture, framework patterns, how to structure the database, how to implement a feature
  - When the team is unsure how to approach something technically, you drive the decision
  - Document significant technical decisions so the team has a record of why things were built the way they were

- **Code Review**
  - Review and approve all pull requests before they are merged into `main`
  - Leave substantive code review comments — not just approvals
  - Hold the team to the standard that `main` is always stable and nothing broken gets merged

- **Repository Health**
  - Ensure the repository is well-organized and the README is accurate and up to date
  - Own the deployment — make sure the app is deployed and the deployment is stable
  - Ensure automated tests are in place and passing before code is merged

- **Engineering Manager Check-In**
  - Speak to significant technical decisions made since the last check-in
  - Raise any technical blockers that need input or escalation

**What Good Looks Like**
- `main` is always stable — no broken builds, no untested code merged without review
- The team has a clear technical direction and does not spend time relitigating decisions that have already been made
- Pull requests receive thoughtful feedback, not just rubber-stamp approvals

{% endtab %}

{% endtabs %} 

#### Shared Responsibilities

Regardless of role, every team member is expected to:

- Contribute code throughout the project
- Submit all individual Capstone deliverables (LinkedIn posts, README contributions, presentation sections)
- Attend and participate in Engineering Manager check-ins
- Review teammates' pull requests (the Tech Lead has final approval, but everyone reviews)
- Participate in sprint planning and sprint retros
- Conduct and participate in user testing sessions
- Present their section of the final Engineering Fair presentation

---

### The PR Workflow

Once your team begins building in Week 5, all code goes through a pull request. This is not optional and it is not bureaucracy — it is how real engineering teams work.

**The rules:**

- `main` is always stable. Nothing broken ever gets merged to `main`.
- Every feature lives on its own branch, named `your-name/feature-name` (e.g., `jordan/user-auth`)
- No one merges their own PR. At least one teammate must review and approve it first.
- Code review comments should be substantive. "LGTM" is not a review.

**What good code review looks like:**

- Point out anything you do not understand and ask the author to explain it
- Flag anything that looks like it could break, even if you are not sure
- Suggest a cleaner or more Pythonic way to write something if you see one
- Acknowledge what is working well — a review is a conversation, not just criticism

**On merge conflicts:**
You will hit them. They are not emergencies. A merge conflict means two people changed the same part of the codebase and Git cannot automatically decide which version to keep. Resolving them is a skill — one worth learning now, because it comes up in every engineering job.

---

## Using AI as a Learning Tool

You have access to Claude Code during Capstone. Used well, it is one of the most powerful learning accelerants available to you. Used poorly, it will quietly rob you of the understanding you need to present your project, answer interview questions, and grow as an engineer.

The principle that governs everything in this section is simple: **AI is most useful when you already have a question, not when you are trying to avoid having one.** If you open Claude Code before you have tried something yourself, you are outsourcing the thinking. If you open it after you have hit a wall and formed a specific question, you are using it as a thinking partner. The difference between those two behaviors is the difference between a fellow who learns Python and a fellow who has Python-shaped code they cannot explain.

---

### When to Use It

**To translate concepts from JavaScript to Python**

You already have a mental model. Use Claude Code to map it onto Python. Good prompts sound like:

- "I know how to write a higher-order function in JavaScript. Show me the Python equivalent and explain what's different."
- "In Express, middleware works like this. What's the equivalent pattern in Flask?"
- "I understand how `async/await` works in JavaScript. How does Python handle asynchronous code differently?"

This is one of the highest-leverage uses of AI during the first two weeks. You are not asking it to think for you — you are asking it to translate.

**To understand code you did not write**

When documentation or a tutorial shows you code you do not fully understand, paste it into Claude Code and ask it to explain it line by line. Ask it to connect each line to something you already know. Ask it to tell you what would happen if you changed a specific part.

This is much more effective than re-reading the same code five times hoping it will click.

**To pull apart a generated skeleton**

Here is a legitimate use of AI to accelerate framework learning: ask Claude Code to generate a minimal app with a specific set of features — for example, a Flask app with user authentication and one one-to-many relationship. Then close Claude Code and spend your time reading every line of what it produced. Before you run it, try to predict what each part does. After you run it, break something intentionally and figure out why it broke.

The generated code is not the output. Your understanding of the generated code is the output.

**As a rubber duck before office hours**

Before you bring a problem to office hours or ask a teammate for help, try explaining it to Claude Code first. Describe what you expected to happen, what actually happened, and what you have already tried. This forces you to articulate the problem clearly — and a clearly articulated problem is often halfway to a solution. If Claude Code helps you solve it, great. If it does not, you now have a much clearer question to bring to a human.

**To get unstuck on syntax**

Syntax errors and small implementation questions are a completely reasonable thing to ask Claude Code about. "How do I iterate over a dictionary in Python?" or "What's the syntax for a list comprehension with a conditional?" are not cheating. They are what the tool is for. The goal is not to memorize everything — it is to understand what you are doing.

---

### When Not to Use It

**Do not use it to generate features for your project before you understand how to build them yourself.**

There is a meaningful difference between "I understand how database relationships work in Django and I am using Claude Code to help me write the model" and "I have no idea how Django models work and I am asking Claude Code to write them for me." The first is professional. The second will catch up with you at the Engineering Fair when someone asks you how your schema is designed.

**Do not use it as a first resort when you are confused.**

Confusion is the feeling of your brain building a new connection. Sit with it for a few minutes before reaching for AI. Try reading the documentation again. Try writing down in plain English what you think should happen. Try changing one thing and observing what breaks. AI can resolve confusion quickly, but it can also prevent you from developing the debugging instinct that makes you a strong engineer.

**Do not merge code into your project that you cannot explain line by line.**

This is the hard rule. If you cannot walk a teammate through a piece of code you wrote — how it works, why you made the choices you made, what would happen if a specific line changed — that code should not be in your codebase. Not because there is something wrong with using AI to help you write it, but because you are presenting this project at the Engineering Fair and defending it in interviews. "Claude wrote it" is not an answer that gets you hired.

---

### A Useful Test

Before you submit a PR, ask yourself: if my interviewer pulled up this file and asked me to walk them through it, could I do that confidently? If the answer is no, keep working until it is yes.

---

*This document is a starting point. The best version of it is the one you build through your own research, your own throwaway apps, your own team's conversations, and your own hard-won confusion. Go figure it out.*