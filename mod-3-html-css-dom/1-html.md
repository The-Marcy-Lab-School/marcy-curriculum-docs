# HTML

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-0-0-html)!
{% endhint %}

**Table of Contents**

- [Key Concepts](#key-concepts)
- [Why HTML Matters for JavaScript Developers](#why-html-matters-for-javascript-developers)
- [Browsers and Markup Languages](#browsers-and-markup-languages)
  - [HTML is a Markup Language](#html-is-a-markup-language)
  - [Browsers Are Programs for Viewing HTML](#browsers-are-programs-for-viewing-html)
  - [The `file://` protocol vs the `https://` protocol](#the-file-protocol-vs-the-https-protocol)
- [How to write HTML](#how-to-write-html)
  - [Properly Structured HTML Document](#properly-structured-html-document)
- [Attributes: Adding Information to Tags](#attributes-adding-information-to-tags)
  - [Hyperlink "Anchor" Tags](#hyperlink-anchor-tags)
  - [Images and Self-Closing Tags (Void Tags)](#images-and-self-closing-tags-void-tags)
  - [Ids and Classes](#ids-and-classes)
- [Semantic Elements](#semantic-elements)
- [Quiz](#quiz)
- [Complete HTML](#complete-html)


## Key Concepts

- A **Browser** is a program that interprets and renders HTML
- Browsers use the **`file://` protocol** when opening HTML files locally; the **`https://` protocol** is used when accessing files from a server over the internet.
- **index.html** is the default entry point for a website: browsers and servers look for this file automatically.
* **HTML (Hypertext Markup Language)** describes the **content** and **structure** of a webpage.
* **HTML elements** are created using **opening and closing tags** (e.g., `<p>content</p>`).
* Tags can **nest** other tags. 
  * The container is called the **parent**
  * Elements inside are **children**
  * Elements at the same level are **siblings**.
* **Self-closing tags (void tags)** like `<img />` do not require a closing tag.
* **Attributes** provide additional information and are included in the opening tag (e.g., `href`, `src`, `alt`).
  * The `id` attribute marks a unique element on the page. 
  * The `class` attribute can be applied to multiple elements.
* A properly formatted HTML document includes `<!DOCTYPE html>`, `<html>`, `<head>`, and `<body>` tags.
* **Semantic elements** have names that describe their function (e.g., `heading`, `main`, `footer`, `section`, `aside`, `h1`, `p`, `ul`, `li`). 
  * **Non-semantic elements** like `div` and `span` are used for grouping elements for the purpose of styling.

## Why HTML Matters for JavaScript Developers

You've been building applications that run in the terminal using Node.js. These CLI apps take input, process data, and output results to the console. Now you're going to learn how to build applications with HTML, CSS, and JavaScript that run in a web browser instead.

You already know how to:

* Create and manipulate data with JavaScript
* Build classes to model real-world systems
* Handle user input in the terminal

HTML gives you a new environment to apply these skills: **the browser**. Instead of `console.log()` displaying text in the terminal, you'll create visual interfaces that users can see and interact with in a browser window.

**The Browser vs. Node**

| Node.js | Browser (HTML + CSS + JavaScript) |
| ---- | ------ |
| Text-based interface     |   Visual interface     |
|  Input via prompt-sync    |  Input via forms, buttons, clicks      |
| Output via console.log()     |  Output via updating HTML elements      |
|  Runs on your computer in the Terminal    |   Runs in user's browser     |

## Browsers and Markup Languages

### HTML is a Markup Language

The most basic kind of a file is a `.txt` file which simply stores a string of characters. A `.txt` file is not considered "code" because its characters are interpreted literally.

A **markup language** on the other hand contains symbols that will not be interpreted literally and instead influence how the text inside is viewed.

Markdown (written in `.md` files) is considered a markup language. For example, when we view (**"render"**) the double-asterisk symbols `**` surrounding a word, the text inside will become **bold**:

```md
Here is some **bold** text.
```

Just like Markdown, **HTML (Hypertext Markup Language)** is a markup language that defines the structure and content of webpages.

```html
<h1>Hello World!</h1>
<p>This is some <strong>bold</strong> text</p>
```

### Browsers Are Programs for Viewing HTML

To illustrate the difference between markup languages and regular `.txt` files, do the following:

* In your `mod-3` folder, create a new folder called `my-profile`
* Create two files called `index.txt` and `index.html`
* Put the code above inside both of them:
* Right click on the `index.html` file and select Reveal in Finder/Explorer
* Open Chrome. Then drag and drop the `index.html` into the tab bar (there should be a + icon)
* Then do the same for your `index.txt` file.

What is the difference between how they are presented by Chrome?

![A txt and an html file side by side.](../.gitbook/assets/txt-vs-html.png)

At the end of the day, your browser is just a program for interpreting and rendering HTML. It can also interpret/render CSS and execute JavaScript!

{% hint style="info" %}
The first HTML file you should create for a new website should always be `index.html`. Your browser will automatically try to load the `index.html` when given a set of HTML files from a server (or from your computer).

`index.html` is commonly known as the **entry point**.
{% endhint %}

### The `file://` protocol vs the `https://` protocol

![using the file protocol to get the html file](../.gitbook/assets/index-file-protocol.png)

When you open an HTML file directly to view in your browser, your browser uses the `file://` **protocol** to retrieve the `.html` file from your file system.

![The file protocol loads files directly from your own computer](./img/file-protocol.png)

The rest of the time, your browser will likely be using the **`https://` protocol** to get web files from a **server**.

![using the https protocol to get the html file](../.gitbook/assets/github-https-protocol.png)

A **server** is just another computer, somewhere else, that provides a resource to another computer.

**HTTPS** (Hypertext Transfer Protocol Secure) is how your browser communicates with a server over the internet.

![The http protocol loads files over the internet](./img/http-protocol.png)


## How to write HTML

**HTML** (**H**yper**t**ext **M**arkup **L**anguage) is the language used to build webpages. It describes **content** and **structure** of a webpage. In other words, what to display and where/how to display it.

**HTML elements** are created using **tags** wrapped in angle bracket `<>`. In your `index.html` file, replace the content with the following elements:

```html
<!-- This is a "heading" -->
<h1>Ada's Website</h1>

<!-- This is a "paragraph" -->
<p>My name is Ada Lovelace and I am a programmer.</p>

<!-- A subheading -->
<h2>Programming Skills</h2>

<!-- This is an "unordered list", a.k.a. a bullet list -->
<ul>
  <!-- "list items" are nested inside the ul -->
  <li>Git & GitHub</li>
  <li>Command Line Interface</li>
  <li>JavaScript Fundamentals</li>
  <li>OOP and Classes</li>
  <li>HTML</li>
</ul>
```

We create a comment with the syntax `<!-- comment -->`

Most tags have an opening tag (`<tagType>`) and closing tag (`</tagType>`)
* The tag type determines what kind of content is displayed (header, list item, paragraph, button, etc...)
* Only the content between the tags is displayed to the user. 
* The angle brackets (`<>`) and the tag name are not displayed.

{% hint style="info" %}
**More about headings**: headings are created using the tags `h1` through `h6`. The first heading should always be `h1` and there should only be one `h1` per page. Subsequent headings should flow in descending order to indicate the structure of the document.
{% endhint %}

**Quiz: Take a look at the HTML below and identify the errors (4)**

```html
<h1>My Website<h2>
<p My site about coding></p>
<un>
  <li>Coding</li>
<un>
```

**<details><summary>Q: What are the errors with the code above?</summary>**

1. The `<h1>` tag is opened, but instead of being closed with `</h1>`, it is incorrectly closed with `<h2>`. It should be `</h1>`.
2. The `<p>` tag is missing a closing angle bracket (`>`). It is written as `<p My site about coding></p>`; it should be `<p>My site about coding</p>`.
3. The unordered list tag is written as `<un>` instead of `<ul>`. Lists should use `<ul>`.
4. The second list is opened with `<un>`, but never closed, and should be `</ul>` to properly close the list.

</details>

### Properly Structured HTML Document

Every HTML document starts with the same "boilerplate" code (repetitive, standard code used as a starting point):

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- The title appears in the browser tab -->
    <title>Ada's Website</title>
  </head>
  <body>
    <!-- Visible Elements Go Here -->
  </body>
</html>
```

Let's break them down:

* `<!DOCTYPE html>` and `<html lang="en">` immediately tell the browser that the document it is rendering is HTML
* `<head>` contains "meta data" (information *about* the website) that won't show up in the webpage itself.
* `<body>` contains all of the elements that the user will see on the webpage.

{% hint style="info" %}
**Pro Tip!** In VS Code, when you create an empty `.html` document, type `html` and then from the popup, select `html:5` and hit Enter.
{% endhint %}

**TODO**: Create a new HTML page called `about.html` with an `<h1>About Me</h1>` header inside the body.

## Attributes: Adding Information to Tags

Tags can have **attributes** that provide additional information for the element.

### Hyperlink "Anchor" Tags

For example, to create a **hyperlink** to navigate to another page, we make an "anchor" element
```html
<!-- Attributes go in the opening tag -->
<a href="./about.html">About Me</a>
<a href="./index.html">Home</a>
```
Attributes and their values go in the opening tag of an element.
* `href` is the **attribute** (short for "hyperlink reference")
* `"./about.html"` and `"./index.html"` are **values**

**TODO: Add these links to your `index.html` and `about.html` pages.**

{% hint style="info" %}
The content between the `<a></a>` tags is visible to the user and should indicate the link's destination. Do NOT make hyperlink elements like this:

```html
<a href="https://some-malicious-website.com">Click me. It will be good. I promise</a>
```
{% endhint %}

Links can also be nested inside of `<p>` tags:

```html
<p>Head to the <a href="https://marcylabschool.gitbook.io">Marcy GitBook</a> to learn more about HTML!</p>
```

**TODO: Add these links to the bottom of your `index.html` and `about.html` pages.**

### Images and Self-Closing Tags (Void Tags)

Another common element type is an image. Images have one required attribute `src` and an optional, but strongly encouraged, attribute `alt`

```html
<img 
  src="https://http.cat/images/415.jpg" 
  alt="cat eating a record" 
/>
```

* `src` is the source location of the image
* `alt` describes the picture to screen readers.
  * If the content is central to your site, you _must_ include an `alt` attribute
  * If the picture is just a decoration (like a background image) you do not need an `alt` attribute.
  * Be specific with your `alt` text.

`src` can be a hyperlink (a link to another web page) or a local link:

```html
<img src="https://http.cat/images/415.jpg" alt="A cat eating a record" />

<img src="./images/lecture-prep/images/cat-pizza.png" alt="cat eating a pizza" />
```

A cooler version of an image is `figure` with a nested image and `figcaption`!

```html
<figure>
  <img src="./images/lecture-prep/images/cat-pizza.png" alt="cat eating a pizza" >
  <figcaption>Here we see a cat eating a pizza it does not deserve.</figcaption>
</figure>
```

{% hint style="info" %}
Notice that the image element doesn't require a closing bracket. It is considered a **self-closing tag** or a **void tag**.
{% endhint %}

**TODO:** Go to your GitHub profile. Right-click your profile image and select **Copy Image Address**. Then, add a `figure`, `img`, and `figcaption` to your website with that URL as the source and a caption that reads `"It's nice to meet you!"`.

### Ids and Classes

Two attributes that we can apply to ANY element are `id` and `class`. These allow us to label elements:

```html
<ul>
  <li class="devtool">Git & GitHub</li>
  <li class="devtool">Command Line Interface</li>
  <li class="js-skill">JavaScript Fundamentals</li>
  <li class="js-skill">OOP and Classes</li>
  <li class="webdev-skill" id="current-topic">HTML</li>
</ul>
```

* The `id` attribute labels a tag with a unique name (there should not be any duplicate ids within a page)
* The `class` attribute indicates a group of related elements

We'll learn more about CSS later but for now you can test this out by adding a `<style>` element into the `<head>` of your document:

```html
<head>
  <!-- other meta tags -->
  <style>
    /* All elements with class="devtool" will have an orange background */
    .devtool {
      background: orange;
    }

    /* All elements with class="js-skill" will have a yellow background */
    .js-skill {
      background: yellow;
    }
    /* All elements with class="webdev-skill" will have a yellow background */
    .webdev-skill {
      background: lightgreen;
    }

    /* Only this one element will be bold */
    #current-topic {
      font-weight: bold;
    }
  </style>
</head>
```

Here are some additional things to note about `id` and `class` attributes:
* `id` and `class` names are case sensitive, cannot start with numbers, and can't have spaces.
* You can have multiple classes by separating with spaces, but only one id.
* You can include both an id and class on a single tag.
* Use `kebab-case` for your `id` and `class` names

## Semantic Elements

Prior to HTML5 (released in 2014), the primary way to organize HTML elements was using a "division" element `<div>` with `id` and `class` attributes to describe them:

```html
<body>
  <!-- division -->
  <div id="header"> 
    <h1>My Website</h1>
    <!-- division -->
    <div id="nav">
      <a href="./index.html">Home</a>
      <a href="./about.html">About</a>
    </div>
  </div>
  <!-- division -->
  <div id="main-content">
    <!-- division -->
    <div id="image-container">
      <img src="img-url.png" />
    </div>
    <!-- division -->
    <div class="section">
      <h2>Programming Skills</h2>
      <ul>
          <li class="devtool">Git & GitHub</li>
          <li class="devtool">Command Line Interface</li>
          <li class="js-skill">JavaScript Fundamentals</li>
          <li class="js-skill">OOP and Classes</li>
          <li class="webdev-skill" id="current-topic">HTML</li>
      </ul>
    </div>
    <!-- division -->
    <div class="section">
      <h2>Favorite Quotes</h2>
      <ol>
        <li>Quote 1...</li>
        <li>Quote 2...</li>
        <li>Quote 3...</li>
      </ol>
    </div>
  </div>
  <!-- division -->
  <div id="footer">
    <p>Copyright Marcy Lab School 2025</p>
    <p>Learn more at <a href="https://marcylabschool.org">marcylabschool.org</a>
  </div>
</body>
```

Then, **semantic elements** like `<header>`, `<main>`, `<footer>`, `<section>`, and `<nav>` were added, transforming our HTML into this:

```html
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
      <img src="img-url.png" alt="Profile Picture" >
      <figcaption>It's nice to meet you!</figcaption>
    </figure>
    <section>
      <h2>Programming Skills</h2>
      <ul>
          <li class="devtool">Git & GitHub</li>
          <li class="devtool">Command Line Interface</li>
          <li class="js-skill">JavaScript Fundamentals</li>
          <li class="js-skill">OOP and Classes</li>
          <li class="webdev-skill" id="current-topic">HTML</li>
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
    <p>Learn more at <a href="https://marcylabschool.org">marcylabschool.org</a>
  </footer>
</body>
```

**TODO: Add the semantic elements to your document:** `header`, `main`, `footer`, `nav`, and `section`

Semantic tags make websites more understandable, accessible, and efficient by giving meaning to HTML content, which boosts search-engine optimization (SEO), helps assistive technologies (like screen readers), improves code readability for developers, and ensures a more consistent user experience across devices. 

They structure content logically, telling browsers and search engines what each part is, not just how it looks. 

## Quiz

**Q1: What is the difference between a markup language and a plain text file?**

<details>

<summary>Answer</summary>

A plain text file (`.txt`) stores characters that are interpreted literally. A markup language (like HTML or Markdown) contains symbols that influence how the text is displayed when rendered. For example, `<strong>bold</strong>` in HTML will display as **bold** text rather than showing the angle brackets.

</details>

**Q2: What is the difference between the `file://` protocol and the `https://` protocol?**

<details>

<summary>Answer</summary>

- The `file://` protocol retrieves HTML files directly from your local file system (your computer).
- The `https://` protocol retrieves HTML files from a server over the internet.

</details>

**Q3: What are the four errors in the HTML below?**

```html
<h1>Welcome<h1>
<p This is my website.</p>
<ol>
  <li>Item 1
  <li>Item 2</li>
<ol>
```

<details>

<summary>Answer</summary>

1. The `<h1>` tag is not properly closed—it should be `</h1>`, not `<h1>`.
2. The `<p>` tag is missing a `>` after `<p`—it should be `<p>This is my website.</p>`.
3. The first `<li>` is missing its closing `</li>` tag.
4. The list is closed with `<ol>` instead of `</ol>`.

</details>

**Q4: Look at the HTML below. Identify one parent-child relationship and one sibling relationship.**

```html
<main>
  <h1>My Blog</h1>
  <p>Welcome to my blog!</p>
  <ul>
    <li>Post 1</li>
    <li>Post 2</li>
  </ul>
</main>
```

<details>

<summary>Answer</summary>

- **Parent-child**: `<main>` is the parent of `<h1>`, `<p>`, and `<ul>`. Also, `<ul>` is the parent of the two `<li>` elements.
- **Siblings**: `<h1>`, `<p>`, and `<ul>` are siblings (they share the same parent). The two `<li>` elements are also siblings.

</details>

**Q5: What is wrong with this image element? How would you fix it?**

```html
<img>picture-of-cat.png</img>
```

<details>

<summary>Answer</summary>

Two issues:
1. `<img>` is a **void (self-closing) tag**—it should not have content between opening and closing tags.
2. The image source should be specified using the `src` attribute, and an `alt` attribute should describe the image.

**Fixed:**

```html
<img src="picture-of-cat.png" alt="A picture of a cat" />
```

</details>

**Q6: Create an anchor tag that links to `https://google.com` with the visible text "Search on Google".**

<details>

<summary>Answer</summary>

```html
<a href="https://google.com">Search on Google</a>
```

</details>

**Q7: Create a `<p>` element with the id `"intro"` and the classes `"highlight"` and `"large-text"` containing the text "Hello World".**

<details>

<summary>Answer</summary>

```html
<p id="intro" class="highlight large-text">Hello World</p>
```

Note: Multiple classes are separated by spaces within the same `class` attribute.

</details>

**Q8: What are the three required sections of a properly structured HTML document, and what does each contain?**

<details>

<summary>Answer</summary>

1. `<!DOCTYPE html>` and `<html>` — Declares the document as HTML
2. `<head>` — Contains metadata about the page (like `<title>`, character encoding) that isn't displayed on the page itself
3. `<body>` — Contains all the visible content that users see on the webpage

</details>

**Q9: Rewrite the following HTML using semantic elements instead of `<div>` tags:**

```html
<div id="header">
  <h1>My Site</h1>
</div>
<div id="main-content">
  <p>Welcome!</p>
</div>
<div id="footer">
  <p>Copyright 2025</p>
</div>
```

<details>

<summary>Answer</summary>

```html
<header>
  <h1>My Site</h1>
</header>
<main>
  <p>Welcome!</p>
</main>
<footer>
  <p>Copyright 2025</p>
</footer>
```

Semantic elements like `<header>`, `<main>`, and `<footer>` describe the purpose of the content, making the HTML more readable, accessible, and better for SEO.

</details>

## Complete HTML

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ben's Website</title>
  <style>
    /* All elements with the class "devtool" will be orange */
    .devtool {
      background: orange;
    }

    /* All elements with the class "js-skill" will be yellow */
    .js-skill {
      background: yellow;
    }
    
    .web-dev-skill {
      background: lightgreen;
    }

    #current-topic {
      font-weight: bold;
    }
  </style>
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
      <img id="profile-picture" src="https://avatars.githubusercontent.com/u/111444562?v=4" alt="Profile Picture" >
      <figcaption>It's nice to meet you!</figcaption>
    </figure>
    <section>
      <h2>Programming Skills</h2>
      <ul>
        <li class="devtool">Git & GitHub</li>
        <li class="devtool">Command Line Interface</li>
        <li class="js-skill">JavaScript Fundamentals</li>
        <li class="js-skill">OOP and Classes</li>
        <li class="web-dev-skill" id="current-topic">HTML</li>
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
  </footer>
    <p>Copyright Marcy Lab School 2025</p>
    <p>Learn more at <a href="marcylabschool.org">marcylabschool.org</a>
  </footer>
</body>
</html>
```

**about.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Ben's Website</title>
</head>
<body>
    <h1>About Me</h1>
    <a href="./index.html">Home</a>
    <a href="./about.html">About</a>
</body>
</html>
```