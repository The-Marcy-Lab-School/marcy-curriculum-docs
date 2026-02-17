# Interactive & Data-Driven User Interfaces Project

**Table of Contents:**

- [Overview](#overview)
  - [Deliverables](#deliverables)
  - [Stretch Features: localStorage + AI](#stretch-features-localstorage--ai)
  - [Milestones](#milestones)
  - [Git Workflow for Paired Projects](#git-workflow-for-paired-projects)
    - [The Golden Rule](#the-golden-rule)
    - [Branch Strategy](#branch-strategy)
    - [The Workflow (Repeat for Every Ticket)](#the-workflow-repeat-for-every-ticket)
    - [Dividing the Work into Tickets](#dividing-the-work-into-tickets)
    - [Handling Merge Conflicts](#handling-merge-conflicts)
    - [Daily Routine](#daily-routine)
    - [Common Mistakes to Avoid](#common-mistakes-to-avoid)
- [Project Grading](#project-grading)
  - [Fetching Requirements (8 points)](#fetching-requirements-8-points)
  - [User Interface: Structure and Accessibility (12 points)](#user-interface-structure-and-accessibility-12-points)
  - [General (9 points)](#general-9-points)
  - [Form Requirements (9 points)](#form-requirements-9-points)
- [Tips for Success](#tips-for-success)
  - [Choosing an Easy API to Work With](#choosing-an-easy-api-to-work-with)
  - [Focus on demonstrating your learning, not building something unique.](#focus-on-demonstrating-your-learning-not-building-something-unique)
  - [Make Decisions Quickly](#make-decisions-quickly)
  - [Plan and Delegate!!](#plan-and-delegate)
  - [Meet on your own time](#meet-on-your-own-time)
  - [Test your API!](#test-your-api)

## Overview

It's time for your first paired project week! For this project, you and a partner will build an application that interacts with a third-party API. You will fetch data, render the fetched data to the DOM, and then fetch some more data! In the end, you will have a deployed interactive application that connects with a web API.

The main technical goal of your app is to **send two fetches** and render the fetched data:

1. First, fetch a collection of resources (GET a set of users, a set of pictures, a set of authors) and render all of those resources.
2. Fetch more details about a single one of those resources (GET details about a single user, a single picture, a single author, etc…) when the user interacts with the page.

By building this project, you will demonstrate the following technical skills:
* Structuring HTML with semantic elements
* CSS layout with Flexbox and Grid
* Media Query Responsiveness
* JavaScript form handling
* Modular Code with ES Modules
* Event handling and event delegation

Additionally, you will:

* Develop teamwork skills including communication, problem-solving, compromise, and task management
* Strengthen your Git workflow fundamentals, managing branches, navigating merge conflicts, and maintaining a repository
* Practice Agile methodologies by managing a scrum board and by performing daily stand-up and stand-down
* Deploy a project
* Practice your technical communication through a presentation

### Deliverables

Unlike your solo projects where the project itself was the main deliverable, for a portfolio-level project, you will be required to do a bit more. The deliverables below will help keep you and your partner organized and will help keep your Engineering Manager (your instructor) informed on your progress. In the end, all of these deliverables will serve as valuable artifacts that document your work.

**Deliverable Resources**
* [Project Spec Sheet Template](https://docs.google.com/document/d/1gSM_RWRAP_EJGQ7pp58Pti6PXKQJ_YWx3uvC4iRQ1EI/edit)
* [How to Create a Scrum Board](../projects/2-how-to-create-scrumboard.md)!
* [Template Scrum Board](https://github.com/users/benspector-mls/projects/7/views/1)
* [Presentation Template](https://docs.google.com/presentation/d/1NvzL4yUaVSNFlmNtG3shNdK4j0oW3Ll2zbq8gK2vrzQ/edit?usp=sharing) — make sure to hit all of the talking points in the template but feel free to modify the design!

Expand each below to see details and examples.

**<details><summary>A Deployed Project</summary>**

* Your project must meet all of the grading requirements listed below. 
* Each project is deployed on GitHub using an Organization
* Examples:
  - [Art Viewer (Art Institute of Chicago API)](https://art-viewer-mike-example.github.io/art-viewer-day-4/)
  - [Forecast It Already (Open Meteo Weather API)](https://forecast-it-already.github.io/Forecast-It-Already/)
  - [We Cooked (TheMealDB)](https://we-cooked.github.io/cooked/)
  - [Pokedex (PokeAPI)](https://phoenix-pokedex.github.io/Phoenix-Pokedex/)
  - [Buldak (Jikan Anime/Manga API)](https://bul-dak.github.io/anime-web/)

</details>

**<details><summary>A Project Spec Sheet</summary>**

* A project spec sheet is a document that provides key details about the project including the API being used, the intended features/user stories, and wireframes.
* Examples:
 * [Art Viewer Product Spec Sheet](https://docs.google.com/document/d/1ekNpWx_LKCFGPQ74qijXTMRJJoq4sHJIB1mv9Mg8HHk/edit)

</details>

**<details><summary>A GitHub Organization and Scrum Board</summary>**

* A GitHub Organization is a shared account on GitHub where you and your teammates can collaborate on repositories. Having a GitHub organization will make deployment straight forward.
  * [We Cooked Organization](https://github.com/we-cooked)
* A scrum board is a tool for creating and assigning tasks ("tickets") within a project. Typically, a scrum board will organize tasks into these columns: _Backlog_ (tasks waiting to be assigned), _In Progress_ (assigned tasks that are being worked on), _In Review_ (tasks to be reviewed), and _Done_ (completed tasks).
* Examples:
  * [We Cooked Completed Scrum Board](https://github.com/orgs/we-cooked/projects/1/views/1)*
  * [Sample "In Progress" Example](https://github.com/users/benspector-mls/projects/3/views/1?layout=board)
  * [Starting Template To Copy](https://github.com/users/benspector-mls/projects/7/views/1)

</details>

**<details><summary>A Professional README</summary>**

Your `README.md` should be polished enough that a new engineer can understand and run your project without asking questions.

Example: [We Cooked Repository README](https://github.com/we-cooked/cooked)

At minimum, include:

* Project title and one-paragraph summary
* Live deployed link
* Team members
* API used (name + endpoints)
* Features section
  * List your 2 MVP features
  * List your stretch feature(s)
* Setup instructions
  * `npm install`
  * `npm run dev`
* Tech stack (`HTML`, `CSS`, `JavaScript`, `Vite`, etc.)
* Screenshots or GIFs
* Known limitations or future improvements

Use the same professional writing standards we expect in all artifacts:

* Clear section headers
* Correct spelling/grammar
* Consistent formatting
* No placeholder text

</details>

**<details><summary>AI Usage Document (Required)</summary>**

Generative AI tools are a required part of this project workflow, but you must be able to explain every line of code you submit.

Make a copy of this [AI Usage Document](https://docs.google.com/document/d/1xxhC0GWMqWOzoeY7_aCX8ERWY6avB4RrS5LAqla8CSI/copy) and track your usage.

For each meaningful AI interaction, you are required to document:

1. What you asked for (your prompt)
2. What you received (the AI response)
3. What you changed and why
4. How the final code works (in your own words)

Add a link to your AI Usage Document in your `README.md`, for example:

```md
This project provided us an opportunity to build with AI assistance. Check out our [AI Usage Document](PASTE_LINK_HERE) to see how we used AI on this project.
```

</details>

**<details><summary>A Presentation + Walkthrough Demo Video</summary>**

* The presentation is your "elevator pitch" to tell your audience about the project, how you built it, and what you learned through the process.
* Examples:
  * [We Cooked Presentation](https://docs.google.com/presentation/d/1fm1dWFZofhChPFYlG4CcbOKGoE5ziJWtFNuzhKytm7k/edit?slide=id.g327ab0f0e7e_0_559#slide=id.g327ab0f0e7e_0_559) — This example does an excellent job with their demo recording, walking the audience through the features of the application.
  * [Pickle Productions Presentation](https://docs.google.com/presentation/d/1Jbn9q0Pc8iUsJGCY93X3RoSkMVg0zSzHz7xFzVUuON4/edit) — This example does an excellent job at explaining their technical challenge and teamwork learnings.

</details>

### Stretch Features: localStorage + AI

Use AI to help you learn and implement `localStorage` for a real user-facing feature in your app.

Your localStorage stretch feature should:

* Store user preferences or user-generated state in the browser
* Persist after page refresh
* Be loaded on page start and reflected in the UI
* Include a clear way for users to update the stored value

Feature ideas:

* Save the most recently selected item and re-render it on reload
* Save filter/search form values so they persist on refresh
* Save a favorites list of fetched items
* Save a theme preference (light/dark) or display settings

Suggested AI workflow:

1. Ask AI: "How does `localStorage` work in JavaScript, and when should I use it?"
2. Ask AI: "Help me implement `<your feature>` with `localStorage` in a Vite app."
3. Ask AI to review your implementation for edge cases:
   * Missing keys
   * Invalid JSON
   * Clearing stale data
4. Refactor the code to match your project structure and naming conventions.
5. Document the full interaction in your AI Usage Document.

Quality bar for this stretch:

* `localStorage` reads/writes are wrapped in helper functions
* JSON parsing/stringifying is handled safely
* UI still works when `localStorage` is empty
* The feature is demonstrated in your walkthrough video

### Milestones

As you work on this project, don't set your sights on the fully completed project from the jump. Give yourself smaller goals that you can reach each day and focus on one goal at a time. As long as your goals build on each other, you will eventually reach your final destination.

For this Art Viewer project, you can see the progress made on each day.

* [https://art-viewer-mike-example.github.io/art-viewer-day-1](https://art-viewer-mike-example.github.io/art-viewer-day-1/) (just the app is deployed. It doesn't do anything yet)
* [https://art-viewer-mike-example.github.io/art-viewer-day-2](https://art-viewer-mike-example.github.io/art-viewer-day-2/) (we can fetch and then render a list of pictures)
* [https://art-viewer-mike-example.github.io/art-viewer-day-3](https://art-viewer-mike-example.github.io/art-viewer-day-3) (we can click on a picture to fetch more details about that one picture)

_Note: no need to create a new repo for each day - this is just done this way for demonstration purposes._

Eventually, you will need to develop the skill of planning out your objectives. However, for this project we've laid out some daily milestones that you can strive for with bare minimum requirements, ideal progress, and stretch goals. Remember: a feature isn't "done" until it's been reviewed and merged to `main` via a PR.

{% tabs %}
{% tab title="Day 1" %}
* **Bare Minimum:**
  * [ ] Product Spec Sheet is complete with API endpoints, user stories, and a wireframe that shows thoughtful idea about what data is displayed.
  * [ ] Scrum board is populated with tickets (use the ticket breakdown above as a starting point). Partners have assigned themselves tickets.
* **Ideal:**
  * [ ] GitHub Organization and Repo is created.
  * [ ] Vite project skeleton is set up pushed — both partners do this together on `main`. This is the one time you push directly to `main`.
* **Stretch:**
  * [ ] Partners each create their first feature branch and start working.
* Note: If you have not submitted your proposal and are undecided about your API by this deadline, you will be assigned an API and product spec sheet by your Engineering Manager.
{% endtab %}

{% tab title="Day 2" %}
* **Bare Minimum:**
  * [ ] All Day 1 goals are completed.
  * [ ] Each partner has opened and merged at least one PR (e.g., Partner A: fetch function, Partner B: form HTML).
* **Ideal:**
  * [ ] Fetch functions for both endpoints are written, tested in the console, and merged to `main`.
  * [ ] HTML structure is complete.
* **Stretch:**
  * [ ] Render functions are written and merged. Fetched data appears on the page.
{% endtab %}

{% tab title="Day 3" %}
* **Bare Minimum:**
  * [ ] All Day 1–2 goals are completed.
  * [ ] Both fetches render data to the page (merged to `main`).
* **Ideal:**
  * [ ] Event handling is wired up — clicking an item fetches and displays details, or submitting the form triggers a fetch. These are merged via PRs.
* **Stretch:**
  * [ ] Layout (flexbox / grid) and basic responsive styling is added.
{% endtab %}

{% tab title="Day 4" %}
* **Bare minimum:**
  * [ ] All Day 1–3 goals are completed.
  * [ ] Responsive layout is merged.
  * [ ] All open branches are merged or closed — `main` represents your working app.
* **Ideal:**
  * [ ] Project is deployed (build the Vite project and push to `main`).
  * [ ] Presentation slides are created and posted on Canvas.
  * [ ] Begin implementing localStorage stretch feature (on a new branch!).
* **Stretch:**
  * [ ] localStorage feature is merged.
{% endtab %}

{% tab title="Day 5" %}
* **Bare minimum:**
  * [ ] All Day 1–4 goals are completed.
  * [ ] The localStorage stretch feature is merged. No open branches remain.
* **Ideal:**
  * [ ] Presentation slides are complete with a recorded demo video.
  * [ ] GitHub Repo README is polished with screenshots, live link, and AI Usage Doc link.
* **Stretch:**
  * [ ] Additional stretch features.
{% endtab %}
{% endtabs %}

### Git Workflow for Paired Projects

This is likely your first time collaborating on code with another person using Git. The workflow below is intentionally simple. Follow it closely and you'll avoid most of the headaches that come from working in the same codebase.

#### The Golden Rule

**Never push directly to `main`.** All work happens on branches. Code gets into `main` through Pull Requests (PRs) that your partner reviews.

#### Branch Strategy

Use **feature branches** — short-lived branches where each branch corresponds to one ticket on your scrum board.

Branch naming convention: `feature/short-description`

Examples:
- `feature/fetch-all-items`
- `feature/render-item-list`
- `feature/single-item-section`
- `feature/form-html`
- `feature/responsive-layout`

#### The Workflow (Repeat for Every Ticket)

```
1. Pull the latest main
   git checkout main
   git pull origin main

2. Create a new branch for your ticket
   git checkout -b feature/your-ticket-name

3. Do your work, committing as you go
   git add .
   git commit -m "Add fetch function for all artworks"

4. Push your branch to GitHub
   git push origin feature/your-ticket-name

5. Open a Pull Request on GitHub
   - Base: main ← Compare: feature/your-ticket-name
   - Assign your partner as a reviewer

6. Your partner reviews the PR
   - Look it over, leave comments or approve
   - The reviewer merges the PR on GitHub

7. Both partners pull the updated main
   git checkout main
   git pull origin main
```

Then start the next ticket from step 1.

#### Dividing the Work into Tickets

The key to working in parallel is to **split work by feature, not by file**. Each ticket should be a vertical slice — the HTML, CSS, JS, and fetch logic needed for one piece of functionality.

Here is a suggested way to break the project into tickets. The ticket numbers suggest a rough order, but tickets at the same level can be worked on in parallel by different partners.

**Setup (do together, Day 1) — this is the one time you push directly to `main`**
1. **Project Setup** — Create GitHub Organization, repo, and clone locally. Set up Vite project with initial file structure (`index.html`, `styles.css`, `app.js`)
2. **Assign Tickets** — Assign tickets in the backlog to each team member.

**Partner A: Fetch & Render All Items:**
3. **Collection section HTML & CSS** — Create a `<section>` for displaying the collection of data with a `h2` and a `ul` to be filled using JavaScript
4. **Fetch all items** — Write a fetch function that GETs the collection endpoint and logs the data. Include `async/await`, `try/catch`, `response.ok` check.
5. **Render item list** — Write a function that takes the fetched array and renders `<li>` elements inside the `<ul>` in the collection `<section>`. Use `createElement`
6. **Responsive grid layout** — Style the item list using CSS Flexbox or Grid. Add a media query so items stack on mobile and sit side-by-side on desktop.

**Partner B: Fetch & Render One Item:**
7. **Single item section HTML + CSS** — Create the `<section>` for displaying a single fetched item with its `<h2>` and placeholder content.
8. **Fetch single item** — Write a fetch function that GETs the "single item" endpoint and logs the data. Include `async/await`, `try/catch`, `response.ok` check.
9. **Fetch on Click** — Add an event listener (using event delegation on the `<ul>`) that fetches details for one item and renders them in the single-item section.

**Either partner (after earlier tickets merge):**
10. **Build the form HTML** — Create the `<form>` with labels, inputs, and `name` attributes for each input.
11. **Form event handling** — Add a submit event listener that prevents default, reads input values, clears the form, and triggers a fetch (or filters results).
12.  **Connect form to fetch** — Wire up the form so submitting it triggers the collection fetch with the form's input value (e.g. a search query or filter).
13.  **Polish and accessibility pass** — Confirm semantic HTML, single `<h1>`, single `<main>`, `<title>` in head, no divs replacing semantic elements.
14.  **README and deployment** — Write the professional README, link AI Usage Doc, final deploy.

#### Handling Merge Conflicts

Merge conflicts happen when both partners edit the same lines in the same file. To minimize them:

- **Communicate constantly.** Tell your partner what file you're working in.
- **Keep branches short-lived.** Merge PRs quickly (within hours, not days). The longer a branch lives, the more likely it conflicts with `main`.
- **Pull `main` before starting a new branch.** This ensures you're always building on the latest code.

When a conflict does happen:
1. Pull `main` into your feature branch: `git merge main`
2. Git will mark the conflicting lines in the file. Open the file and look for the `<<<<<<<`, `=======`, `>>>>>>>` markers.
3. **Talk to your partner** about which version to keep (or how to combine them).
4. Edit the file to resolve the conflict, removing the markers.
5. `git add .` and `git commit` to complete the merge.
6. Push your branch and continue with the PR.

#### Daily Routine

| Time               | Activity                                                                                             |
| ------------------ | ---------------------------------------------------------------------------------------------------- |
| **Start of day**   | Stand-up: What did you do yesterday? What are you doing today? Any blockers?                         |
| **During the day** | Work on your ticket. Commit often. Push and open a PR when done. Review your partner's PRs promptly. |
| **End of day**     | Stand-down: What got done? What's next? Make sure all open PRs are merged so tomorrow starts clean.  |

#### Common Mistakes to Avoid

- **Working on `main` directly.** You'll overwrite each other's work.
- **Giant branches that touch every file.** Keep tickets small and focused.
- **Not pulling before branching.** You'll end up working on stale code.
- **Ignoring your partner's PR.** Review quickly — your partner is blocked until you do.
- **Both editing `index.html` at the same time.** Coordinate who's touching shared files, or accept that you'll need to resolve a merge conflict.

## Project Grading

We care deeply about the quality of the code that is produced at Marcy. Even if your application works as described, was it created following best practices? Did you effectively use all of the skills from the module? Were you able to learn and grow from this project-building process?

To ensure that your project meets Marcy's standards of excellence, your project will be assessed on the following criteria (38 points total):

### Fetching Requirements (8 points)

* [ ] A fetch is made to an API that returns an array of data.
* [ ] A fetch is made to an API that returns a single data value
* [ ] On page load, a fetch call is made, rendering initial fetch data to the screen
* [ ] Asynchronous code uses `async` and `await`
* [ ] Fetch calls are made using a `try` / `catch` block
* [ ] Errors are handled by `console.warn()`
* [ ] `response.ok` is checked before parsing
* [ ] Throw a new error if the response is NOT ok

### User Interface: Structure and Accessibility (12 points)

* [ ] The `head` has a `title`
* [ ] There is a single `main` element on the page
* [ ] There is a single `h1` element on the page
* [ ] There is a `section` for the rendering an array of fetched data
  * [ ] The section has an `h2`
  * [ ] The section has a `ul` with `li` items that display the fetched data
  * [ ] Fetched items appear next to each other in a grid-like pattern (flex or grid presentations fine)
* [ ] There is a `section` for displaying a single fetched item
  * [ ] The section has an `h2`
  * [ ] The section displays information about the single fetched item
* [ ] There are no instances of recreating any semantic elements (using a `div` for a `nav`)
* [ ] The entire application is responsive with mobile and desktop compatibility (we recommend a mobile-first approach)

### General (9 points)

* [ ] The project is created using Vite
* [ ] The code exists in more than one JS file
* [ ] The project is deployed via GitHub Pages properly
* [ ] CSS Flexbox or Grid was used
* [ ] The code does not render unescaped text directly to the DOM (`createElement` or other escape method used)
* [ ] The repository has a polished `README.md` with: project summary, live link, team members, API details, setup instructions, and feature list (MVP + stretch)
* [ ] The `README.md` includes screenshots or GIFs and uses professional writing (clear headers, no placeholder text, minimal grammar/spelling errors)
* [ ] The AI Usage Document is linked in `README.md`
* [ ] The AI Usage Document includes meaningful logged entries with: prompt, AI response, what changed/why, and how the final code works

### Form Requirements (9 points)

* [ ] There is a `form` for triggering a fetch or for modifying the fetch results
  * [ ] `form` elements have an aria-label or aria-labelledby attribute that describes the form
  * [ ] The form has an `h2`
  * [ ] The form has at least one `input`
  * [ ] Every `input` element has an associated `label` element (it can be invisible but it must be in the HTML)
  * [ ] The form has a `button` to submit the form
* [ ] At least one field is a required field, and the form cannot be submitted without it
* [ ] Clicking the form submit button does not reload the page because the default behavior is prevented
* [ ] Clicking the form submit button clears the form

## Tips for Success

### Choosing an Easy API to Work With

Choose an API from this [list of free APIs](../projects/4-free-apis.md) as they are all free and, importantly, do not require an API key.

If you are able to find another API that is free and does not require an API key, you are free to use it, but do not waste time on this search nor trying to get a tricky API to work (don't use the Spotify API. Just don't. Please.)

### Focus on demonstrating your learning, not building something unique.

One of the easiest ways to get off track is by worrying about creating a unique project

Remember:
* **Student projects are meant for learning, demonstrating your learning, and practicing talking about your technical work**
* Student projects are not meant to be unique.
* You're not creating a new app that addresses a gap in the market.
* You aren't deploying this app for the purpose of having real users.

### Make Decisions Quickly

You may have a great idea that you really want to implement, but remember, **you are working as a team and you only have a week**. Time is not on your side and the more time you waste disagreeing with your partner about what features to implement or what API to use, the less time you'll have to actually build the thing.

### Plan and Delegate!!

* Use branches and make PRs
* Use your scrum board
* Assign tasks
* Don't do duplicate work

### Meet on your own time

Be in alignment with your teammate outside of the scheduled meetings with your Engineering Manager.

### Test your API!

Actually write a fetch to make sure that the API works before choosing it for your proposal
