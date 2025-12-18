# CSS

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Intro to CSS](#intro-to-css)
  - [CSS Syntax](#css-syntax)
- [Linking/Loading CSS Files](#linkingloading-css-files)
  - [Internal Stylesheets With Style Tags (Fine If You Only Want 1 File)](#internal-stylesheets-with-style-tags-fine-if-you-only-want-1-file)
  - [Attributes / Inline Styling (Practically Never Do This)](#attributes--inline-styling-practically-never-do-this)
  - [External Stylesheets (Best Practice)](#external-stylesheets-best-practice)
- [Selectors](#selectors)
  - [Element Selector](#element-selector)
  - [Class Selector](#class-selector)
  - [ID Selector](#id-selector)
  - [Multi-Selectors](#multi-selectors)
  - [Pseudo-Class Selectors](#pseudo-class-selectors)
  - [The Cascade: Why It's Called "Cascading"](#the-cascade-why-its-called-cascading)
- [Common CSS Properties](#common-css-properties)
  - [Color Properties](#color-properties)
  - [Typography Properties](#typography-properties)
  - [Spacing Properties (Just the Basics)](#spacing-properties-just-the-basics)
  - [Units: `px` vs `%` vs `rems`](#units-px-vs--vs-rems)
- [Development Workflow](#development-workflow)
- [Quiz!](#quiz)
- [Complete CSS](#complete-css)

## Key Concepts

- **CSS (Cascading Style Sheets)** defines the appearance of HTML documents: colors, typography, spacing, and layout.
- CSS syntax consists of **selectors**, **properties**, and **values**. Each `property: value;` pair is called a **declaration**.
- CSS can be added via internal `<style>` tags, inline `style` attributes, or external `.css` files. **External stylesheets are the best practice** as they promote reusability and separation of concerns.
- **Selectors** target elements to style:
  - Element selector: `p { }` — targets all `<p>` elements
  - Class selector: `.intro { }` — targets elements with `class="intro"`
  - ID selector: `#main { }` — targets the element with `id="main"`
  - Multi-selector: `h1, h2, h3 { }` — targets multiple element types
  - Pseudo-class selector: `a:hover { }` — targets elements in a specific state
- **Specificity** determines which styles win when multiple rules apply: ID > Class > Element. If specificity is equal, the last rule wins.
- **Units**: Use `rem` for font sizes (accessible, scales with user settings), `%` for relative sizing, and avoid `px` for font sizes.

## Intro to CSS

In the previous lesson, we learned how to structure content with HTML and even got a preview of styling with `<style>` tags. Now we'll dive deeper into CSS and learn how to truly transform the appearance of our pages.

When the world wide web was born, it was just a means of communicating information. The world of design hadn't yet made its way to the internet and websites looked like this:

![An HTML page with no styling.](../.gitbook/assets/html-page-no-css.png)

As you can see, the browser already has some default styles applied to elements of different types. Headings are bigger and bolder than paragraphs. Hyperlinks ("anchors") are blue and underlined.

But with CSS, we can override those defaults and dramatically transform the page. 

![The same page with CSS.](../.gitbook/assets/css-zen-garden.png)

{% hint style="info" %}
You can see this page at [https://csszengarden.com/](https://csszengarden.com/), a website dedicated to showing the power of CSS. It showcases a variety of designs of the exact same HTML. The only thing that changes is the CSS!
{% endhint %}

Which website would you rather use? Which one feels more trustworthy and professional? **CSS bridges the gap between content and experience.** As a developer, understanding CSS allows you to:

| Skill | Why It Matters |
| ----- | -------------- |
| Control visual hierarchy | Guide users' attention to what's important |
| Create responsive layouts | Make your app usable on phones, tablets, and desktops |
| Implement design systems | Build consistent, professional interfaces |
| Debug styling issues | Fix layout problems that break user experience |

Later, when you learn DOM manipulation with JavaScript, you'll be able to dynamically change CSS in response to user actions—highlighting selected items, showing/hiding elements, animating transitions, and more. A solid CSS foundation makes that JavaScript work much more powerful.

### CSS Syntax

CSS is written using **selectors**, **properties**, and **values**:

```css
body {
  background: azure;
  color: midnightblue;
  font-family: Helvetica;
  text-align: center;
  padding: 20px;
}

ul, ol {
  margin: auto;
  width: fit-content;
}
```

* **Selectors** (like `body` or `ul, ol`) specify which HTML element(s) to style. Selectors are followed by curly braces `{}` which contain the properties for the selected element(s).
* **Properties** (like `background` and `color`) tell our browser the aspect of the element to change.
* Each property has a set of valid **values** that can be assigned to them (like `azure` or `midnightblue` for colors, `center` for alignment, or `20px` for sizing and spacing.)

Each `property:value;` pair is called a **declaration** that *must* be followed by a semicolon.

## Linking/Loading CSS Files

There are three ways to add CSS to an HTML document:
1. Inside `<style></style>` tags (this is okay but not preferred)
2. As attributes of individual elements (don't do this)
3. Inside a separate `.css` file, loaded via a `<link>` tag.

### Internal Stylesheets With Style Tags (Fine If You Only Want 1 File)

Open up your `index.html` file from the `my-profile` folder and add these `<style>` tags and CSS within the `<head>`:

```html
<!DOCTYPE html>
<html>
  <head>
    <!-- Other meta tags -->
    <style>
      body {
        background: azure;
        color: midnightblue;
        font-family: Helvetica;
        text-align: center;
        padding: 20px;
      }

      ul, ol {
        margin: auto;
        width: fit-content;
      }
    </style>
  </head>
  <body>
    <!-- Your content -->
  </body>
</html>
```

This approach works well if you are interested in keeping all of your code inside of one file or need to quickly test some CSS.

### Attributes / Inline Styling (Practically Never Do This)

You can also add a `style` attribute directly to an individual element:

```html
<body style="background: azure; color: midnightblue; font-family: Helvetica; text-align: center; padding: 20px;">
  <!-- body content -->

  <ul style="margin: auto; width: fit-content;">
    <!-- unordered list items -->
  </ul>

  <ol style="margin: auto; width: fit-content;">
    <!-- ordered list items -->
  </ol>
</body>
```

**<details><summary>Q: What issues can you foresee with inline styling?</summary>**

**Problem:** This mixes HTML structure with visual styling, making your code harder to maintain. If you want to change the styling of all `ul` and `ol` lists, you'd have to update every single `ul` and `ol` tag.

</details>


### External Stylesheets (Best Practice)

CSS is most commonly written in `.css` files that are loaded by an HTML file via the `<link>` element.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./index.css"> 
    <!-- ^ This link tag is how we apply CSS to an HTML page -->
    <!-- It should go in the head -->
  </head>
  <body>
    <!-- body content -->
  </body>
</html>
```

* The `rel` (relationship) attribute tells the browser what kind of file is being linked (other file types can also be loaded using this tag)
* The `href` (hyperlink reference) attribute is an absolute/relative path to the `.css` file

**TODO**: Move all of the CSS into a separate file called `index.css` and load it into your HTML file.

**<details><summary>Q: What are the potential benefits of having CSS in an external file?</summary>**

Just like modules in a JavaScript program, external stylesheets can be reused by multiple pages that want to have the same styling. When you decide to make a change to the overall style of the website, all pages that use that stylesheet will be updated without the need to update pages individually.

Overall, this makes development and maintenance much easier and more organized.

</details>

## Selectors

There are TONS of ways to select elements with CSS rules ([list of all the selectors](https://www.w3schools.com/cssref/css_selectors.php)). 

The most common selectors are:
* By element (select all `li` elements)
* By class name (select all elements with `class="blue"`)
* By id name (select the element with `id="email-form"`)
* Or some combination of these things

**TODO:** For each of the examples below, try adding them to your `index.html` profile page!

### Element Selector

Targets all elements of a specific type:

```css
li { 
  font-family: roboto;
  margin-bottom: 5px;
}
```

This styles *every* `<li>` element on the page.

### Class Selector

Targets elements with a specific class attribute. Starts with a dot (`.`)

**HTML:**
```html
<h2>Programming Skills</h2>
<ul>
  <li class="devtool">Git & GitHub</li>
  <li class="devtool">Command Line Interface</li>
  <li class="js-skill">JavaScript Fundamentals</li>
  <li class="js-skill">OOP and Classes</li>
  <li class="webdev-skill" id="current-topic">HTML</li>
</ul>
```

**CSS:**
```css
/* All elements with the class "devtool" will be orange */
.devtool {
  background: orange;
}

/* All elements with the class "js-skill" will be yellow */
.js-skill {
  background: yellow;
}

/* All elements with the class "webdev-skill" will be lightgreen */
.webdev-skill {
  background: lightgreen;
}
```

Remember, multiple elements can share the same class.

**TODO: Add list items to your programming skills list for CSS. Then, add `class="webdev-skill"`**

### ID Selector

Targets a single element with a specific id attribute. IDs start with a hash (`#`):

**HTML:**

```html
<img id="profile-picture" src="img-url.png" alt="Profile Picture" >
```

**CSS:**

```css
#profile-picture {
  border-radius: 50%;
}
```

Remember, each id should be unique on the page. If you want to style multiple elements the same way, use a class instead.

### Multi-Selectors

You can apply the same styles to multiple selectors by separating them with a comma (`,`). We did this earlier with the `ul` and `ol` elements:

```css
ul, ol {
  margin: auto;
  width: fit-content;
}
```

This applies the styles to all `<ul>` and `<ol>` elements.

### Pseudo-Class Selectors

**Pseudo-class selectors** let us select an element in a particular **state** such as when it is being hovered over or has been clicked:

```css
/* a link when the mouse hovers over it */
a:hover { color: red } 
/* a link the moment it is clicked */
a:active { color: green; } 
```

### The Cascade: Why It's Called "Cascading"

When multiple CSS rules apply to the same element, the browser follows these rules to determine which styles win:

1. **Specificity**: More specific selectors override less specific ones
   - ID selectors (`#main-title`) are most specific
   - Class selectors (`.intro`) are less specific
   - Element selectors (`p`) are least specific

2. **Source order**: If specificity is equal, the rule that appears last wins

**Example:**
```css
p {
  color: yellow;
}

p {
  /* overrides the rule above */
  color: blue;
}

.intro {
  color: green;
}

#special {
  color: red;
}
```

```html
<p>I'm blue</p>
<p class="intro">I'm green (class beats element)</p>
<p id="special" class="intro">I'm red (ID beats class)</p>
```

## Common CSS Properties

### Color Properties

```css
p {
  color: blue;                    /* text color */
  background-color: lightgray;    /* background color */
}
```

**Color values can be:**
- **Named colors**: `red`, `blue`, `green`, `hotpink`
- **Hex codes**: `#FF5733`, `#0000FF`
- **RGB**: `rgb(255, 87, 51)`
- **RGBA** (with transparency): `rgba(255, 87, 51, 0.5)`

### Typography Properties

```css
p {
  font-size: 16px;                /* size of text */
  font-family: Arial, sans-serif; /* typeface */
  font-weight: bold;              /* normal, bold, or 100-900 */
  text-align: center;             /* left, center, right, justify */
  line-height: 1.5;               /* spacing between lines */
}
```

**About `font-family`**: List multiple fonts separated by commas. The browser uses the first available font. Always end with a generic family like `sans-serif` or `serif` as a fallback.

### Spacing Properties (Just the Basics)

```css
p {
  margin: 20px;     /* space outside the element */
  padding: 10px;    /* space inside the element */
}
```

We'll explore spacing in detail when we cover the box model, but for now:
- **Margin** creates space between elements
- **Padding** creates space inside an element, between its border and content

### Units: `px` vs `%` vs `rems`

* The `px` unit sets the exact size of a property. **Setting the size of an element using `px` values should be avoided if possible** as they don't comply with accessibility standards.

```css
p {
  padding: 30px;
}

div {
  width: 100px;
}
```

* The `rem` unit sets a size of an element relative to the browser's **base font size** which is typically `16px` by default. Users can change their base font size as an accessibility feature. If your website uses `rem` units instead of `px`, your font will scale according to your user's accessibility settings.

```css
p {
  /* if the base font size is 16px, 2rem === 32px */
  font-size: 2rem; 
}
```

* The percentage `%` unit will set the size of an element to a percentage of its parent element's width. So, if you have a parent element with a width of `100px`, and the child has its width set to `75%`, the child will have a width of `75px`. Use this carefully!

```css
.parent {
  width: 100px;
}

.child {
  width: 75%;
}
```

## Development Workflow

When building a webpage:

1. **Start with HTML structure** - Get your content organized with proper semantic elements
2. **Link your CSS file** - Add the `<link>` tag in your `<head>`
3. **Add classes/ids to HTML** - Label elements you want to style
4. **Write CSS rules** - Target elements and apply styles
5. **Refresh browser** - See your changes (you may need to hard refresh with Cmd+Shift+R or Ctrl+Shift+R)

**Debugging tip**: Use your browser's Developer Tools (right-click > Inspect) to see which CSS rules are being applied to an element. This is essential for understanding why styles aren't working as expected.

## Quiz!

**Q: The tags `h1`, `h2`, and `p` all have different default styles. How are they different?**

<details>

<summary>Answer</summary>

Among other things, `h1` and `h2` elements are both bold compared to `p`.

`h1` elements have larger font than `h2` elements which have larger font than `p` elements.

</details>

**Q: What is wrong with the CSS syntax below?**

```css
{
  color: red;
  text-align: right;
}

h2 [ color: white; font-family: Arial; ]

p {
  font-weight: bold
  margin: 10px
}
```

<details>

<summary>Answer</summary>

1. In the first CSS rule, there is no selector.
2. In the second rule, `[]` are used instead of `{}`
3. In the third rule, the declarations need to end with semicolons.

</details>

**Q: For the HTML below, how would you make all `p` tags blue? How would you make only the `p` tags with `class="fun"` green? How would you make the second `p` tag `orange`?**

```html
<p class='fun'>first</p>
<p class='fun' id='super-fun'>second</p>
<p>third</p>
```

<details>

<summary>Answer</summary>

```css
p {
  color: blue;
}

.fun {
  color: green;
}

#super-fun {
  color: orange;
}
```

</details>

**Q: For the HTML and CSS below, what will be the color of paragraph? (pay close attention)**

HTML:
```html
<p class='fun'>Hello World</p> 
```

CSS:
```css
.fun {
  color: orange;
}
#its-purple {
  color: purple
}
p {
  color: red;
}
```

<details>

<summary>Answer</summary>

Did you fall for my trap?

It will be orange! While the selector `#its-purple` is the most specific selector, the element doesn't have that id! So the most specific selector is the `.fun` class selector.

</details>

**Q: How do you link an external CSS file called `styles.css` to an HTML document? Where in the HTML should this go?**

<details>

<summary>Answer</summary>

Use the `<link>` tag with `rel="stylesheet"` and `href` pointing to the CSS file. It should go in the `<head>`:

```html
<head>
  <link rel="stylesheet" href="styles.css">
</head>
```

</details>

**Q: How would you apply the same styles to all `h1`, `h2`, AND `h3` elements using a single CSS rule?**

<details>

<summary>Answer</summary>

Use a multi-selector by separating the element selectors with commas:

```css
h1, h2, h3 {
  font-family: Georgia, serif;
  color: navy;
}
```

</details>

**Q: What are three different ways you could set an element's text color to red in CSS?**

<details>

<summary>Answer</summary>

1. **Named color**: `color: red;`
2. **Hex code**: `color: #FF0000;`
3. **RGB**: `color: rgb(255, 0, 0);`

Bonus: You can also use **RGBA** for transparency: `color: rgba(255, 0, 0, 0.5);`

</details>

## Complete CSS

```css
body {
  background: azure;
  color: midnightblue;
  font-family: Helvetica;
  text-align: center;
  padding: 2rem;
}
ul, ol {
  margin: auto;
  width: fit-content;
}
li {
  font-family: roboto;
  margin-bottom: 0.5rem;
}
/* All elements with the class "devtool" will be orange */
.devtool {
  background: orange;
}

/* All elements with the class "js-skill" will be yellow */
.js-skill {
  background: yellow;
}

.webdev-skill {
  background: lightgreen;
}

#current-topic {
  font-weight: bold;
}

#profile-picture {
  border-radius: 50%;
}

/* a link when the mouse hovers over it */
a:hover { color: red } 
/* a link the moment it is clicked */
a:active { color: green; } 
```