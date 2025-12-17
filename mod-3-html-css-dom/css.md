# 2. CSS

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-0-1-css)!
{% endhint %}

**Table of Contents**

* [Before CSS There Was Only HTML](css.md#before-css-there-was-only-html)
* [What is CSS?](css.md#what-is-css)
* [Linking/Loading CSS Files](css.md#linkingloading-css-files)
* [Anatomy of CSS](css.md#anatomy-of-css)
* [Selectors](css.md#selectors)
* [Selector Specificity](css.md#selector-specificity)
* [Units: `px` vs `%` vs `rems`](css.md#units-px-vs--vs-rems)
* [Quiz!](css.md#quiz)

## Before CSS There Was Only HTML

When the world wide web was born, it was just a means of communicating information. The world of design hadn't yet made its way to the internet and websites looked like this:

![An HTML page with no styling.](../.gitbook/assets/html-page-no-css.png)

With just the addition of CSS, that same paged can be dramatically transformed:

![The same page with CSS.](../.gitbook/assets/css-zen-garden.png)

You can see this page at [https://csszengarden.com/](https://csszengarden.com/), a website dedicated to showing the power of CSS. It showcases a variety of designs of the the exact same HTML. The only thing that changes is the CSS!

## What is CSS?

* **CSS** stands for **Cascading Style Sheets**
* **CSS rules** tell our browser how to display our HTML
* You can **select** elements on the page by tags, ids, classes, and more, and then apply styles to them.

```css
p.vivid {
  color: purple;
  font-family: Helvetica;
  font-weight: bold;
}
```

> CSS let's us say "I want all paragraph elements with the class "vivid" to have purple bold text in Helvetica font.

* The browser already has some **default styles** but we can override those styles with our own.
* We can change the **color**, the **size**, the **font**, the **position**, and more of any element on the page.

## Linking/Loading CSS Files

* CSS rules are most commonly written in `.css` files
* In order for CSS rules to be applied to an HTML page, the `.html` file must use a `<link rel="" href="" />` tag that references the `.css` file:

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

* The `rel` (relevance) attribute tells the browser what kind of file is being linked (other file types can also be loaded using this tag)
* The `href` (hyperlink reference) attribute is an absolute/relative path to the `.css` file

> 💡 Tip! Test that the link worked by adding a few super obvious rules in the CSS file to make sure it's actually changing the HTML appearance. For example, make the `body` have a purple background!

## Anatomy of CSS

* A CSS rule is made up of a **selector** and a **declarations**

![Anatomy of a CSS rule with one selector and two declarations](../.gitbook/assets/css-anatomy-of-a-rule.png)

* the **selector** is what chooses the element(s) that will be styled
* the **declarations** are the actual styles that will be applied

Declarations are made up of property/value pairs (like an object) and MUST end with a semicolon (unlike an object)

```css
/* This is a CSS Comment */

/* one line is okay */
p { color: red; text-align: left; } 

/* Most of the time we spread it out */
h1 { 
  color: blue;
  text-align: center;
}
```

## Selectors

* There are TONS of ways to select elements with CSS rules ([list of all the selectors](https://www.w3schools.com/cssref/css_selectors.php)). The most common selectors are:
  * By tag (select all `p` elements)
  * By class name (select all elements with `class="blue"`)
  * By id name (select the element with `id="email-form"`)
  * Or some combination of these things

```css
/* Select all paragraph elements */
p { 
  font-family: fantasy;
  font-size: 20px;
}

/* Select all elements with class="blue" */
.blue {
  color: blue;
}

/* Select all p elements with class="blue" */
p.blue {
  font-style: italic;
}

/* Select the element with id="special" */
#special {
  background: yellow;
}
```

* You can also select children tags of a particular parent tag

```css
/* selects all li tags that are in ul tags that have the class="links" attribute */
ul.links-list > li {
  color: black;
}
```

* **Pseudo-classes** let us select an element in a particular **state** such as when it is being hovered over or has been clicked:

```css
a { color: black; }
a:visited { color: purple } /* a link once a user has visited the page */
a:hover { color: red } /* a link when the mouse hovers over it */
a:active { color: blue; } /* a link the moment it is clicked */
```

## Selector Specificity

* When one HTML element is styled by two conflicting CSS rules, **selector specificity** determines how conflicting style rules are resolved
* By default, CSS is applied top-to-bottom and the rule that comes last is applied (think of it like coats of paint being applied on top of each other):

```css
p {
  color: red;
}

p {
  color: blue;
}
```

* The more selectors you add, the more specific the rule is. Selectors with greater specificity will override rules defined by selectors with lesser specificity.

There are four categories which define the specificity level of a selector:

* Inline styles - Example: `<h1 style="color: pink;">`
* IDs - Example: `#navbar`
* Classes, pseudo-classes, attribute selectors - Example: `.test`, `:hover`, `[href]`
* Elements and pseudo-elements - Example: `h1`, `::marker`

```css
p#subtitle {
  color: red;
}

p {
  color: blue;
}
```

* The `p` with the `id="subtitle"` attribute will be red, because it's more specific than the selector `p`.

{% hint style="info" %}
Specificity can be calculated! Start at 0, add 100 for each ID value, add 10 for each class value (or pseudo-class or attribute selector), add 1 for each element selector or pseudo-element.

Note: Inline style gets a specificity value of `1000`, and is always given the highest priority!

Note 2: There is one exception to this rule: if you use the `!important` rule, it will even override inline styles!

[Read more about CSS Specificity here](https://www.w3schools.com/css/css_specificity.asp)
{% endhint %}

## Units: `px` vs `%` vs `rems`

* The `px` unit sets the exact size of an element. **Setting the size of an element using `px` values should be avoided if possible** as they don't comply with accessibility standards.

```css
p {
  font-size: 30px;
}

div {
  width: 100px;
}
```

* The `rem` unit sets a size of an element relative to the browser's **base font size** which is typically `16px` by default. User's can change their base font size as an accessibility feature. If your website uses `rem` units instead of `px`, your font will scale according to your user's accessibility settings.

```css
p {
  /* 32px if the base font size is 16px */
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

**Q: How would you make all `p` tags blue? How would you make only the `p` tags with `class="fun"` green? How would you make the second `p` tag `orange`?**

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

**Q: What will be the color of paragraph?**

```html
<p class='fun'>Hello World</p> 
```

```css
.fun {
  color: orange;
}
p.fun {
  color: blue;
}
p {
  color: red;
}
#its-purple {
  color: purple
}
```

<details>

<summary>Answer</summary>

It will be blue! The selector `p.fun` is the most specific selector that applies to this element.

</details>

**Q: what is the width of the `img` element based on the CSS below?**

```css
img {
  width: 200px;
  padding: 5px;
  margin: 5px;
}
```

<details>

<summary>Answer</summary>

A 200px wide element with 5px of padding on either side and 5px of margin on either side will actually be 220px wide.

</details>

**Q: By default, the `width` property affects the size of the "content" box and padding and margin are added to the outside of this content box. What kinds of challenges does this present?**

<details>

<summary>Answer</summary>

To set the absolute size of an element, we have to calculate the width, padding, and border properties of the box model, which can either be challenging or annoying to maintain.

</details>

**Q: What will the total width of the `img` element be after applying the CSS rules below? What will the width of the content box be?**

```css
* {
  box-sizing: border-box;
}
img {
  width: 200px;
  padding: 10px;
  border: 5px solid black;
  margin: 10px;
}
```

<details>

<summary>Answer</summary>

The width of the entire `img` will be `200px` but the width of the image's content box will be `170px`.

The `margin` will still add `10px` of space on all sides causing the element to take up `220px` worth of space.

</details>

**Q: A user sets their font size to "extra-large" which changes the base font size to `32px`. What will the size of an element with `font-size: 2rem;` be in pixels?**

<details>

<summary>Answer</summary>

`1rem` is equal to the base font size of `32px` so `2rem` will be `64px`.

</details>
