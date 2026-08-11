**Table of Contents:**
- [Overview](#overview)
  - [How to Use This Guide](#how-to-use-this-guide)
  - [Presentation Structure](#presentation-structure)
- [Part-By-Part Guides](#part-by-part-guides)
  - [Part 1: Framing the Presentation: Problem + Objective](#part-1-framing-the-presentation-problem--objective)
  - [Part 2: The Demo](#part-2-the-demo)
    - [Live and Recorded Demos](#live-and-recorded-demos)
    - [Tips for Live Demo](#tips-for-live-demo)
    - [Tips for Recording Demo](#tips-for-recording-demo)
    - [Common Mistakes to Avoid](#common-mistakes-to-avoid)
  - [Part 3: Team Introduction](#part-3-team-introduction)
  - [Parts 4-6: Telling The Story of Your Residency](#parts-4-6-telling-the-story-of-your-residency)
    - [Part 4: Product Decision-Making](#part-4-product-decision-making)
    - [Part 5: Technical Decision-Making](#part-5-technical-decision-making)
    - [Part 6a: Teamwork and Collaboration](#part-6a-teamwork-and-collaboration)
    - [Part 6b: Planning and Documentation](#part-6b-planning-and-documentation)
    - [Part 6c: Navigating Technical and Interpersonal Challenges](#part-6c-navigating-technical-and-interpersonal-challenges)
  - [Part 7: Q\&A](#part-7-qa)


## Overview

**Audience:** Peers, Marcy staff, Industry Stakeholders, and potential employers

**Goal:** Prove you can do the job — not just that you can code.

**Presentation Format:**

- Hosted at Marcy
- Industry Stakeholders are invited!
- 15 minutes per group (12 minutes of presentation + 3 minutes of Q&A/transition)
- Each team member must have a speaking role
- Voting to decide on the final 4 presentations

### How to Use This Guide

This guide will support you in planning the script and narrative for your presentation. All of the information/content that you are being asked to create through this guide should NOT live in the slides. Instead, this information/content should live in a script or on index cards while the slides serve as a high-level summary and reference for you and the audience while also providing engaging visual aides.

Check out these exemplar presentations from previous cohorts. Observe the strong use of visuals and the minimal text on each slide.

{% embed url="https://docs.google.com/presentation/d/1gukTJXleFuQ6-L0rnpszP4-3WCgz4Pe5oUC-kDCQvas/embed?start=false&loop=false&delayms=3000" %}

Here is a second example of a presentation. Note that the order is slightly different. That is okay! The structure outlined in the rest of the document is a **suggestion, not a requirement**. Use it to inspire how you can best tell your story.


{% embed url="https://docs.google.com/presentation/d/1Fpm46Z0VTv-PAKo2qaexSFsOG09Yn1kM6q6SU_H04cQ/embed?start=false&loop=false&delayms=3000" %}

### Presentation Structure

The content below is required. It outlines all of the details that you are expected to speak on but you are free to craft a narrative structure that deviates from the path below if you feel that it more strongly conveys your story.

**Part 1: Problem + Objective** *[2 min]*

- Open with your names, the name of your project, and a single phrase that captures what it sets out to do.
- Who has this problem, and what does it cost them today? Share your research or personal anecdotes to bring the problem to life.
- What was your hypothesis? Before you wrote a line of code or thought about a product, what did you think was the solution to the problem you were seeing. Not “we thought that an app that takes attendance would improve learning” but “if students had stronger attendance, they would have stronger learning outcomes”
- What was your specific objective? (e.g. not "build an app that takes attendance” but “improve learning outcomes by making it easier to take attendance”)

**Part 2: Demo** *[3 min]*

- Show the feature that most directly demonstrates the product decision you will speak about in Part 4 — not necessarily your flashiest feature.

**Part 3: Team Introduction** *[1 min]*

- Who did what, and why the roles were split that way.
- One sentence per person on what they owned, which sets up the stories each person tells in Parts 4 through 6.

**Part 4: Product Leader: Product Decision** *[2 min, STAR structure]*

- The single most important product trade-off you made
- What you chose, what you gave up, and the rationale for the decision you made.

**Part 5: Technical Lead(s): Technical Challenge** *[2 min, STAR structure]*

- The single most important technical decision you made
- What you chose, what you gave up, and the rationale for the decision you made.

**Part 6: Scrum Master: Teamwork/Collaboration/Planning/Interpersonal Challenge and Growth** *[2 min, STAR structure]*

- Choose one of the following:
    - A description of your team’s approach to collaboration that supported the development of your application and an anecdote of *how* this approach made an impact.
    - A description of how your team used planning and documentation to support the development of your application and an anecdote of *how* this approach made an impact.
    - An anecdote that captures a critical interpersonal moment that your team had to move past in order to work more productively

**Part 7: Q&A** *[3 min] (Be prepared to answer the questions below)*

- What was unfamiliar going in (Python/FastAPI, AI tooling, whatever applies)? What was your actual process for learning and getting productive — concrete, not "I studied harder."
- How did you use AI to speed up your development? What were examples where you had to push back on what AI gave you and tell it to create something else?
- How would your approach be different if you were to do this project again knowing what you know now?
- What would you build next if you had more time?

## Part-By-Part Guides

### Part 1: Framing the Presentation: Problem + Objective

**Open with your names, the name of your project, and one phrase that captures what it sets out to do.** This is the only thing the audience needs before you dig into the problem — a name to attach everything else to.

> *"Hello, my name is [name 1] and I'm joined by [name 2] and [name 3]. We are the creators of [app name], an application that [what it does]."*

What you are doing in this first section is setting up the entire presentation to answer, in this order:

1. What problem were we solving?
2. Who are you solving the problem for? Share your user personas.
3. What did you learn about the problem before building?
4. What did we decide to build (the core features of the application), and why?

If you find yourself narrating *what the code does* instead of *what you decided and why*, back up.

**Start with the problem and your objective.** Features only make sense once the audience understands what you were solving for. An employer who hears "we built a live lesson dashboard with a socket connection" learns nothing. An employer who hears "teachers had no way to know which students were falling behind mid-lesson, so we built a real-time signal for it" already wants to know how you did it.

**What was your hypothesis?** Before you wrote a line of code or thought about a product, what did you think was the solution to the problem you were seeing. Not “we thought that an app that takes attendance would improve learning” but “if students had stronger attendance, they would have stronger learning outcomes”

**Weak Example:** 

> *“We built a live lesson dashboard with a socket connection to make it easier for teachers to track their students’ understanding.”*

**Strong Example:** 

> *“Teachers had no way to know which students were falling behind mid-lesson and shy students weren’t willing to raise their hands when they had a question. We wanted to create a tool that would minimize any embarrassment of letting your classmates know you are behind while providing teachers with insight to support those students. Our hypothesis was that if a student could anonymously signal that they were struggling, and the teacher could see it in real time, teachers could provide a lesson that was targeted specifically to the needs of their classroom and shy students wouldn’t fall behind. So, we built a live lesson dashboard that makes it easier for teachers to track their students’ understanding in real time. Let’s look at how!”*

### Part 2: The Demo

Your audience now understands the problem, audience, your objective, and your hypothesis. Now you get to show them what you built to solve it. The demo also becomes the shared reference point for everything that follows — when you introduce your roles and describe a product trade-off or a technical decision later, the audience will already have seen the feature you are talking about.

Just like the rest of the presentation, a strong demo keeps the problem that you’re trying to solve and the intended audience at it core. So, **before you do anything else, answer this:** what's the one thing this demo needs to prove, and what's the shortest path through the app that proves it? Everything else is optional and should probably be cut.

A strong demo is *not* a tour of every single feature. Jumping around to various features will feel disconnected and will make it harder to see how your application solves the problem. This might mean that some bonus or peripheral features (like editing a profile) are skipped.

Instead, take the perspective of one of your core user personas (from the Product Spec Sheet) and plan a demo that shows how *they* would use the application in a way that highlights the ways in which it solves their problem.

A useful test: if you only read the narration out loud, would it still make sense as a story? If the narration is just "and then I click here, and then this happens," you're demoing clicks, not a decision.

**Structure:**

Three minutes is enough for one real user journey, told well — not enough for a full tour. Take one of your core user personas and build a demo around them:

* **[0:00–0:20] Anchor it back to the problem.**         
  * One sentence restating the intended user persona and the problem they are hoping to get solved by your application
* **[0:20–2:20] Walk one real journey start to finish.** 
  * Pick the single most representative path through your app — ideally the one that touches the product decision you'll speak about in Part 4. Narrate *why* each step matters, not just what it does. "Now I'll submit the form" is filler; "this is the moment a teacher would find out a student is falling behind — that's the whole point of the feature" is the story.
  * If your app has multiple user roles (student/teacher, admin/user), pick ONE role's journey for the main walkthrough. You can gesture at the other role in a sentence, but don't demo both in full — that's the fastest way to blow your time budget on breadth instead of depth.
* **[2:20–3:00] Land it.**                               
  * Show the actual outcome/result state, and say explicitly what it proves. Don't just end on a click — end on the screen that shows the problem is solved.

#### Live and Recorded Demos

A live demo demonstrates confidence in the application and that you've spent the time to prepare. However, things can always go wrong — Wifi drops, servers hiccup, laptops don't connect to the projector, etc.

So, **you are expected to be prepared for a live demo** *and* **have a recorded backup ready to go.** This isn't optional insurance, it's required.

#### Tips for Live Demo

- **Rehearse the exact path you'll click, more than once, on the actual machine/network you'll present on.** Not "I know the app" — the literal sequence of clicks, timed.
- **Seed your data ahead of time.** Don't create a test user or fill out a form live unless that specific action is the point of the demo — it's dead time otherwise.
- **Never debug live.** If something breaks, don't start troubleshooting in front of the room. Say what should have happened, cut to your recorded backup or a screenshot, and move on. A calm recovery reads as competence; a live debugging spiral reads as the opposite, no matter how good your code actually is.
- **Narrate slightly ahead of your clicks**, not after. "Now I'm going to submit this, which should trigger—" then click. Talking after the fact ("...and that's what just happened") reads as reactive and makes the room wait on you.
- **Know your escape hatch.** Decide in advance what you'll say and do if it breaks. "Let me show you a recording of this same flow" said confidently costs you nothing.

#### Tips for Recording Demo

- **Script the narration, don't improvise a voiceover.** A recorded demo with dead air or "umm, so here we're gonna..." reads worse than a live demo with minor imperfections, because the audience knows you had unlimited takes.
- **Cut the dead time.** No sitting on a loading spinner, no waiting for a page to render. Edit around it, or speed it up.
- **Show your cursor/clicks clearly** — a recording where the audience can't tell what you clicked to trigger something is confusing in a way a live demo isn't (you can just watch the room's eyes for confusion in the moment; you can't with a recording).
- **Keep it exactly to time.** A recorded demo has no excuse for running long — you had editing control. If it's 3:40, cut something before showcase day, not during it.
- **Play it yourself in the room and narrate live over it if that fits your team**, rather than sitting silently while it plays — this keeps you actively presenting instead of just pressing play and stepping back.

#### Common Mistakes to Avoid

- **Breadth over Depth.** Clicking through every page you built because you built it, with no throughline. If a viewer can't say what problem each screen solves, cut the screen.
- **Silence while things load or while you click around deciding what to show next.** Dead air is more noticeable in a demo than anywhere else in the talk — you should know exactly what you're clicking before you click it.
- **Demoing the easy part instead of the true part.** It's tempting to demo the polished CRUD flow instead of the messier feature that's actually central to the product decision you're about to describe. Show the one that matters, even if it's less visually finished.
- **No connection back to the rest of the talk.** A demo that could be dropped into any other team's presentation without changing a word means it wasn't actually anchored to your specific problem and decision.
- **Trying to cram in a second user role "just to show it works."** This is almost always the first thing to cut when you're over time — cut it before showcase day, not by rushing through it live.

### Part 3: Team Introduction

The audience has now seen the application. This is the moment to tell them who built it. Coming after the demo, the introduction answers a question the audience is already asking — "who made this, and who made which part of it?" — and it hands off directly into the stories in Parts 4 through 6.

Keep this short and simple:
* Choose a nice picture for each team member
* Include your name and role
* Say in one sentence what you owned on the project

> *"Hello, my name is [your name] and I was the [role] on this project. I owned [responsibilities]."*

---

### Parts 4-6: Telling The Story of Your Residency

Now that your audience understands the problem and your objective, has seen the application you built, and knows who built which part of it, each of you can tell the story of *how* you built it. These stories should all tie back to the problem you are trying to solve and should convey clearly how each experience made a positive impact on your team’s ability to build an application that solves the problem. Lastly, these stories will give you an opportunity to show (not tell) your individual contribution on the project. In interviews, these stories will be an essential part of your toolkit for questions like "tell me about a time when you contributed to a team project".

Below are five areas of your AI Residency experience that you should be prepared to speak on during your presentation and in interviews.

And a strong story isn’t always just a clean and easy win. A story about a decision that didn't pan out, a sprint that slipped, or a disagreement you didn't fully resolve, told with a clear account of how you responded to that challenge or what you'd do differently, demonstrates more judgment than a story where everything went smoothly. Don't manufacture drama, and don't hide the parts that were messy! Be honest and find stories that demonstrate your ownership and initiative to get things done.

You also don't need a dramatic story for every category. A quiet, specific moment ("I noticed the ticket didn't specify who owned the endpoint, so I flagged it before it became a blocker") is a real answer. Specificity beats size.

For Part 6, choose one story but all members of the team should be prepared to share anecdotes from the other options during the Q&A

#### Part 4: Product Decision-Making

What you're proving: you understand the target audience and can prioritize the features that matter most to them and solve their problem

Prompts:

- How did your product vision evolve? What research went into deciding on the most important feature to build (your MVP)?
- How did your product vision or scope change as you learned more about the problem?
- What's a call you made about what NOT to build, and why?

**Weak Example:** 

> *"We were deciding between building an add-on app to Google Classroom or building a stand-alone learning management system. We decided to build an add-on app because it seemed easier to build.”*
> 

**Strong Example:**

> *“We were deciding between building an add-on app to Google Classroom or building a stand-alone learning management system. Ultimately, we considered the needs of our target audience (teachers who were looking for better insights into student performance) and determined that having to switch back and forth between the raw data in Google Classroom and the analysis in our application would introduce too much context switching that would distract from their goal. Our research on context switching as a detriment to analysis supported this decision.”*
> 

#### Part 5: Technical Decision-Making

What you're proving: you can weigh trade-offs, not just implement a spec someone handed you.

Prompts:

- Name a real technical fork in the road (a library, an architecture, a feature cut). What were the actual trade-offs — cost, time, complexity, what it enabled? What did you give up by choosing what you chose?

**Weak Example:** 

> *"we needed the ability to send emails to our users through the application. The two leading options were an API called Resend and python’s built-in `smtp` library. We ended up going with Resend because it was easier to use and cheaper.”*
> 

**Strong Example:**

> *“we needed the ability to send emails to our users through the application. The two leading options were an API called Resend and python’s built-in `smtp` library. Resend looked promising at first since all we would have to do is make an API call and Resend would handle the rest, however it required a registered domain which we would need to purchase. Looking into `smtp` revealed that we could send emails in a development environment without a problem, but we would need to a paid tier on our production hosting service Render in order to send emails. After weighing the costs, it turned out that purchasing a domain and using Render would be the cheaper and simpler option overall.”*
> 

#### Part 6a: Teamwork and Collaboration

What you're proving: you can work *with* people, not just alongside them.

Prompts:

- Where did your work depend on someone else's, and how did you coordinate the handoff? (e.g., frontend blocked on a backend endpoint — how did you communicate the need, and what happened if the timing didn't line up?)
- What did you do to keep a teammate who was behind or newer to the stack productive and included, rather than sidelined?
- What's a moment you changed your plan because of what a teammate needed or pushed back on?

**Weak Example:** 

> *“I couldn’t test the login feature because I was waiting for the login endpoint to be implemented. We communicated about this and got the login endpoint implemented allowing me to test it on the frontend."*
> 

**Strong Example:** 

> *“I couldn’t test the login feature because I was waiting for Reuben to finish the login endpoint. We used daily stand down updates which made it easy for me to share this blocker with Reuben who responded, asking for help. Since I had finished my ticket, I was free to support him and fix the bug in the endpoint. It turned out that the JSON wasn’t being parsed from the request body. Once the bug was fixed we were able to quickly test and ship the feature!”*
> 

#### Part 6b: Planning and Documentation

What you're proving: your team's success wasn't an accident — you can run a process.

Prompts:

- Walk through how a sprint actually moved: how tickets got scoped, assigned, and closed. Where did the plan slip, and how did you catch it?
- What's something you noticed wasn't working about how the team operated, and what did you change?
- Where did documentation (a ticket, a written update, a decision doc) save you later — or where did *missing* documentation cost you?

**Weak Example:**

> *“We used a GitHub board to track our tickets and had daily stand-downs to update each other on our progress, which kept everyone on the same page throughout the sprint.”*

**Strong Example:**

> *“We used a GitHub board to track our tickets and had daily stand-down messages in Slack to update each other on our progress, which kept everyone on the same page throughout the sprint. However, during Sprint 1, we had to make an adjustment to ensure that we didn’t do overlapping work."*

During Sprint 1, Priya and I had decided to work on the login feature but ended up both working on the backend endpoint. We’d both picked up overlapping tickets without realizing it and because our daily updates were vague, just ‘working on login’, so we didn’t catch it in time. After this mistake, as Scrum Master I made sure that future tickets had clearly assigned owners and that as a team we gave more detailed stand down updates. After this change we never made this mistake again!”*

#### Part 6c: Navigating Technical and Interpersonal Challenges

What you're proving: you don't fall apart when things break — technically or between people.

Prompts:

- What's the hardest bug you hit, and what was your actual debugging process (not just "I found it")?
- What's a disagreement — technical or personal — that came up on your team, and how did you resolve it? Not every disagreement needs a tidy resolution; "we didn't fully agree but here's how we moved forward" is honest and fine.
- What's something you built specifically because you noticed a user (a student, in your simulated context) was struggling — not because it was on the spec?

**Weak Example:**

> *“We disagreed about whether to store our lesson data as a NoSQL document or in a normal SQL table, but we ended up going with PostgreSQL and it worked out fine.”*
> 

**Strong Example:**

> *“Our Product Lead and our Tech Lead disagreed about how to store lesson content in Postgres. The Product Lead wanted a flexible JSONB column since we were still discovering what a ‘lesson’ actually needed to contain week to week. The Tech Lead pushed back, worried that an unstructured column would make it hard to query and validate student progress later. We didn’t fully agree in that meeting and instead agreed to use a JSONB column for now to keep moving, but write strict validation at the API layer so the data wasn’t completely unstructured underneath. That let both people keep their core concern addressed without either one just overriding the other, and we revisited the schema once we actually knew what a lesson needed to hold."*
> 

---

### Part 7: Q&A

Be prepared to answer the following questions:

1. What was unfamiliar going in (Python/FastAPI, AI tooling, whatever applies)? What was your actual process for learning and getting productive — concrete, not "I studied harder."
2. How did you use AI to speed up your development? What were examples where you had to push back on what AI gave you and tell it to create something else?
3. How would your approach be different if you were to do this project again knowing what you know now?
4. What would you build next if you had more time?

For these questions, just like the stories above, be prepared to go in depth.

**Weak Example (Question 1):**

> *“We didn’t know Python before this project, so we spent the first week learning the basics and then started building with FastAPI once we felt ready.”*
> 

**Strong Example (Question 1):**

> *“Coming from Express, I was used to writing middleware and passing things like a session cookie down manually through each route. FastAPI’s dependency injection system didn’t make sense to me at first — I kept re-creating a new database session inside every single route function instead of sharing one. I asked Claude to explain why FastAPI uses `Depends()` instead of middleware, and had it refactor one of our routes to use it properly. Seeing the diff side by side is what made it click: `Depends()` was solving the exact same problem Express middleware solves, just scoped per-route instead of globally. Once I understood that, I refactored the rest of our routes myself without needing to ask again.”*
