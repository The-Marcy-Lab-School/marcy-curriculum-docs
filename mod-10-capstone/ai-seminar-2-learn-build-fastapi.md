# Using AI as a Learning and Building Tool

{% hint style="info" %}
Follow along with the repo [here](https://github.com/The-Marcy-Lab-School/fastapi-ai-practice)!
{% endhint %}

**Table of Contents:**
- [The New Distribution of Work](#the-new-distribution-of-work)
- [Phase 1: Plan Before You Prompt](#phase-1-plan-before-you-prompt)
- [Phase 2: Let AI Build](#phase-2-let-ai-build)
- [Phase 3: Review, Understand, and Own It](#phase-3-review-understand-and-own-it)
- [The Test Before Every PR](#the-test-before-every-pr)
- [Using AI to Learn What Was Built](#using-ai-to-learn-what-was-built)
- [What This Looks Like in Practice](#what-this-looks-like-in-practice)

The way software gets built has changed. AI tools like Claude Code can generate working, production-quality code in seconds. Fighting that reality is not a useful use of your energy. Learning to work with it — deliberately, professionally, and in a way that actually makes you a stronger engineer — is.

This guide is built around one central idea: **AI changes *where* your effort goes, not how much effort you put in.**

---

## The New Distribution of Work

Before AI tools, building a feature looked something like this:

| Phase                    | Before AI |
| ------------------------ | --------- |
| Planning                 | 30%       |
| Execution (writing code) | 60%       |
| Revision & Review        | 10%       |

With AI, it looks more like this:

| Phase                    | With AI |
| ------------------------ | ------- |
| Planning                 | 45%     |
| Execution (writing code) | 10%     |
| Revision & Review        | 45%     |

Notice what this means: **the total effort doesn't go down.** What changes is where it goes. The mechanical work of typing out implementations shrinks dramatically. The intellectual work — thinking through what you're building and understanding what was built — expands to fill that space. Fellows who treat AI as a way to skip that intellectual work will produce code they can't explain, defend, or debug. That catches up with you fast.

---

## Phase 1: Plan Before You Prompt

The quality of what AI produces is almost entirely determined by the quality of what you ask for. Vague input produces vague output. This means planning is now the highest-leverage thing you can do.

Before you write a single prompt, you should be able to answer:

- **What problem am I solving?** Not "I need a route," but "I need a `PATCH /todos/:id` endpoint that accepts partial updates and returns a 404 if the ID doesn't exist."
- **What does the data look like going in and coming out?** Define your inputs, outputs, and edge cases explicitly.
- **What are the components of this feature?** Break it into the smallest pieces you can name.
- **What do I already know that's relevant?** If you've solved something similar before, in any language or framework, name it. That prior knowledge becomes the reference frame for understanding what AI gives you.

A well-scoped prompt sounds like: *"I need a PATCH endpoint that takes an ID as a URL parameter and a request body with optional fields. If the ID doesn't exist, return a 404. Otherwise update only the fields provided and return the updated object."*

A poorly scoped prompt sounds like: *"Add an update route."*

The second prompt will produce code too. But you'll spend the next hour trying to figure out if it's actually doing what you need.

**Plan on paper before you open Claude Code.** Write it out — even a few lines. The act of writing forces clarity that typing into a prompt box does not.

---

## Phase 2: Let AI Build

Once your plan is solid, give AI room to execute. This is the part that feels like cheating but isn't — provided you do Phase 3.

A few things to know about working with AI during execution:

**One piece at a time.** Ask for one component, one route, one function. Asking for the whole feature at once makes Phase 3 much harder. If you can't hold the scope of what was generated in your head, you can't review it effectively.

**Use your prior knowledge as a reference frame.** If you know how something works in JavaScript, ask for the equivalent. "I know how middleware works in Express — what's the FastAPI equivalent and why?" is a much better prompt than "How do I add middleware?" It anchors the explanation to something you already understand.

**Push back and ask follow-up questions.** If AI gives you something you don't immediately understand, don't move on. Ask it to explain a specific line. Ask what would happen if you changed something. Ask if there's a simpler approach. You are the engineer; AI is the tool.

**Let it generate. Then stop.**

Do not read the output and immediately run it or paste it in. The next phase is not optional.

---

## Phase 3: Review, Understand, and Own It

This is where the learning happens. It is also the phase most people skip when they're in a hurry — and the phase that determines whether you can actually call yourself the engineer who built this.

**Read every line before you run it.** Not to verify syntax. To understand intent. For each section, ask yourself: do I know what this is doing? If not, that is your next question for AI.

**Predict before you test.** Before running the code, write down what you expect to happen. Then run it. If the behavior matches your prediction, your mental model is accurate. If it doesn't, that gap is something to investigate — not ignore.

**Add comments in your own words.** Not code comments that describe *what* the code does — those are for future readers. Comments that describe *why* and how it connects to things you already know. Writing forces understanding in a way that reading does not. If you can't write the comment, you don't understand it yet.

**The hard rule: don't keep code you can't explain.** If someone on your team or an interviewer pulled up this file and asked you to walk them through it, could you do that confidently? If the answer is no, keep working until it is yes. This isn't about having memorized everything — it's about being able to reason about the code in front of you.

---

## The Test Before Every PR

Before you submit a pull request, ask yourself:

1. Can I explain what every file I touched is doing?
2. Can I explain why the approach I used makes sense?
3. If something breaks in production, do I have enough understanding to debug it?

If any of those answers is no, go back to Phase 3.

---

## Using AI to Learn What Was Built

Sometimes you'll review code and realize you don't understand a piece of it. That's not failure — that's exactly when AI is most useful. Use it to close the gap:

- *"You generated this code: [paste]. I don't understand what this specific part is doing. Don't rewrite it — explain what it does and why it's structured this way."*
- *"Here's my understanding of this code: [your explanation]. Is that right? What am I missing?"*
- *"What would happen if I changed this line to X? Why?"*

The goal of these prompts is to build your mental model, not to produce more code. You are using AI as a tutor, not a ghostwriter.

---

## What This Looks Like in Practice

| Situation                            | What to do                                                                        |
| ------------------------------------ | --------------------------------------------------------------------------------- |
| Starting a new feature               | Plan on paper first. Define inputs, outputs, edge cases. Then prompt.             |
| AI gives you working code            | Read every line before running it. Add comments. Predict behavior before testing. |
| You don't understand a line          | Ask AI to explain that specific line without rewriting it.                        |
| You think you understand something   | Write your explanation in your own words and ask AI to verify it.                 |
| You're about to open a PR            | Run the three-question test above.                                                |
| Someone asks you how your code works | You should be able to answer without looking at it.                               |

---

*The engineers who will stand out — in the Engineering Fair, in interviews, and on the job — are not the ones who generated the most code. They're the ones who understood what they built well enough to explain it, defend it, and improve it. That understanding is what this process is designed to build.*