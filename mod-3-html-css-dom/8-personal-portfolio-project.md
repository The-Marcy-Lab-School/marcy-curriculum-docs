# Portfolio Project

**Table of Contents:**
- [Project Overview](#project-overview)
  - [Sample Designs Investigation](#sample-designs-investigation)
  - [Telling Your Story](#telling-your-story)
  - [AI Usage (It's Required!)](#ai-usage-its-required)
    - [AI Usage Guidelines](#ai-usage-guidelines)
  - [Science Fair Presentation and Reflection Questions](#science-fair-presentation-and-reflection-questions)
  - [Project Requirements and Assessment Criteria](#project-requirements-and-assessment-criteria)
  - [Summary](#summary)
- [Getting Started](#getting-started)
  - [1. Repository Setup](#1-repository-setup)
  - [2. Set up your file structure](#2-set-up-your-file-structure)
  - [3. Follow the Roadmap](#3-follow-the-roadmap)
    - [Checkpoint 1: Planning and Structure (Friday)](#checkpoint-1-planning-and-structure-friday)
    - [Checkpoint 2: Mobile Styling (Saturday - Sunday)](#checkpoint-2-mobile-styling-saturday---sunday)
    - [Checkpoint 3: Responsive Design (Monday)](#checkpoint-3-responsive-design-monday)
    - [Checkpoint 4: Polish and Deploy (Tuesday)](#checkpoint-4-polish-and-deploy-tuesday)

## Project Overview

For this solo project, you will build a personal portfolio website using HTML and CSS.

A personal portfolio website is often the first impression employers, collaborators, and clients have of you as a developer. It is a platform to showcase your skills, projects, creativity, and personality.

By crafting your own site from scratch, you gain essential experience with real-world web development and create a powerful tool to advance your career.

You will then have an opportunity to present your project to your peers, sharing the challenges you faced along the way and your key learnings from the project.

**📋 Key Deliverables:**

| Deliverable                   | Description                                                                                                    |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Portfolio Website**         | A responsive personal portfolio deployed to `username.github.io`                                               |
| **AI Usage Document**         | A Google Doc logging your AI interactions (prompts, responses, changes, explanations). Link it in your README. |
| **Reflection Responses**      | Written responses to the 3 reflection questions, added to your README.md                                       |
| **Science Fair Presentation** | A 15-minute presentation slot where you'll demo your site and discuss your reflections                         |

### Sample Designs Investigation

Portfolio websites most commonly have the following:

* **Hero Section** — an overview/introduction with your name, role or tagline, and a visual or statement that represents you.
* **About Me** — a section or page with a friendly biography, photo, your story, and highlights of your skills or values.
* **Projects/Work** — a showcase of 3-5 projects, each with a title, description, technologies used, images or demos, and links (like GitHub or live sites).
* **Contact** — ways to reach you, such as an email form, email address, social media links, and optionally a downloadable resume.
* **Navigation Menu** — easy-to-use navigation for switching between sections/pages (often as a top nav bar).
* **Consistent Personal Branding** — colors, fonts, and visuals that reflect your personality and style.
* **Responsive Design** — looks good and works on both mobile and desktop devices.
* **Accessible and Semantic HTML** — uses proper markup so it’s usable for everyone and discoverable by search engines.

While there are many similarities across portfolios, the choices you make around colors, fonts, and layouts can dramatically change the feel of your portfolio.

Check out these examples of personal portfolio websites which all have roughly the same structure ([click here to play around with them.](https://benspector-mls.github.io/))

![](./img/personal-portfolio-project/examples-grid.png)

Each example demonstrates different approaches to:

| Design Element    | What to Notice                                                                                  |
| ----------------- | ----------------------------------------------------------------------------------------------- |
| **Color Theme**   | Light vs. dark backgrounds, single accent color vs. multiple colors, gradients vs. solid colors |
| **Typography**    | Serif vs. sans-serif fonts, playful vs. professional feel, font pairing strategies              |
| **Navigation**    | Top bar vs. sidebar, ALL CAPS vs. lowercase, hover effects                                      |
| **Project Cards** | Grid vs. list layout, image placement, hover animations                                         |
| **Spacing**       | Compact vs. spacious layouts, section padding                                                   |
| **Personality**   | Minimalist, bold, elegant, retro, editorial, etc.                                               |

Make a copy of the [Investigation Guide](https://docs.google.com/document/d/1EtJqo6H6ORwC2maBSQPePHkUtkdWoPe9oevKdJCxsJo/copy) to analyze these examples and develop your own design taste before building your portfolio.

### Telling Your Story

The story you tell is just as important as the code that your write.

For your personal portfolio project, you will dedicate the "About Me" section to telling the world your story. You won't have a ton of space so you will need to craft a concise but compelling message that captures the unique experiences and perspectives you can bring to an engineering team.

Make a copy of this [About Me Content Worksheet](https://docs.google.com/document/d/1o4Y_uWSn387CfAMjcTDd4ybb-srKIlODhEXLP8MNsdY/copy) which will help you craft a compelling narrative to share on your personal portfolio.

### AI Usage (It's Required!)

Generative AI tools are now essential in the developer toolkit and this project is an excellent opportunity to start developing your AI skillset. 

AI tools (ChatGPT, Claude, GitHub Copilot, etc.) are **required** for this project—but you must be able to explain every line of code you submit.

**📝 AI Usage Log (Required)**

Create a Google doc to track your AI usage. For each use case below, you are **required** to document:
1. What you asked for (your prompt)
2. What you received (the AI's response)
3. What you changed and why
4. How your resulting code works (in your own words)

Share the Google Doc and link it in your README like so:

```md
This project provided me an opportunity to build something with AI assistance. Check out my [AI Usage Document](PASTE_LINK_HERE) to see how I used AI on this project.
```

---

**Use Case 1: Generate boilerplate code**

Use AI to generate starting templates for repetitive structures like your contact form or project cards.

**Your task:**
1. Ask: *"Create an accessible HTML contact form with fields for name, email, and message"* or *"Here is an project card component that I designed [paste HTML], make 3 more using the same structure"*
2. Review the generated code—does it use proper semantic elements and structure? For the form, are there `<label>` elements and correct `input` types?
3. Modify the code to match your design (classes, structure, styling)
4. Document in your AI Usage Log what you changed and why

---

**Use Case 2: Debug your CSS layouts**

When your flexbox or grid layout isn't working as expected, use AI to help you diagnose and fix the issue.

**Your task:**
1. When you encounter a layout bug, take a screenshot or copy your relevant HTML/CSS
2. Ask an AI tool: *"Why isn't my grid/flexbox layout working? Here's my code: [paste code]"*
3. Read the explanation to understand *why* your code wasn't working
4. Apply the fix and document in your AI Usage Log

**💡 Pro tip:** Chrome DevTools has a built-in AI assistant! Access it by clicking the blue chat icon in the top right corner of the developer tools panel. You can select specific elements on the page and ask questions directly.

![The Chrome Devtools built-in AI assistant](./img/personal-portfolio-project/chrome-devtools-ai-assistant.png)

---

**Use Case 3: Explore design alternatives**

Use AI as a brainstorming partner when you're stuck on design decisions.

**Your task:**
1. Ask questions like:
   - *"What are different ways to style a navigation bar?"*
   - *"How can I make my project cards more visually interesting?"*
   - *"What color combinations work well with [your primary color]?"*
2. Evaluate the suggestions—you don't have to use them all!
3. Document any suggestions you implemented

---

**Use Case 4: Learn how to implement extension features**

For this project, you will be required to create a sticky header and use CSS transitions, neither of which you've learned yet! This is intentional and we want you to use AI to teach yourself these concepts.

**Your task:**
1. Ask an AI tool: *"How do I make a sticky header using CSS?"*
2. Read and understand the explanation before copying any code
3. Implement it in your project, modifying the code to fit your design
4. Document the interaction in your AI Usage Log

Then repeat for CSS transitions:
1. Ask: *"What are CSS transitions and how do I use them for hover effects?"*
2. Experiment with different transition properties (`transition-duration`, `transition-property`, etc.)
3. Apply a transition to at least one interactive element (nav links, buttons, or project cards)
4. Document in your AI Usage Log

---

#### AI Usage Guidelines

**✅ Green Light** — Do these freely:
- Understanding concepts (*"Explain how flexbox justify-content works"*)
- Debugging help (*"Why isn't my grid layout working?"*)
- Learning syntax (*"Show me how to create a CSS variable"*)
- Generating boilerplate (*"Create a basic HTML form structure"*)
- Exploring alternatives (*"What are different ways to center a div?"*)

**⚠️ Yellow Light** — Allowed, but requires deep understanding:
- Generating complete components → you must modify and explain every part
- Getting layout solutions → you must explain why it works and what alternatives exist
- Receiving code suggestions → you must evaluate if it's the best approach

**🛑 Red Light** — Strictly not allowed:
- Copy-pasting AI code you don't understand
- Using AI to generate your entire project
- Being unable to explain code in your presentation
- Not documenting AI usage in your log

---

**The Test: If you can't explain it, you can't ship it.**

Before using any AI-generated code, ask yourself:
- Can I explain how this works to a peer?
- Can I modify this if requirements change?
- Do I understand the tradeoffs of this approach?
- Could I recreate something similar without AI?

If you answer "no" to any of these, spend time understanding before using the code.

### Science Fair Presentation and Reflection Questions

At Marcy, instead of formal presentations, we regularly use "Science Fair-style" presentations to showcase our work. In a Science Fair, you will be stationed around the room with your laptop open for your classmates to experience your website in person.

You will be assigned to a "presentation shift"—a roughly 15-minute long time slot where you will be required to present. Outside of your assigned shift you will be free to roam about and see the projects that your classmates have built!

In addition to showing off your project, you will be expected to reflect on the questions below and share them to your audience. **❗️Your responses to these questions should be added to your project's README.md file❗️:**

1. Share one technical concept that you developed greater mastery over in this project. Demonstrate how *you* understand that concept by sharing your mental model of the concept. Then, show how you used that concept in your project.

2. Choose one project requirement that you found challenging and are proud of implementing. Describe what made it challenging and how you were able to implement the requirement by walking through your code as succinctly as possible. Remember that your audience does not know your code nearly as well as you do so you’ll have to break it down in a logical manner for them to quickly understand it.

3. How did you leverage AI to assist your development of this project?

### Project Requirements and Assessment Criteria

Your final project must be submitted via Canvas and will be evaluated on the following criteria. Use this list as a guide for completing this project.

**Portfolio Content (5 points):**
- [ ] Has a navigation bar with link to access each page/section of the website
- [ ] Has a "hero" section that introduces the user to the portfolio page
- [ ] Has an about section that tells a brief story about your background and contains a list of technical skills
- [ ] Has a projects section with a grid of "project cards" to showcase your work
- [ ] Has a contact section with a form for users to get in touch with you. At minimum there should be fields for their name, email/phone number, and a message to send to you.

**HTML Best Practices (3 points):**
- [ ] Uses semantic HTML elements (`header`, `main`, `footer`, `section`, `nav`)
- [ ] Uses lists (`ul`, `li`) for repetitive elements (navigation links, skill items, projects, etc.)
- [ ] Contact form is structured properly (`label` elements for all inputs, appropriate `input` types, a button to submit)

**CSS Best Practices (2 points):**
- [ ] Uses CSS Variables to store colors and avoid repetition
- [ ] CSS comments are used to organize CSS

**CSS Layout (3 points):**
- [ ] Uses CSS Flexbox at least once to effectively create a one-dimensional layout
- [ ] Uses CSS Grid at least once to effectively create a two-dimensional layout
- [ ] Padding, margins, and gaps provide ample space for elements to "breathe"

**Responsive Design (3 points):**
- [ ] Mobile-first approach (base styles work on small screens)
- [ ] Uses media queries for tablet (768px+) and desktop (1024px+)
- [ ] Layout adapts appropriately across all breakpoints

**Core Functionality (2 points):**
- [ ] Hover states on navigation links, buttons, and project cards
- [ ] Contact form submits to Formspree (refer to the [Forms chapter](./6-forms.md#setting-up-formspree) for setup instructions).

**Extension Functionality (2 points)**
- [ ] Has a sticky header (a header that stays at the top as you scroll)
- [ ] At least one CSS transition or animation

**Deployment & Polish (2 points):**
- [ ] Portfolio page is accessible at `username.github.io`
- [ ] README.md file contains reflection questions and responses
- [ ] README.md file includes a link to your AI Usage Document.
- [ ] All images and links work

### Summary

**📋 Key Deliverables:**

| Deliverable                   | Description                                                                                                    |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Portfolio Website**         | A responsive personal portfolio deployed to `username.github.io`                                               |
| **AI Usage Document**         | A Google Doc logging your AI interactions (prompts, responses, changes, explanations). Link it in your README. |
| **Reflection Responses**      | Written responses to the 3 reflection questions, added to your README.md                                       |
| **Science Fair Presentation** | A 15-minute presentation slot where you'll demo your site and discuss your reflections                         |

**📚 Resources:**

- [Web Development Cheatsheet](./cheatsheet.md) — Quick reference for HTML, CSS, and DOM
- [Investigation Guide](https://docs.google.com/document/d/1EtJqo6H6ORwC2maBSQPePHkUtkdWoPe9oevKdJCxsJo/copy) — Analyze sample designs
- [About Me Content Worksheet](https://docs.google.com/document/d/1o4Y_uWSn387CfAMjcTDd4ybb-srKIlODhEXLP8MNsdY/copy) — Craft your story
- [Formspree Setup](./6-forms.md#setting-up-formspree) — Formspree setup instructions
- [Sample Portfolio Designs](https://benspector-mls.github.io/) — See different layout approaches

**🗺️ Next Steps:**

Jump to the [Project Roadmap](#3-follow-the-roadmap) below to get started. Follow the checkpoints in order to ensure you prioritize content and structure before diving into styling!

---

## Getting Started

### 1. Repository Setup

Go to GitHub and create a new repo. **The name of the repo must match this pattern exactly:**

```
username.github.io
```

For example, for the user `jon-smith123` their repo would be named 

```
jon-smith123.github.io
```

Make sure to include a README.md file.

Then clone your repo.

### 2. Set up your file structure

In your repo, create the following file structure:

```
.
├── README.md
├── img/    <-- for things like logos, images, etc.
├── index.html
└── style.css
```

**NOTE: It is essential that your `index.html` is at the root of the repo.**

### 3. Follow the Roadmap

It will be essential that you prioritize your work appropriately. It can be incredibly easy to fall into the trap of jumping into design before your content is fully completed. Avoid that urge and instead follow the roadmap below to ensure success!

--- 

#### Checkpoint 1: Planning and Structure (Friday)
**Before you write any code:**
- [ ] Set up your GitHub repository
- [ ] Review the [Sample Designs Investigation](#sample-designs-investigation) and decide on your color palette, fonts, and layout
- [ ] Complete the [About Me Content Worksheet](#telling-your-story) to determine the content on your page.

**Then build:**
- [ ] HTML structure
- [ ] Ensure that you use semantic HTML throughout (`header`, `main`, `footer`, `nav`, `section`, `aside`, etc.)
- [ ] Ensure that you use lists for repeating content (navigation links, skills, projects, etc.)
- [ ] Add your About Me content and placeholder text for projects

**Deliverable:** HTML-only site with all content and structure

---

#### Checkpoint 2: Mobile Styling (Saturday - Sunday)
- [ ] Set up CSS variables (colors, fonts, spacing)
- [ ] Style navigation bar
- [ ] Style each page for mobile view (375px width)
- [ ] Implement the contact form
- [ ] Add hover states to interactive elements

**Deliverable:** Fully styled mobile experience

---

#### Checkpoint 3: Responsive Design (Monday)
- [ ] Add media queries for tablet (768px+) and desktop (1024px+)
- [ ] Use flexbox for navigation and project cards
- [ ] Use grid for project gallery layout
- [ ] Test responsiveness across breakpoints

**Deliverable:** Fully responsive site

---

#### Checkpoint 4: Polish and Deploy (Tuesday)
- [ ] Add stretch features (sticky nav, animations) if time allows
- [ ] Test form submission to Formspree (refer to the [Forms chapter](./6-forms.md#setting-up-formspree) for setup instructions)
- [ ] Validate HTML and CSS
- [ ] Deploy to GitHub Pages
- [ ] Test deployed site

**Deliverable:** Live portfolio at `username.github.io`

---
