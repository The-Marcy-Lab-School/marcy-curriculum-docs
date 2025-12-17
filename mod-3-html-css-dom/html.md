# 1. HTML

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-0-0-html)!
{% endhint %}

**Table of Contents**

* [Markup Languages](html.md#markup-languages)
  * [The `file://` protocol vs the `http://` protocol](html.md#the-file-protocol-vs-the-http-protocol)
* [How to write HTML](html.md#how-to-write-html)
  * [Nesting Elements](html.md#nesting-elements)
  * [Links and Attributes](html.md#links-and-attributes)
  * [Images and Self-Closing Tags](html.md#images-and-self-closing-tags)
  * [Ids and Classes](html.md#ids-and-classes)
* [Properly formatted document](html.md#properly-formatted-document)
* [Semantic vs Non Semantic Elements](html.md#semantic-vs-non-semantic-elements)
* [Quiz](html.md#quiz)

## Markup Languages

The most basic kind of a file is a `.txt` file which simply stores a string of characters. A `.txt` file is not considered "code" because its characters are interpreted literally.

A **markup language** on the other hand contains symbols that will not be interpreted literally and instead influence how the text inside is viewed.

Markdown (written in `.md` files) is considered a markup language. For example, when we view (**"render"**) the double-asterisk symbols `**` surrounding a word, the text inside will become **bold**:

```md
Here is some **bold** text.
```

Similarly, **HTML (Hypertext Markup Language)** is a markup language and our browsers are the applications we use to view them.

```html
<h1>Hello World!</h1>
<p>This is some <strong>bold</strong> text</p>
```

To illustrate the difference between markup languages and regular `.txt` files, do the following:

* In your `mod-3` folder, create a new folder called `intro-to-html`
* Create two files called `index.txt` and `index.html`
* Put the code above inside both of them:
* Right click on the `index.html` file and select Reveal in Finder/Explorer
* Open Chrome. Then drag and drop the `index.html` into the tab bar (there should be a + icon)
* Then do the same for your `index.txt` file.

What is the difference between how they are presented by Chrome?

![A txt and an html file side by side.](../.gitbook/assets/txt-vs-html.png)

At the end of the day, your browser is essentially built to render text and images.

* The browser can interpret `.html` files and render content with style. It can also execute JavaScript!
* The first HTML file you should create for a new website should always be `index.html`
  * `index.html` is a magic name that servers will automatically look for if a user enters a domain without a file extension: `test.com` is the same as `test.com/index.html`.
* `index.html` is commonly known as the **entry point**

### The `file://` protocol vs the `http://` protocol

When you open an HTML file directly to view in your browser, your browser uses the `file://` **protocol** to retrieve the `.html` file from your file system.

![using the file protocol to get the html file](../.gitbook/assets/index-file-protocol.png)

* Most of the time, your browser will be using the **`https://` protocol** to get the `.html` file from a **server**.
* A **server** is just another computer, somewhere else, that provides a resource to another computer (over the internet in this case)
* **HTTPS** (Hypertext Transfer Protocol Secure) is how your browser communicates with a server.

![using the https protocol to get the html file](../.gitbook/assets/github-https-protocol.png)

## How to write HTML

**HTML** (**H**yper**t**ext **M**arkup **L**anguage) is the language used to build webpages. It describes **content** and **structure** of a webpage. In other words, what to display and where/how to display it.

**HTML elements** are created using a pair of **opening and closing tags**:

```html
<!-- This is a "heading" -->
<h1>My awesome website</h1>

<!-- This is a "paragraph" -->
<p>My hobbies are...</p>

<!-- This is an "unordered list", a.k.a. a bullet list -->
<ul>
  <!-- "list items" are nested inside the ul -->
  <li>coding</li>
  <li>soccer</li>
  <li>cooking</li>
</ul>
```

* We create a comment with the syntax `<!-- comment -->`
* Notice the opening tag (`<tagName>`) and the closing tag (`</tagName>`) for each HTML element
  * The tag name determines _how_ the content inside them is displayed (e.g. as a heading, a paragraph, or a bulleted list)
  * Only the content between the tags is displayed to the user. The angle brackets (`<>`) and the tag name are not displayed.

{% hint style="info" %}
**More about headings**: headings are created using the tags `h1` through `h6`. The first heading should always be `h1` and there should only be one `h1` per page. Subsequent headings should flow in descending order to indicate the structure of the document.
{% endhint %}

### Nesting Elements

As you may have noticed with the `ul` element, tags can **nest** other tags inside them.

An element that is commonly used to contain other elements is the `<div>` element (short for "division").

```html
<div>
  <p>This is nested inside the division</p>
  <p>We can put as much stuff in here as we want!<p>
  <div>
    <p>And nest quite deeply!</p>
  </div>
</div>
```

Nested elements should always be indented one level for readability.

Other examples of this are the **ordered list** and **unordered list** tags and their **list items**:

```html
<!-- An ordered list is rendered with numbers -->
<h2>Steps to make dinner</h2>
<ol>
  <li>Get freezer meal</li>
  <li>Put in microwave</li>
  <li>Eat</li>
</ol>

<!-- An unordered list is rendered with bullets -->
<h2>Pets</h2>
<ul>
  <li>Dogs</li>
  <li>Cats</li>
  <li>Fish</li>
</ul>
```

We refer to the element acting as the "container" as the **parent** and the elements nested inside as the **children**. Tags that are in the same nesting level next to each other are **sibling tags**.

<details>

<summary><strong>Q: What is the relationship between a <code>ul</code> and the <code>li</code> elements inside? What about the relationship between the <code>ul</code> and the <code>h2</code> element?</strong></summary>

The `ul` element is the parent and the `li` elements are its children. The `ul` and `h2` elements are siblings.

</details>

### Links and Attributes

One of the most basic elements in a website is a link to another page. We create a link element (a.k.a. an **"anchor"**) using the `<a>` tag:

```html
<!-- The href attribute goes in the opening tag -->
<a href="https://google.com">Go to Google</a>
```

Notice that this tag contains extra code inside of the opening `<a>` tag! `href` stands for "hyperlink reference" and is an "attribute". **Attributes** provide additional piece of information used to render an element and are included in the opening tag.

* In this case, the content between the tags is what the user will see while the `href` attribute tells the browser where to redirect the user after clicking on the link.

Links can take you to online sites, local or absolute pages of our own, or even reference elements within the page (they link to ids)

```html
<a href="https://google.com">Go to Google</a>
<a href="./">Go Home</a>
<a href="./about">Our About Page</a>
<a href="#more-information">See more information</a>
```

* Be careful with relative links: `"/"` is the root of the _server_ which may not be where you mean.
* Local links `./`, `../` will direct you from your current directory.
* Links can also be nested, but will still flow seamlessly in the final document.

```html
<p> If you're looking for things <a href="https://google.com">Go to Google</a>, and you'll see ads</p>
```

* Link text should _always_ be descriptive of where you're going, never just put "click here."

### Images and Self-Closing Tags

Another common element type is an image. Images have one required attribute `src` and an optional attribute `alt`

```html
<img 
  src="https://http.cat/images/415.jpg" 
  alt="cat eating a record" 
/>
```

* Notice that the image element doesn't require a closing bracket. It is considered a **self-closing tag**.
* `src` is the source location of the image
* `alt` describe the picture to screen readers.
  * If the content is central to your site, you _must_ include an `alt` attribute
  * If the picture is just a decoration (like a background image) you do not need an `alt` attribute.
  * Be specific with your `alt` text.

`src` can be a hyperlink (a link to another web page) or a local link:

```html
<img src="https://http.cat/images/415.jpg" alt="A cat eating a record" />

<img src="./images/lecture-prep/images/cat-pizza.png" alt="cat eating a pizza" />
```

* A cooler version of an image with a caption is `figure`, look it up!

```html
<figure>
  <img src="./images/lecture-prep/images/cat-pizza.png" alt="cat eating a pizza" >
  <figcaption>Here we see a cat eating a pizza it does not deserve.</figcaption>
</figure>
```

### Ids and Classes

Two attributes that we can apply to ANY element are `id` and `class`. These allow us to label elements.

* `id`s mark a tag as a _single_, unique, important item on your page.
* `class`es are for denoting a bunch of related tags, and can appear more than once per page.
* Both `id`s and `class`es are used to reference elements on pages so that they can be accessed, either by JS or CSS.
* They are a way to differentiate tags on your page from other identical tags.

```html
<h1 id="main-heading">Hello world!</h1>
<p class="blue-text">lorem ipsum</p>
<p>lorem ipsum</p>
<p class="blue-text">lorem ipsum</p>
<p>lorem ipsum</p>
```

* `id` and `class` names are case sensitive, cannot start with numbers, and can't have spaces.
* Stylistically, in pure html, you'll see `kebab-case` names.
* You can have multiple classes by separating with spaces, but only one id.
* You can include both an id and class on a single tag.

```html
<p id="important-text" class="blue funky-font">
  some text
</p>
```

* `id` = one name per page, one per tag
* `class` = multiple per page, multiple space separated names per tag

## Properly formatted document

* The way that HTML works is it's a "document" filled with "tags".
* Let's start simple with some boilerplate to set up our document

```html
<!DOCTYPE html>
<html lang="en">
  <!-- Everything else goes here -->
</html>
```

* These two tags make sure our browser knows what we're dealing with.
* Just copy them in without worrying.
* There are other doctypes and languages, but this is what we'll use

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
```

* The `head` tag is where we put meta information, scripts, links, and titles.
* The `title` tag is the only one visible to our users as what appears in the browser tag.
* `meta` does other things like tell the mobile browser how big to start the page and what character set we're using.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  <body>
    <h1>Hello World!</h1>
  </body>
</html>
```

* The `body` tag is where we can put things for our users to see.
* From here on out, always include the **boilerplate** above
* VS Code users: start with an empty document, type `html` and then from the popup, select `html:5` and hit Enter

**Create a new HTML file and type in `html:5`**

## Semantic vs Non Semantic Elements

* So far, all the tags we've made were **semantic** — their name told us about their function: `h` tags are headings, `a` are links, `p` are paragraphs.
* However, there's another kind of tag that conveys no meaning: **non-semantic**.
* Pretty much the only non-semantic tags you'll see are `div` and `span`. These are just for grouping other related tags together (div) or calling out a particular section of text (span).

```html
<div id="main-user-info">
  <p><span class="name-text">Name:</span> Zo</p>
  <p>Age: 24</p>
  <p>Hobbies:
  <ul>
    <li>Basketball</li>
    <li>Coding</li>
  </ul>
</div>
```

* Non-semantic tags should only be used for grouping or grabbing parts of text.

```html
<div>
  <div>Hobbies</div>
  <div class="hobby">Basketball</div>
  <div class="hobby">coding</div>
</div>
<!-- NO AWFUL. NEVER. -->
```

## Quiz

**Q: What is different about how the browser shows the `.txt` and `.html` files?**

<details>

<summary>Answer</summary>

A browser can show the raw contents of any file, such as a `.txt` file but it can render a `.html` file with more style!

</details>

**Q: Browsers are specialized "viewer" applications for `.html` files. What are other examples of applications that are made to view specific kinds of files?**

<details>

<summary>Answer</summary>

There are tons of examples! But here are a few

* Photoshop is used to display image files like `.jpg`, `svg`, etc...
* Adobe and Preview are made to view `.pdf` files
* iTunes is made to play `.mp3` files.
* Microsoft Word is made to view and edit `.doc` files

</details>

**Q: How do you think these different tags affect what is displayed?**

<details>

<summary>Answer</summary>

* The `h1` says to make a header.
* The `p` says to make a normal paragraph.
* The `ul` says to make an unordered list with three list items (`li`) in it.

</details>

**Q: What is wrong with the HTML below?**

```html
<h1>
Hello World!
</h3>
```

<details>

<summary>Answer</summary>

The closing tag does not match the opening tag! The closing tag should be `</h1>`.

Also, stylistically, we should indent the content of the element if we are putting each part of the tag on separate lines.

</details>

**Q: When should you use `<ol>` and when should you use `<ul>`?**

<details>

<summary>Answer</summary>

Use `<ol>` when order matters, and `ul` when it doesn't.

</details>

**Q: In the example above, which elements are parents, which elements are children, and which elements are siblings?**

<details>

<summary>Answer</summary>

* Both of the `h2` tags, the `ol` tag and the `ul` tag are sibling tags
* The `ol` and the `ul` are both parent tags to their `li` tags
* The `li` tags are all children tags of the `ol` or `ul` tags
* The `li` tags are sibling tags within their own lists.

</details>

**Q: What is wrong with this HTML element?**

```html
<img>cat-pizza.png</img>
```

<details>

<summary>Answer</summary>

The source of the image should be in the opening `<img>` tag as a `src` attribute.

There should also be an `alt` attribute describing the picture.

</details>

**Q: How would you create a `<p>` element with the id `"caption-1"` and the classes `"italic"` and `"centered"`?**

<details>

<summary>Answer</summary>

some text

</details>

**Q: What page does this anchor tag take us to?**

```html
<a href="./">Go Home</a>
```

<details>

<summary>Answer</summary>

The `index.html` page!

</details>

**Q: Which semantic tags should we use in the code snippet above?**

<details>

<summary>Answer</summary>

The outermost `div` is okay but the text `Hobbies` should be inside a `p` or a header and the hobbies themselves should be `li` elements inside of a `ul` or an `ol`.

```html
<div>
  <h3>Hobbies</h3>
  <ul>
    <li class="hobby">Basketball</li>
    <li class="hobby">coding</li>
  </ul>
</div>
```

</details>
