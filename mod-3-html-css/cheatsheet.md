# Web Development Cheat Sheet

Use this as a reference while working.

**Table of Contents:**
- [HTML](#html)
  - [Document Structure (Boilerplate)](#document-structure-boilerplate)
  - [Text Elements](#text-elements)
  - [Lists](#lists)
  - [Links and Images](#links-and-images)
  - [Semantic Structure Elements](#semantic-structure-elements)
  - [Forms](#forms)
  - [Accessibility Quick Tips](#accessibility-quick-tips)
- [CSS](#css)
  - [Colors](#colors)
  - [Typography (Text Styling)](#typography-text-styling)
  - [CSS Variables](#css-variables)
  - [Box Model (Spacing and Sizing)](#box-model-spacing-and-sizing)
  - [Layout](#layout)
  - [Interactive States (Pseudo-classes)](#interactive-states-pseudo-classes)
  - [Flexbox](#flexbox)
  - [Grid](#grid)
  - [Media Queries](#media-queries)

## HTML

### Document Structure (Boilerplate)

Every HTML document should start with this structure:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <!-- Visible content goes here -->
  </body>
</html>
```

**Pro Tip:** In VS Code, type `html:5` and press Enter to insert this boilerplate automatically.

### Text Elements

| Element          | Purpose                                    | Example                  |
| ---------------- | ------------------------------------------ | ------------------------ |
| `<h1>` to `<h6>` | Headings (use in order, one `h1` per page) | `<h1>Main Title</h1>`    |
| `<p>`            | Paragraph of text                          | `<p>Some text here.</p>` |
| `<strong>`       | Bold/important text                        | `<strong>bold</strong>`  |
| `<em>`           | Italic/emphasized text                     | `<em>italic</em>`        |

### Lists

```html
<!-- Unordered list (bullets) -->
<ul>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>

<!-- Ordered list (numbers) -->
<ol>
  <li>First</li>
  <li>Second</li>
</ol>
```

**Important:** `<li>` elements must always be inside a `<ul>` or `<ol>`.

### Links and Images

```html
<!-- Hyperlink -->
<a href="https://example.com">Click here</a>
<a href="./about.html">About Page</a>

<!-- Image (self-closing tag) -->
<img src="photo.jpg" alt="Description of the image" />
```

| Attribute | Element | Purpose                                                     |
| --------- | ------- | ----------------------------------------------------------- |
| `href`    | `<a>`   | URL or file path the link goes to                           |
| `src`     | `<img>` | URL or file path of the image                               |
| `alt`     | `<img>` | Description for screen readers and when image fails to load |

### Semantic Structure Elements

Use these elements to organize your page structure:

```html
<body>
  <header>
    <nav>
      <!-- Navigation links -->
    </nav>
  </header>
  <main>
    <section>
      <!-- A distinct section of content -->
    </section>
  </main>
  <footer>
    <!-- Copyright, contact info -->
  </footer>
</body>
```

| Element        | Purpose                            |
| -------------- | ---------------------------------- |
| `<header>`     | Top of page (logo, navigation)     |
| `<nav>`        | Contains navigation links          |
| `<main>`       | Primary content of the page        |
| `<section>`    | A thematic grouping of content     |
| `<footer>`     | Bottom of page (copyright, links)  |
| `<figure>`     | Container for images with captions |
| `<figcaption>` | Caption for a `<figure>`           |

### Forms

```html
<form action="https://formspree.io/f/YOUR_ID" method="POST">
  <div>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required />
  </div>
  <div>
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required />
  </div>
  <div>
    <label for="message">Message</label>
    <textarea id="message" name="message" rows="5"></textarea>
  </div>
  <button type="submit">Send</button>
</form>
```

**Key form rules:**
- Every `<input>` needs a `<label>` with a matching `for`/`id` pair
- Every `<input>` needs a `name` attribute for the data to be submitted
- Use appropriate `type` attributes: `text`, `email`, `number`, `date`, `url`, `checkbox`, `radio`

| Input Type   | Use Case                           |
| ------------ | ---------------------------------- |
| `text`       | Short, open-ended text             |
| `email`      | Email addresses (validates format) |
| `number`     | Numeric data                       |
| `date`       | Dates (shows calendar picker)      |
| `url`        | URLs (validates format)            |
| `checkbox`   | Yes/no options                     |
| `radio`      | Pick ONE from a few options        |
| `<textarea>` | Long, multi-line text              |
| `<select>`   | Dropdown menu (many options)       |

### Accessibility Quick Tips

1. **Use headings in order** — Don't skip from `h1` to `h4`
2. **Always add `alt` to images** — Describe what the image shows
3. **Use descriptive link text** — "Read more about pricing" not "Click here"
4. **Connect labels to inputs** — Use `for` and `id` attributes
5. **Use semantic elements** — `<nav>`, `<main>`, `<section>` instead of `<div>` everywhere

---

## CSS

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

### Box Model (Spacing and Sizing)

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
