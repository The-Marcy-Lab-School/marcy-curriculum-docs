# The Box Model

**Table of Contents**

- [Key Concepts](#key-concepts)
- [The Box Model](#the-box-model-1)
  - [Web Design 101: Every Element is a Box](#web-design-101-every-element-is-a-box)
- [Box Model](#box-model)
  - [Content, Width, and Height](#content-width-and-height)
  - [Padding: Space Inside the Box](#padding-space-inside-the-box)
  - [Border: The Edge of the Box](#border-the-edge-of-the-box)
  - [Margin: Space Between Elements](#margin-space-between-elements)
  - [Margin Collapse](#margin-collapse)
    - [Centering with Margin](#centering-with-margin)
- [Display](#display)
  - [Display Challenge](#display-challenge)
- [The Box-Sizing Problem](#the-box-sizing-problem)
  - [The Solution: border-box reset](#the-solution-border-box-reset)
  - [General CSS Reset](#general-css-reset)
- [Test your Skills!](#test-your-skills)
- [Complete HTML \& CSS](#complete-html--css)

## Key Concepts

* **Box Model** — How elements in an HTML document are modeled in the browser and how their dimensions are calculated based on the provided CSS properties. It consists of content, padding, border, and margin.
* The **content** — the space that the content of an element occupies (the text of a `<p>` tag, the image of an `img` tag, etc...)
  * Content can be modified with the `width` and `height` properties.
* The **padding** — the space around the content but inside the border. This is the "background" of the content.
  * Padding can be modified with the `padding` property.
* The **border** — the space around the padding.
  * Border can be modified with the `border` and `border-radius` properties.
* The **margin** — the space around the border and "between" adjacent elements.
  * Margin can be modified with the `margin` property.
  * **Margin collapse** occurs when two vertical margins meet and the larger margin "wins".
* The `box-sizing` property determines how the total width and height of an element is calculated.
  * The default `box-sizing: content-box` setting has the `width` and `height` properties affect the content box, with padding and border dimensions added.
  * The `box-sizing:border-box` setting makes the `width` and `height` properties affect the border box, with the content box automatically shrinking to fit inside the padding box.
* A **CSS Reset** is a set of rules intended to remove default CSS styles applied by the _user agent stylesheet_.

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

* The `display` property changes how elements are arranged relative to each other:
  * `display: block` elements will stack on top of each other, regardless of their width.
  * `display: inline` elements will sit next to each other (if there is space available) and are not affected by the `width` or `height` properties
  * `display: inline-block` elements will sit next to each other (if there is space available) and ARE affected by the `width` and `height` properties
  * `display: none` elements are removed from the flow of the document.

## The Box Model

You've learned how to apply colors, fonts, and basic spacing to HTML elements using CSS. Now you need to understand the fundamental concept that controls how elements take up space on a webpage: **the box model**.

### Web Design 101: Every Element is a Box

The first rule to understanding CSS and web design is that **every HTML element is rendered as a rectangular box**.

Even elements that don't look like boxes (paragraphs, headings, links, images, buttons) are all boxes. Understanding how these boxes work is essential for controlling layout and spacing.

Open up your `index.html` file from the `my-profile` folder in the browser. Then do the following:
1. Right click on your profile picture and select "Inspect". This will open your browser's **Developer Tools** where you can see the HTML structure of your elements and the CSS styles applied to them. In the bottom-right corner is the Box Model diagram.

    ![We can see the box of the profile picture when we inspect it.](./img/box-model-inspect-profile-page.png)

2. Choose the **element selector tool** (look at the developer tools and it is the arrow icon in the top-left corner). 
3. Then, hover over different elements. You'll see colored rectangles highlighting each element's box. Those rectangles aren't just visual aids—they represent how the browser actually thinks about every element.

You can also see how every element is a box if you add this CSS declaration to your webpage:

```css
/* All elements have a 1px solid red border */
* {
  border: 1px solid red;
}
```

![](../.gitbook/assets/everything-is-a-box.png)

Knowing that everything is a box, we must start by learning how to control those boxes.

## Box Model

The **box model** defines the size and spacing of any element and it consists of 4 parts, from the inside out:

1. The **content** — the space that the content of an element occupies (the text of a `<p>` tag, the image of an `img` tag, etc...)
2. The **padding** — the space around the content but inside the border. This is the "background" of the content.
3. The **border** — the space around the padding.
4. The **margin** — the space around the border and "between" adjacent elements.
    * Note: Margin is always transparent and the margins of adjacent elements can overlap.

![The box model has 4 parts: the content, the padding, the border, and the margin](../.gitbook/assets/margin-padding-content.png)

### Content, Width, and Height

The **content area** holds your actual content (text, images, or other nested elements).

You can control the content dimensions with `width` and `height`. 

Let's change the size of our profile picture:

```css
#profile-picture {
  border-radius: 50%;
  width: 300px; /* <-- add this */
}
```

Refresh your page. Your picture is now exactly 300px wide instead of the inherent size of the picture.

### Padding: Space Inside the Box

**Padding** creates space between the content and the border. It's inside the element so it takes on the element's background color.

Look at your list items, the text is looking a little bit cramped within their content box. Let's add some padding to give the text content some breathing room:

```css
li {
  font-family: roboto;
  margin-bottom: 0.5rem;
  padding: 0.25rem; /* <-- add this. play around with the value */
}
```

You can also assign different padding amounts on each side by providing multiple values. Let's make our navigation links more button-like:

```css
/* Add this entire CSS block */
nav a {
  padding: 10px 20px; /* 10px top/bottom, 20px left/right*/
  background-color: midnightblue;
  color: white;
  text-decoration: none;
}
```

{% hint style="info" %}
The `nav a` selector is an example of a "descendant selector". It selects all `<a>` elements inside of a `<nav>` element. */
{% endhint %}

**When to use padding:** When you want to create space inside an element—like keeping text away from the edges of your list items or making your navigation links more clickable.

### Border: The Edge of the Box

The **border** is the line around the padding. The `border` property has three values: width, style, and color.

Let's add borders to each `section` to visually separate them:

```css
/* Add this entire CSS block */
section {
  border: 2px solid midnightblue;
  padding: 20px;
}
```

Common border styles include `solid`, `dashed`, `dotted`, and `none` (default).

You can also control the curve of the corners of your border with `border-radius`:

```css
/* Add this entire CSS block */
section, li, nav a {
  border-radius: 8px;
}
```

### Margin: Space Between Elements

You may feel that the sections are too close to each other. We could also use some space above and below our profile picture.

**Margin** creates space between an element and other elements around it. Margin is outside the element, so it doesn't take on the background color.

Let's add some margin to the sections first

```css
section {
  border: 2px solid midnightblue;
  padding: 20px;
  margin: 20px; /* <-- add this */
}
```

Margin uses the same shorthand syntax as padding so we can add margin just to the top of our profile like so:

```css
 #profile-picture {
  border-radius: 50%;
  width: 300px;
  margin: 20px 0px; /* only add margin on top/bottom*/
}
```

### Margin Collapse

When two vertical margins meet, they don't stack on top of each other. Instead, the space between the elements is equal to the larger of the two margins. This is called **margin collapse**.

Try this with your sections. Replace your `margin` with `margin-top` and `margin-bottom`:

```css
section {
  border: 2px solid midnightblue;
  padding: 20px;
  margin-top: 40px;
  margin-bottom: 20px;
}
```

The space between the sections is **40px**, not 60px. The larger margin (40px) "wins".

{% hint style="info" %}
**Tip:** If you want consistent spacing, pick one direction (either `margin-bottom` or `margin-top`) and stick with it for all of your elements. This avoids margin collapse entirely.
{% endhint %}

Remember: this only happens with vertical margins, not horizontal margins 🤷.

#### Centering with Margin

Notice your `ul, ol` selector already has `margin: auto`. As long as the element has a valid `width` property set (in this case `fit-content` works), the element will be centered on the page.

```css
ul, ol {
  margin: auto;
  width: fit-content;
  text-align: left;
}
```

**Note: This only works if the element has a defined width**. See what happens if you remove the `width` property.

## Display

HTML elements have default `display` behaviors that affect
1. how an element's width and height can be modified.
2. how they interact with other elements.

For example, if we try adding a `width` to the `<a>` elements, nothing happens! 

```css
nav a {
  width: 200px; /* <-- add this */
  border: 3px solid white;
  padding: 10px 20px;
  background-color: midnightblue;
  color: white;
  text-decoration: none;
}
```

Also, notice how pretty much all of the elements are stacked on top of each other. However, the hyperlink `<a>` elements are next to each other. 

This is all impacted by the `display` property:

| Display Type            | Examples                           | Makes New Line | Can Set Height / Width?                    | When To Use                                                                                                   |
| ----------------------- | ---------------------------------- | -------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| `display: block;`       | Most elements                      | Yes            | Yes                                        | To stack elements vertically.                                                                                 |
| `display: inline`       | `<a>`, `<span>`, `<i>`, `<strong>` | No             | No (but can set horizontal padding/margin) | To display elements horizontally within a line of text.                                                       |
| `display: inline-block` | None                               | No             | Yes                                        | To display elements horizontally with control over height/width                                               |
| `display: none`         | None                               | N/A            | N/A                                        | To completely remove an element from view. Useful for when we want to hide/reveal an element using JavaScript |

Since `<a>` elements have `display:inline`, they appear next to each other AND we can't set their `height` or `width` properties.

**<details><summary>Q: We want our navigation links (`<a>` tags) to be 200px wide AND keep them on the same line. Which display should we use?</summary>**

If we wanted to keep the `<a>` elements on the same line but also have control over their dimensions, we can change them to have `display:inline-block`:

```css
/* This is the "descendant selector"—it selects all <a> elements inside of a <nav> element. 
This will NOT affect the other <a> elements elsewhere in the page.
*/
 nav a {
  display: inline-block; /* <-- add this */
  width: 200px; /* <-- add this */
  border: 3px solid white;
  padding: 10px 20px;
  background-color: midnightblue;
  color: white;
  text-decoration: none;
}
```

</details>

### Display Challenge

Let's change the display of the list items (`<li>`) within our list of programming skills. 

```html
<section>
  <h2>Programming Skills</h2>
  <ul>
    <li class="devtool">Git & GitHub</li>
    <li class="devtool">Command Line Interface</li>
    <li class="js-skill">JavaScript Fundamentals</li>
    <li class="js-skill">OOP and Classes</li>
    <li class="web-dev-skill">HTML</li>
    <li class="web-dev-skill" id="current-topic">CSS</li>
  </ul>
</section>
```

We want them to appear next to each other and have control over their width/height. What display should they have? What is the best way to target those elements?

Try it out, then look at the solution below.

**<details><summary>Solution</summary>**

By default, list items have `display:block` which stacks them on top of each other. To have them listed next to each other, we can give them `display: inline-block`.

The best way to target them is to add an `id` element to one of their ancestors, such as the `<section>`. That allows us to target them using a descendent selector.

**HTML**
```html
<section id="programming-skills"> <!-- Add the id here -->
  <h2>Programming Skills</h2>
  <ul>
    <li class="devtool">Git & GitHub</li>
    <li class="devtool">Command Line Interface</li>
    <li class="js-skill">JavaScript Fundamentals</li>
    <li class="js-skill">OOP and Classes</li>
    <li class="web-dev-skill">HTML</li>
    <li class="web-dev-skill" id="current-topic">CSS</li>
  </ul>
</section>
```

**CSS:**
```css
/* Target all <li> elements that are descendant of the #programming-skills element */
#programming-skills li {
  display: inline-block;
}
```

Try switching this to `display:inline` and see how it changes.

</details>

## The Box-Sizing Problem

By default, `width` and `height` only apply to the **content area**. Padding and border are added on top.

Let's see this problem with your navigation links. They currently have this styling:

**index.css**
```css
nav a {
  display: inline-block;
  width: 200px;
  border: 3px solid white;
  padding: 10px 20px;
  background-color: midnightblue;
  color: white;
  text-decoration: none;
}
```

**<details><summary>Q: What is the width of a navigation link?</summary>**

You might expect each link to be 200px wide. But the **actual width** is:
- Content: 200px
- Padding: 20px left + 20px right = 40px
- Border: 3px left + 3px right = 6px
- **Total width: 246px**

</details>


This is confusing and makes layout calculations difficult.

### The Solution: border-box reset

Add this to the **top** of your CSS file:

**index.css**
```css
* {
  box-sizing: border-box;
}

/* Rest of your CSS below... */
```

The `*` selector targets all elements. This rule changes how `width` and `height` are calculated.

With `box-sizing: border-box`, `width` includes content, padding, AND border. Now your navigation links are **exactly 200px wide**:
- Padding: 40px
- Border: 6px
- Content: 154px (automatically calculated as 200-40-6)
- **Total: 200px**

**Always include `box-sizing: border-box` at the top of your CSS. It makes layout predictable and matches how you naturally think about sizing.**

### General CSS Reset

Different browsers apply different default margins and paddings to elements like `body`, `h1`, `ul`, and `p`. This can cause inconsistent layouts across browsers.

A **CSS reset** removes these defaults, giving you a clean, predictable starting point. Add this to the top of every CSS file:

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
```

This reset:
- Removes all default margins and paddings
- Makes `width` and `height` include padding and border
- Ensures consistent behavior across all browsers

You'll then add spacing intentionally where you need it, giving you full control over your layout.

**TODO:** Take a moment and make adjustments to the padding and margins of the elements of your page until your are satisfied with the overall spacing.

## Test your Skills!

Head over to [MDN to test your box model skills](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Box_Model_Tasks)!

## Complete HTML & CSS

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Website</title>
  <link rel="stylesheet" href="./index.css">
</head>
<body>
  <header>
    <h1>My Website</h1>
    <nav>
      <a href="./index.html">Home</a>
      <a href="./about.html">About</a>
    </nav>
  </header>
  <main>
    <figure>
      <img id="profile-picture" src="https://avatars.githubusercontent.com/u/111444562?v=4" alt="Profile Picture">
      <figcaption>It's nice to meet you!</figcaption>
    </figure>
    <section id="programming-skills">
      <h2>Programming Skills</h2>
      <ul>
        <li class="devtool">Git & GitHub</li>
        <li class="devtool">Command Line Interface</li>
        <li class="js-skill">JavaScript Fundamentals</li>
        <li class="js-skill">OOP and Classes</li>
        <li class="webdev-skill">HTML</li>
        <li class="webdev-skill" id="current-topic">CSS</li>
      </ul>
    </section>
    <section>
      <h2>Favorite Quotes</h2>
      <ol>
        <li>Quote 1...</li>
        <li>Quote 2...</li>
        <li>Quote 3...</li>
      </ol>
    </section>
  </main>
  <footer>
    <p>Copyright Marcy Lab School 2025</p>
    <p>Learn more at <a href="https://marcylabschool.org">marcylabschool.org</a></p>
  </footer>
</body>
</html>
```

**index.css**

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
ul {
  list-style: none;
}
img {
  width: 100%;
}
body {
  background: azure;
  color: midnightblue;
  font-family: Helvetica;
  text-align: center;
  margin: auto;
  padding: 2rem;
}
header {
  margin-bottom: 1rem;
}
h1,
h2 {
  margin-bottom: 1rem;
}
figure {
  margin: 1rem 0;
}
footer {
  margin-top: 2rem;
}
ul,
ol {
  margin: auto;
  width: fit-content;
  text-align: left;
}
li {
  font-family: roboto;
  margin-bottom: 0.5rem;
  padding: 0.25rem;
}
.devtool {
  background: orange;
}
.js-skill {
  background: yellow;
}
.web-dev-skill {
  background: lightgreen;
}
#current-topic {
  font-weight: bold;
}
#profile-picture {
  border-radius: 50%;
  width: 300px;
  margin: 20px 0px; /* only add margin on top/bottom*/
}
a:hover {
  color: red;
}
a:active {
  color: green;
}
nav a {
  display: inline-block;
  width: 100px;
  border: 3px solid white;
  padding: 10px 20px; /* 10px top/bottom, 20px left/right*/
  background-color: midnightblue;
  color: white;
  text-decoration: none;
}
section {
  border: 2px solid midnightblue;
  padding: 20px;
  margin-bottom: 1rem;
}
section,
li,
nav a {
  border-radius: 8px;
}
#programming-skills li {
  display: inline-block;
}
```

