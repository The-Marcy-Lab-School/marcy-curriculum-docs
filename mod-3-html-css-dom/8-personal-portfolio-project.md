# Portfolio Project

**Table of Contents:**
- [Project Overview](#project-overview)
  - [Sample Designs Investigation](#sample-designs-investigation)
  - [Telling Your Story](#telling-your-story)
  - [Science Fair Presentation \& Reflection Questions](#science-fair-presentation--reflection-questions)
  - [Project Requirements \& Assessment Criteria](#project-requirements--assessment-criteria)
  - [Additional Details / FAQs](#additional-details--faqs)
    - [Wait, what if I don't have any projects!?](#wait-what-if-i-dont-have-any-projects)
    - [We never learned sticky header, transitions, or animations! How do I do those?](#we-never-learned-sticky-header-transitions-or-animations-how-do-i-do-those)
    - [Can I use AI??](#can-i-use-ai)
- [Getting Started](#getting-started)
  - [1. Repository Setup](#1-repository-setup)
  - [2. Set up your file structure](#2-set-up-your-file-structure)
  - [3. Follow the Roadmap](#3-follow-the-roadmap)
    - [Checkpoint 1: Planning \& Structure (Friday)](#checkpoint-1-planning--structure-friday)
    - [Checkpoint 2: Mobile Styling (Saturday - Sunday)](#checkpoint-2-mobile-styling-saturday---sunday)
    - [Checkpoint 3: Responsive Design (Monday)](#checkpoint-3-responsive-design-monday)
    - [Checkpoint 4: Polish \& Deploy (Tuesday)](#checkpoint-4-polish--deploy-tuesday)
- [CSS Cheatsheet](#css-cheatsheet)
  - [Colors](#colors)
  - [Typography (Text Styling)](#typography-text-styling)
  - [CSS Variables](#css-variables)
  - [Box Model (Spacing \& Sizing)](#box-model-spacing--sizing)
  - [Layout](#layout)
  - [Interactive States (Pseudo-classes)](#interactive-states-pseudo-classes)
  - [Flexbox](#flexbox)
  - [Grid](#grid)
  - [Media Queries](#media-queries)
- [Formspree Setup](#formspree-setup)

## Project Overview

For this solo project, you will build a personal portfolio website using HTML and CSS.

A personal portfolio website is often the first impression employers, collaborators, and clients have of you as a developer. It is a platform to showcase your skills, projects, creativity, and personality.

By crafting your own site from scratch, you gain essential experience with real-world web development and create a powerful tool to advance your career.

You will then have an opportunity to present your project to your peers, sharing the challenges you faced along the way and your key learnings from the project.

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

### Science Fair Presentation & Reflection Questions

At Marcy, instead of formal presentations, we regularly use "Science Fair-style" presentations to showcase our work. In a Science Fair, you will be stationed around the room with your laptop open for your classmates to experience your website in person.

You will be assigned to a "presentation shift"—a roughly 15-minute long time slot where you will be required to present. Outside of your assigned shift you will be free to roam about and see the projects that your classmates have built!

In addition to showing off your project, you will be expected to reflect on the questions below and share them to your audience. **❗️Your responses to these questions should be added to your project's README.md file❗️:**

1. Share one technical concept that you developed greater mastery over in this project. Demonstrate how *you* understand that concept by sharing your mental model of the concept. Then, show how you used that concept in your project.

2. Choose one project requirement that you found challenging and are proud of implementing. Describe what made it challenging and how you were able to implement the requirement by walking through your code as succinctly as possible. Remember that your audience does not know your code nearly as well as you do so you’ll have to break it down in a logical manner for them to quickly understand it.

### Project Requirements & Assessment Criteria

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
- [ ] Contact form submits to Formspree

**Extension Functionality (2 points)**
- [ ] Has a sticky header (a header that stays at the top as you scroll)
- [ ] At least one CSS transition or animation

**Deployment & Polish (2 points):**
- [ ] Portfolio page is accessible at `username.github.io`
- [ ] README.md file contains reflection questions and responses
- [ ] All images and links work

### Additional Details / FAQs

#### Wait, what if I don't have any projects!?

At this point of the year, you may not have any personal portfolio projects to share. That is expected! 

If you look at the [sample designs](https://benspector-mls.github.io/), you'll notice that they just use placeholders for their projects and "dead" links that don't navigate anywhere. You can do the same for your projects section until you have projects to share.

The point is to create a website that you can return to and update as your portfolio expands. For now, just use placeholders but build out the HTML and CSS so that all you have to is insert a picture, update the description, and add in your links.

#### We never learned sticky header, transitions, or animations! How do I do those?

A sticky header is one in which the header of a website (usually containing navigation links) remains at the top of the browser window even as the user scrolls down.

CSS transitions can be used to create smooth, gradual changes to a CSS property from one value to another over a specified period of time rather than having the change occur instantly. Transitions can create really nice user interactions when hovering over links, buttons, or opening a dropdown menu.

These pieces of extension functionality will require you to do a bit of learning on your own! These are common patterns with tons of examples online to pull from. For some guidance, try using Google or an AI tool like Claude or ChatGPT to find answers to these prompts:

- "How do I make a sticky header?"
- "What is a CSS transition?"
- "How do I use transitions to make cool hyperlink hover effects?"

#### Can I use AI??

**AI tools (ChatGPT, Claude, GitHub Copilot, etc.) are allowed and encouraged** - but you must be able to explain every line of code you submit.

**✅ Green Light** (Allowed and encouraged!): 
- Using AI to understand concepts ("Explain how flexbox justify-content works")
- Getting debugging help ("Why isn't my grid layout working?")
- Learning syntax ("Show me how to create a CSS variable")
- Generating boilerplate ("Create a basic HTML form structure")
- Exploring alternatives ("What are different ways to center a div?")

**⚠️ Yellow Light** (Allowed, but requires modification and deep understanding):
- Generating complete components (you must modify and explain every part)
- Getting layout solutions (you must explain why it works and what alternatives exist)
- Receiving code suggestions (you must evaluate if it's the best approach)

**⚠️ Required if you use AI this way:**
1. Document what you asked for (keep a separate Google Doc to track this)
2. Document what you received
3. Explain what you changed and why
4. Explain how it works in your own words

**🛑 Red Light** (Strictly not allowed): 
- Copy-pasting AI code you don't understand
- Using AI to generate your entire project
- Being unable to explain code in your presentation
- Not documenting AI usage in decision log

**The Test: If you can't explain it, you can't ship it.**

Before using any AI-generated code, ask yourself:
- Can I explain how this works to a peer?
- Can I modify this if requirements change?
- Do I understand the tradeoffs of this approach?
- Could I recreate something similar without AI?

If you answer "no" to any of these, spend time understanding before using the code.

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

#### Checkpoint 1: Planning & Structure (Friday)
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

#### Checkpoint 4: Polish & Deploy (Tuesday)
- [ ] Add stretch features (sticky nav, animations) if time allows
- [ ] Test form submission to Formspree
- [ ] Validate HTML and CSS
- [ ] Deploy to GitHub Pages
- [ ] Test deployed site

**Deliverable:** Live portfolio at `username.github.io`

---

## CSS Cheatsheet

Use this as a reference while working. **You don't need to memorize these!**

### Colors

| Property                           | What It Does          | Example                |
| ---------------------------------- | --------------------- | ---------------------- |
| `color`                            | Sets text color       | `color: midnightblue;` |
| `background` or `background-color` | Sets background color | `background: azure;`   |

Color values can be: named colors (`red`, `blue`), hex codes (`#FF5733`), or RGB (`rgb(255, 87, 51)`).

### Typography (Text Styling)

| Property          | What It Does                | Example                           |
| ----------------- | --------------------------- | --------------------------------- |
| `font-family`     | Sets the font               | `font-family: Arial, sans-serif;` |
| `font-size`       | Sets text size (use `rem`!) | `font-size: 1.5rem;`              |
| `font-weight`     | Sets boldness               | `font-weight: bold;`              |
| `font-style`      | Sets italic                 | `font-style: italic;`             |
| `text-align`      | Aligns text                 | `text-align: center;`             |
| `text-decoration` | Adds/removes underlines     | `text-decoration: none;`          |

### CSS Variables

**CSS Variables** are reusable values you define once and use throughout your CSS. This is helpful for consistent colors, fonts, and easy updates.

**Example:**
```css
:root {
  --color-background: #181818;
  --color-accent: #1db954;
  --color-text: #fff;
  --font-family: Arial, sans-serif;
}
```

**Usage:**
```css
body {
  background: var(--color-background);
  color: var(--color-text);
  font-family: var(--font-family);
}

a:hover {
  color: var(--color-accent);
}
```

Define your variables at the top of your `style.css` inside the `:root` selector, then use them everywhere you reference colors, fonts, or repeated values.

### Box Model (Spacing & Sizing)

| Property                                                     | What It Does                 | Example                           |
| ------------------------------------------------------------ | ---------------------------- | --------------------------------- |
| `width`                                                      | Sets element width           | `width: 300px;`                   |
| `max-width`                                                  | Sets maximum width           | `max-width: 600px;`               |
| `padding`                                                    | Space INSIDE the border      | `padding: 20px;`                  |
| `margin`                                                     | Space OUTSIDE the border     | `margin: 10px;`                   |
| `border`                                                     | Creates a border (all sides) | `border: 2px solid black;`        |
| `border-top`, `border-bottom`, `border-left`, `border-right` | Border on one side           | `border-bottom: 2px solid black;` |
| `border-radius`                                              | Rounds corners               | `border-radius: 8px;`             |

**Shorthand for padding/margin:**
- `padding: 10px;` → all sides
- `padding: 10px 20px;` → top/bottom, left/right

### Layout

| Property       | What It Does                             | Example             |
| -------------- | ---------------------------------------- | ------------------- |
| `margin: auto` | Centers a block element (needs a width!) | `margin: auto;`     |
| `list-style`   | Styles/removes list bullets              | `list-style: none;` |

### Interactive States (Pseudo-classes)

```css
/* When mouse hovers over element */
a:hover {
  background: lightblue;
}
```

### Flexbox

| Property          | What It Does                              | Example                          |
| ----------------- | ----------------------------------------- | -------------------------------- |
| `display: flex`   | Makes the element a flex container        | `display: flex;`                 |
| `flex-direction`  | Sets direction: `row` (default), `column` | `flex-direction: column;`        |
| `justify-content` | Spacing along the **main axis**           | `justify-content: space-between` |
| `align-items`     | Alignment along the **cross axis**        | `align-items: center;`           |
| `gap`             | Space **between** flex items              | `gap: 20px;`                     |

**Common `justify-content` values:** `flex-start`, `flex-end`, `center`, `space-between`, `space-around`, `space-evenly`

**Common `align-items` values:** `flex-start`, `flex-end`, `center`, `stretch` (default)

### Grid

| Property                | What It Does             | Example                           |
| ----------------------- | ------------------------ | --------------------------------- |
| `display: grid`         | Makes the element a grid | `display: grid;`                  |
| `grid-template-columns` | Defines column sizes     | `grid-template-columns: 1fr 1fr;` |
| `gap`                   | Space between grid items | `gap: 20px;`                      |

**The `fr` unit** divides available space into fractions. `1fr 1fr 1fr` creates 3 equal columns.

### Media Queries

```css
/* Styles for screens 768px and wider */
@media (min-width: 768px) {
  .selector {
    /* styles here */
  }
}
```

## Formspree Setup

For guidance on connecting your form to Formspree, refer to the [Marcy GitBook chapter on Forms](https://marcylabschool.gitbook.io/marcy-lab-school-docs/mod-3-html-css-dom/6-forms#setting-up-formspree).
