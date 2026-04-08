# 10. Case Study: Social Bookmark Manager (Part 2)

{% hint style="info" %}
Case study repo: [swe-casestudy-6-social-bookmark-manager](https://github.com/The-Marcy-Lab-School/swe-casestudy-6-social-bookmark-manager). Full investigation guide: [case-study.md](./case-study.md).
{% endhint %}

Yesterday you oriented yourself to the codebase, studied the schema and API, and traced the first two flows — the public feed loading and the registration form. Both scenarios were fully worked examples; your job was to read and understand them.

Today the scaffolding comes off. Scenarios 3–5 ask you to draw the sequence diagrams yourself before checking the answers. Then you'll read the model, middleware, controller, and frontend files with guided questions that go deeper into the auth logic you previewed yesterday.

By the end of today you should be able to trace any request in this app from the browser to Postgres and back — and explain every decision along the way.

**Table of Contents**

- [A Note on the Auth Code](#a-note-on-the-auth-code)
- [Today's Investigation](#todays-investigation)

## A Note on the Auth Code

You haven't formally learned bcrypt, sessions, or auth middleware yet — those are the next three lessons. But you've now seen them in a working app. As you read `userModel.js`, `checkAuthentication.js`, and `authControllers.js` today, focus on *what* the code does rather than *how* you would build it from scratch. The guided questions will direct your attention to the right details.

When you hit something unfamiliar, that's useful information: it tells you exactly what to watch for in the upcoming lessons.

## Today's Investigation

Continue in [the case study](./case-study.md):

1. **Scenario 3** — draw your own sequence diagram first, then expand the answer to check
2. **Scenario 4** — draw your own sequence diagram first, then expand the answer to check
3. **Scenario 5** — draw your own sequence diagram first (all three outcome paths), then expand to check
4. **Guided Reading** — answer the questions for each file in this order:
   - `server/models/userModel.js`
   - `server/models/bookmarkModel.js`
   - `server/middleware/checkAuthentication.js`
   - `server/controllers/authControllers.js`
   - `server/controllers/bookmarkControllers.js`
   - `frontend/src/fetch-helpers.js`
5. **Concepts Checklist** — go through every item and honestly self-assess. Anything unchecked is something to ask about before moving on to auth.
