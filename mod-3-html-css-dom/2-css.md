# CSS

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-2-css)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Intro to CSS](#intro-to-css)
  - [CSS Syntax](#css-syntax)
- [Linking/Loading CSS Files](#linkingloading-css-files)
  - [External Stylesheets (Best Practice)](#external-stylesheets-best-practice)
  - [Internal Stylesheets With Style Tags (Fine If You Only Want 1 File)](#internal-stylesheets-with-style-tags-fine-if-you-only-want-1-file)
  - [Inline Styling (Practically Never Do This)](#inline-styling-practically-never-do-this)
    - [Inspecting CSS With Chrome Developer Tools](#inspecting-css-with-chrome-developer-tools)
- [Common CSS Properties](#common-css-properties)
  - [Color Properties](#color-properties)
    - [Color Values](#color-values)
  - [Typography Properties](#typography-properties)
- [Selectors](#selectors)
  - [Element Selector](#element-selector)
  - [Class Selector](#class-selector)
  - [ID Selector](#id-selector)
  - [Special Selectors](#special-selectors)
    - [Multi-Selectors](#multi-selectors)
    - [Child Selectors](#child-selectors)
    - [Pseudo-Class Selectors](#pseudo-class-selectors)
  - [Selector Specificity](#selector-specificity)
- [Deep Dive On Units: `px` vs `%` vs `rems`](#deep-dive-on-units-px-vs--vs-rems)
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

In the previous lesson, we learned how to structure content with HTML. Now we'll transform the appearance of our pages with CSS.

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
  background-color: azure;
  color: midnightblue;
}

figure {
  max-width: 300px;
}

img {
  width: 100%;
}
```

* **Selectors** (like `body`, `figure`, and `img`) specify which HTML element(s) to style. Selectors are followed by curly braces `{}` which contain the properties for the selected element(s).
* **Properties** (like `background`, `color`, `max-width`, etc.) tell our browser which aspect of the element to change.
* Each property has a set of valid **values** that can be assigned to them (like `azure` or `midnightblue` for colors, `center` for alignment, and `300px` or `100%` for sizing and spacing.)

Each `property: value;` pair is called a **declaration** that *must* be followed by a semicolon.

Now, let's see where we can put this CSS code.

## Linking/Loading CSS Files

There are three ways to add CSS to an HTML document:
1. Inside a separate `.css` file, loaded via a `<link>` tag (best practice)
2. Inside `<style></style>` tags (okay for small projects)
3. As attributes of individual elements (don't do this)

{% hint style="info" %}
Clone down the repo at the top of the lesson if you haven't already. It contains the completed HTML code from the last lesson. 
{% endhint %}

### External Stylesheets (Best Practice)

CSS is most commonly written in separate `.css` files that are loaded by an HTML file via the `<link>` element.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./style.css"> 
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

Then, in `style.css`, we can add some CSS selectors and declarations:

```css
body {
  background-color: powderblue;
  color: midnightblue;
}

img {
  width: 100%;
}

figure {
  max-width: 300px;
}
```

**TODO:** Add the CSS above into `style.css` and load it into your HTML file using the `link` tag. Then, copy that `link` tag into your `about.html` and `portfolio.html` pages.

**<details><summary>Q: What are the benefits of having CSS in an external file?</summary>**

Just like modules in a JavaScript program, external stylesheets can be reused by multiple pages that want to have the same styling. When you decide to make a change to the overall style of the website, all pages that use that stylesheet will be updated without the need to update pages individually.

Overall, this makes development and maintenance much easier and more organized.

</details>

### Internal Stylesheets With Style Tags (Fine If You Only Want 1 File)

In the `index.html` file and add these `<style>` tags and CSS within the `<head>`:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- Our external stylesheet -->
    <link rel="stylesheet" href="./style.css"> 
    
    <!-- An internal stylesheet -->
    <style>
      body {
        background-color: coral;
      }
    </style>
  </head>
  <body>
    <!-- Your content -->
  </body>
</html>
```

Note that the order of our external/internal stylesheets matters. See what happens if you put the external stylesheet *after* the internal one. The stylesheet that we add last will always "win out". This is what the "cascading" part of "Cascading Style Sheets" refers to.

This approach is appropriate under two circumstances:
1. if you want to have styles for a particular page override the external stylesheet
2. if you want to keep all of your code inside of one file and not use an external stylesheet at all

### Inline Styling (Practically Never Do This)

You can also add a `style` attribute directly to an individual element but this should only be done for testing purposes:

```html
<body style="background-color: red">

</body>
```

Inline styles are not a viable option for large scale styling. However, inline styles will always override both internal and external stylesheets. Therefore, it might be useful if you need to quickly test a CSS property and ensure that it will be applied to an element. 

But generally you should avoid this approach.

#### Inspecting CSS With Chrome Developer Tools

You can see how competing styles override each other if you open the Chrome Developer Tools and look at the styles applied to the `body` element

![](./img/css-overriding-devtools.png)

Notice how the styles "cascade" in the following manner:
* The inline `element.style` properties appear at the top and override all other styles
* Next are the internal stylesheet styles from `index.html`
* Third are the external stylesheet styles from `style.css`
* Last are the **user agent stylesheet** styles. These are the default styles applied by the browser.

The Chrome Developer Tools are incredibly useful for testing out your CSS styling. For example:
- Try disabling the inline `background` applied to the body and notice how the page will update immediately to use the next background color in the "cascade".
- Click on the named color `red` and type in `green` to see the color change
- Click on the color box and play around with the sliders to find a color that you like for the background.

**TODO:** Remove all inline styles and the internal stylesheet. We'll use the external stylesheet `style.css` moving forward.

## Common CSS Properties

Now that we know how to add CSS to our HTML files, let's learn the basic CSS properties that we can apply to any HTML element.

### Color Properties

One of the most obvious uses of CSS is to apply color to our otherwise black and white webpages.

In addition to beautifying our page, colors can make a page easier to navigate by visually distinguishing sections from each other.

```css
/* These colors will be inherited by all elements */
body {
  background-color: powderblue;
  color: midnightblue;
}

/* Use an accent color for call-to-action elements */
a {
  background-color: royalblue;
  color: white;
}
```

{% hint style="info" %}
**Choosing a Color Palette:** When designing a website, it is best to decide on a color palette with just a few colors to stick to. Here is a simple strategy to create a three-color palette:
- Choose one primary color to define your entire palette (e.g. `midnightblue`) 
- Choose a second color that has high-contrast with the first (e.g. `powderblue`)
- Choose a third "accent" color that you can use for call-to-action elements like buttons and forms (e.g. `royalblue`)

You can use a website like [https://mycolor.space](https://mycolor.space/?hex=%236F66CE&sub=1) to help come up with your color palettes!

{% endhint %}

#### Color Values

Color values can be written in a variety of ways. For example, these are all equivalent ways to use the color blue:
- **Named color**: `blue`
- **Red/Green/Blue (RGB)**: `rgb(0, 0, 255)`
  - Each value ranges from 0 to 255
- **Hex code**: `#0000ff`
  - Each pair of base-16 values range from `00` (0) to `ff` (255) and represent red/green/blue.

**<details><summary>Q: How would you make green using RGB and Hex? What about black and white?</summary>**

- Green: `rgb(0, 255, 0)` or `#00ff00`
- Black: `rgb(0, 0, 0)` or `#000000`
- White: `rgb(255, 255, 255)` or `#ffffff`

</details>

### Typography Properties

Your choice of font can add a nice touch of personality to a website. How you configure your typography can also make a big difference in the readability of your site.

Fun fonts can be downloaded from the internet (check out [Google Fonts](https://fonts.google.com/)) but classic fonts like Helvetica and Arial will never let you down:

```css
body {
  font-family: Helvetica, Arial, sans-serif;
  line-height: 1.5;     /* spacing between lines */
  text-align: center;   /* left, center, right, justify */
}

h1 {
  font-size: 3rem;      /* size of text */
}
```

Again, we can set these properties in the `body` and they will be inherited by all descendent elements. 

Here are a few notes to consider for each of these properties
* **`font-family`**: List multiple fonts separated by commas. The browser uses the first available font. Always end with a generic family like `sans-serif` or `serif` as a fallback.
* **`line-height`**: A line height between `1.4` and `1.6` improves readability. Use unitless numbers (e.g., `1.5`) so the line height scales with the font size.
* **`text-align`**: Use `left` for natural reading flow in most languages, but `center` can help for headings or to create visual emphasis. Avoid centering long paragraphs for readability.
* **`font-size`**: Use relative units like `rem` instead of `px` to ensure that your font responds to each device's base font size. For example, `1rem` is equivalent to `16px` for devices with "normal" text, `12px` for "small" text, and `24px` for "large" text.

**TODO:** Set the text alignment for the `ol` element to `left`

## Selectors

There are TONS of ways to select elements with CSS rules ([list of all the selectors](https://www.w3schools.com/cssref/css_selectors.php)). 

The most common selectors are:
* By element (select all `li` elements)
* By class name (select all elements with `class="blue"`)
* By id name (select the element with `id="email-form"`)
* Or some combination of these things

**TODO:** For each of the examples below, try adding them to your `style.css` file!

### Element Selector

We've already been using this selector type to target all elements of a specific type:

```css
/* Flip the color scheme to make the header stand out */
header { 
  background-color: midnightblue;
}

/* Use a contrasting color to make the section backgrounds stand out */
section {
  background-color: white;
}
```

### Class Selector

A `class` attribute can be added to elements in order to style them as a group.

**HTML:**
```html
<section>
  <h2>Programming Skills</h2>

  <h3>Developer Tools</h3>
  <ul>
    <li class="programming-skill">Git & GitHub</li>
    <li class="programming-skill">Command Line Interface</li>
  </ul>

  <h3>JavaScript Skills</h3>
  <ul>
    <li class="programming-skill">JavaScript Fundamentals</li>
    <li class="programming-skill">OOP and Classes</li>
  </ul>

  <h3>Web Development Skills</h3>
  <ul>
    <li class="programming-skill">HTML</li>
    <li class="programming-skill">CSS</li>
  </ul>
</section>
```

To target those elements, use a dot (`.`) followed by the class name:

**CSS:**
```css
.programming-skill {
  background-color: royalblue;
  color: white;
}
```

**Rules for classes:**
* Multiple elements can share the same class.
* Names are case-sensitive
* Cannot start with numbers
* Cannot contain spaces
* Use `kebab-case` for multi-word names (e.g., `programming-skill`)

**TODO:** Add a `"quote"` class to all of your quote list items and give them a background of `powderblue` and a text color of `midnightblue`.

### ID Selector

When we want to target a single element, we can add an `id` attribute in the HTML to label it


**HTML:**

```html
<p id="tagline">Mathematician | Pioneer of Computing | Visionary Thinker</p>
```

Then, to style that one element and no others, we write the name of the id with a hash (`#`):

**CSS:**

```css
#tagline {
  font-style: italic;
  color: royalblue;
}
```

Remember, each id should be unique on the page. If you want to style multiple elements the same way, use a class instead.

### Special Selectors

#### Multi-Selectors

You can apply the same styles to multiple selectors by separating them with a comma (`,`):

```css
/* Share these styles for both elements */
#tagline, figure {
  font-style: italic;
  color: royalblue;
}
```

**TODO:** Use the multi-selector syntax to set `text-align: left;` for these elements: `h3`, `ul`, and `ol`.

#### Child Selectors

Currently, the hyperlink `<a>` in the footer has the same styles as the navigation links in the header but we only want those styles to apply to the hyperlinks in the header.

You can select elements that are direct descendants of other elements using the right angle bracket `parent>child` syntax.

```css
nav>a {
  background-color: royalblue;
  color: white;
}
```

**Modify the `a` selector to use the child selector syntax**

#### Pseudo-Class Selectors

**Pseudo-class selectors** let us select an element in a particular **state** such as when it is being hovered:

```css
/* a link when the mouse hovers over it */
nav>a:hover {
  background-color: white;
  color: royalblue;
}
```

### Selector Specificity

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

## Deep Dive On Units: `px` vs `%` vs `rems`

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

**Q: How do you link an external CSS file called `style.css` to an HTML document? Where in the HTML should this go?**

<details>

<summary>Answer</summary>

Use the `<link>` tag with `rel="stylesheet"` and `href` pointing to the CSS file. It should go in the `<head>`:

```html
<head>
  <link rel="stylesheet" href="style.css">
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
img {
  width: 100%;
}

figure {
  max-width: 300px;
  margin-inline: auto;
}

body {
  background-color: powderblue;
  color: midnightblue;

  font-family: Helvetica, Arial, sans-serif;
  line-height: 1.5;
  text-align: center;
}

nav>a {
  background-color: royalblue;
  color: white;
}

header {
  background-color: midnightblue;
}

section {
  background-color: white;
}

h1 {
  font-size: 3rem;
}

ol {
  text-align: left;
}

.programming-skill {
  background-color: royalblue;
  color: white;
}

.quote {
  background-color: powderblue;
  color: midnightblue;
}

#tagline,
figcaption {
  font-style: italic;
  color: royalblue;
}

nav>a:hover {
  color: royalblue;
  background-color: white;
}
```