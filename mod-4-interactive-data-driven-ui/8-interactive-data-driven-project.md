# Interactive & Data-Driven User Interfaces Project

**Table of Contents:**

- [Overview](#overview)
  - [Deliverables](#deliverables)
  - [Stretch Features: localStorage + AI](#stretch-features-localstorage--ai)
  - [Milestones](#milestones)
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
* A scrum board is a tool for creating and assigning tasks ("tickets") within a project. Typically, a scrum board will organize tasks into three columns: _Backlog_ (tasks waiting to be assigned), _In Progress_ (assigned tasks that are being worked on), and _Done_ (completed tasks).
  * [Sample "In Progress" Example](https://github.com/users/benspector-mls/projects/3/views/1?layout=board)
  * [We Cooked Scrum Board](https://github.com/orgs/we-cooked/projects/1/views/1)* Examples:

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

Eventually, you will need to develop the skill of planning out your objectives. However, for this project we've laid out some daily milestones that you can strive for with bare minimum requirements, ideal progress, and stretch goals:

{% tabs %}
{% tab title="Day 1" %}
* **Bare Minimum:** 
  * [ ] Product Spec Sheet is complete with API endpoints, user stories, and a wireframe that shows thoughtful idea about what data is displayed.
* **Ideal:** 
  * [ ] GitHub Organization and Repo is created
* **Stretch:** 
  * [ ] Get started putting together your scrum board with tickets
* Note: If you have not submitted your proposal and are undecided about your API by this deadline, you will be assigned an API and product spec sheet by your Engineering Manager.
{% endtab %}

{% tab title="Day 2" %}
* **Bare Minimum:** 
  * [ ] All day 1 goals are completed.
  * [ ] Scrum board is populated with tasks. Individuals have assigned tasks.
  * [ ] The basic HTML, CSS, and JavaScript files for your project are created (the "skeleton"). 
* **Ideal:**
  * [ ] The HTML structure is complete
  * [ ] You have functions to fetch from your two (minimum) endpoints and have tested them / logged data to the console
* **Stretch:**
  * [ ] You have functions to render fetched data to the page on page load
{% endtab %}

{% tab title="Day 3" %}
* **Bare Minimum:** 
  * [ ] All day 1 and day 2 goals are completed.
  * [ ] You can fetch AND render data for your two endpoints
* **Ideal:**
  * [ ] You have an event handler that triggers one of the fetches and renders.
* **Stretch:**
  * [ ] Layout (flexbox / grid) and basic styling is added with responsive design.
{% endtab %}

{% tab title="Day 4" %}
* **Bare minimum:**
  * [ ] All day 1, 2, and 3 goals are completed.
  * [ ] Layout (flexbox / grid) and basic styling is added with responsive design.
* **Ideal:**
  * [ ] Project is deployed (build the Vite project and push to main)
  * [ ] Presentation slides are created and posted on Canvas
  * [ ] Begin implementing localStorage stretch feature
* **Stretch:** 
  * [ ] A stretch feature is completed
{% endtab %}

{% tab title="Day 5" %}
* **Bare minimum:**
  * [ ] All day 1, 2, 3, and 4 goals are completed.
  * [ ] The localStorage stretch feature is completed
* **Ideal:**
  * [ ] Presentation slides are complete with a recorded demo video
  * [ ] GitHub Repo README is polished
* **Stretch:** 
  * [ ] Additional stretch features
{% endtab %}
{% endtabs %}

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
