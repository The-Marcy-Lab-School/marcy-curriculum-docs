# How to Create Projects with Vite

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-4-dev-servers-vite-esmodules)!
{% endhint %}

**Table of Contents**:

- [Key Concepts](#key-concepts)
- [Background](#background)
  - [What is a Server?](#what-is-a-server)
  - [Development Servers and Deployment](#development-servers-and-deployment)
- [What is Vite?](#what-is-vite)
  - [Vite Quick Starter](#vite-quick-starter)
  - [How the Counter App Uses ES Modules](#how-the-counter-app-uses-es-modules)
- [ES Modules: A Closer Look](#es-modules-a-closer-look)
  - [Why Use Modules?](#why-use-modules)
  - [Import and Export Syntax](#import-and-export-syntax)
  - [Try It: Add a Small Module](#try-it-add-a-small-module)
  - [Importing CSS](#importing-css)
  - [Importing JSON and other Files](#importing-json-and-other-files)
- [Challenge](#challenge)
  - [Starter Code](#starter-code)
  - [Task 1: Render Posts Dynamically](#task-1-render-posts-dynamically)
  - [Task 2: Use Modules](#task-2-use-modules)
  - [Solution](#solution)
- [Additional Reading](#additional-reading)
  - [Module Scripts, CORS, and Live Server](#module-scripts-cors-and-live-server)
  - [Vite's Build Tool and Deployment](#vites-build-tool-and-deployment)
  - [Importing Node Modules](#importing-node-modules)

## Key Concepts

* **Server** — Any computer that stores, manages, and shares resources over the internet.
* **Client** — Any computer that requests resources from a server.
* **HTTP** — Hypertext Transfer Protocol. The protocol used to transfer data between two computers over the internet.
* **Deploy** — To transfer web application files from one's own computer to a server for the purposes of distribution.
* **Development Server** — A program that runs on a developer's computer to simulate a deployed project.
* **ES Modules** — The syntax supported by browsers for organizing code into modules. Lets you use `import` and `export` to share values between files instead of relying on the global namespace.
* **Module Script** — A script tag with `type="module"`. Enables ES module syntax, loads only the entry file (other files load via imports), and waits for the DOM before running. Requires the page to be served over `http://` (not `file://`) due to CORS.
* **CORS (Cross-Origin Resource Sharing)** — A browser security feature that restricts webpages from loading resources from a different origin than the one that served the page. Opening HTML via `file://` is treated as a different origin than your own `.js` files, so module loading (and `fetch`) fail until you use a development server.

**Key Syntax**
* **Default export** — Export the single most important value from a file: `export default theMainFunction;`
* **Named export** — Export one of many values: `export const oneOfMany = () => {};` or `export anotherOfMany;`
* **Importing** — You must include the `.js` extension in browser module paths: `import theMainFunction from './the-main-function.js';` and `import { oneOfMany, anotherOfMany } from './named-exports.js';`

## Background

Before diving into Vite, it's important to go over a few key concepts. We'll go over the terms below.

Vite will help us to deploy our project to a server allowing for clients around the world to access our projects via the HTTP protocol. Vite will also help us during development by simulating a deployed environment via a development server.

Let's get into it!

### What is a Server?

Until now, you've been using the `file://` protocol to open up projects on your own computer. All web applications, even project like Google, started out this way. So, how is it that you can access Google with your browser? When you visit [https://google.com](https://google.com), what exactly is happening?

Well, Google is "hosted" on a server that lets you access it over the internet.

A **server** is any computer that stores, manages, and shares resources over the internet.

![The client server interaction](img/dev-servers-vite-esmodules/client-server-interaction.png)

A user's computer plays the role of the **"client"** and sends an **HTTP request** to get resources from the server using the `https://` protocol (the hypertext transfer protocol).The server then sends an **HTTP response** with the requested content.

For example, when we visit [https://www.google.com](https://www.google.com) in our browsers, we are sending an HTTP request to Google's server. In response, Google's servers send us the HTML, CSS, and JavaScript that makes up the Google site.

{% hint style="info" %}
`google.com` is just the human-readable **domain name** of Google's servers. Every server also has a unique 4-digit IP address (**I**nternet **P**rotocol address) that precisely locates the server in the vast world wide web.

For example, Google's servers have the IP address [142.251.41.14](http://142.251.41.14/). Put that IP address into your search bar and it will also take you to Google!

When we send an HTTP request, our browser takes the domain name (`google.com`) and looks up the IP address using a DNS (Domain Name Service) before sending a request to the server.
{% endhint %}

### Development Servers and Deployment

All web applications, even Google, started out as a coding project on someone's computer. Then, then they **deploy** their project to a publicly accessible server allowing anyone to access their code via the internet and the `https://` protocol.

Until now, our browsers have been using the `file://` protocol to access files located on our own computers. But if we ever want to deploy our projects, sharing them with the world via the `https://` protocol, then we should begin using that protocol during development.

> _Deployment: the action of bringing resources into effective use._

With a **local development server**, we can simulate the HTTP protocol to access the files on one's own computer, even though the internet is not required to do so.

{% hint style="info" %}
The standard domain name and IP address of a local development server are:

* Domain Name: `http://localhost`
* IP Address: `127.0.0.1`
{% endhint %}

There are important differences between how the browser treats websites served via the `file://` and `http://` protocols. Specifically, the browser prevents us from doing these two things when using the `file://` protocol:
1. **Importing and exporting modules across files**
2. **Fetching data from third-party APIs over the internet**

Attempting to do either of these things when using the `file://` protocol will result in **CORS (Cross-Origin Resource Sharing)** errors. Not so with the `https://` protocol!

By using a development server, we can use the `http://` protocol throughout the entire development process. This ensures that our applications behave the same way in development and "in production" and enables us to take advantage of those two essential pieces of functionality.

Let's start by making a development server with Vite. Then, we'll look at how to use ESModules. We'll learn about fetching data in a future lesson.

## What is Vite?

According to the [Vite](https://vite.dev/guide/#getting-started) documentation:

> Vite (French word for "quick", pronounced /vit/, like "veet") is a build tool that aims to provide a faster and leaner development experience for modern web projects. It consists of two major parts:
>
> * A **development server** that provides rich feature enhancements over native ES modules, for example extremely fast Hot Module Replacement (HMR).
> * A **build command that bundles your code** with [Rollup](https://rollupjs.org/), pre-configured to output highly optimized static assets for production.

In simpler terms, Vite provides a feature-rich developer server as well as a command line tool that will optimize our application when we are ready to deploy.

Once we learn how Vite works, it can dramatically improve our development and deployment experience. It will be slow at first to use a new development tool, however the long-term benefits often outweigh the short-term frustrations so be patient!

The best way to learn is by doing. Let's make a project using Vite!

### Vite Quick Starter

To start a new project using Vite, do the following:

1. Start by creating a new repository on GitHub. Make sure it has a `README.md` file
2. Clone down the repo
3.  Inside the repo, create a [Vite](https://vitejs.dev/guide/) project using the `npm create vite@latest` command:

    ```sh
    npm create vite@latest
    # > Project Name: app
    # > Select a framework: Vanilla
    # > Select a variant: JavaScript
    ```

    This will create a folder in your repo called `app` that will serve as the "development" version of the application (later, when you are ready to deploy, you will also create a "production" version).
4. Open up the `app` directory and look around. Vite will have created the following files and directories for you to get started:
   * `package.json`: defines the scripts and dependencies of your project. Notably, when you run `npm i` in your `app` directory, it will install the `vite` command-line tool. It's the first place to look when working on a new project.
     * `package-lock.json`: Read more about package lock files [here](https://docs.npmjs.com/cli/v9/configuring-npm/package-lock-json).
   * `index.html`: the "entry point" of your application. It contains only a `div#app` element and loads the `src/main.js` file. This must remain in the root of your `app` directory.
   * `.gitignore`: lists filepaths to be ignored when making a commit. Importantly, you'll see that `node_modules/` are ignored.
   * `public/`: contains files that you want to be publicly accessible. Often, these are images that you reference in your HTML or CSS.
   * `src/`: contains the JavaScript and CSS that make up your application.
     * `main.js`: is the "entry point" of your JavaScript code. It **imports** other files—we'll use this to learn how ES modules work.
     * `style.css`: contains the CSS and is imported into `main.js` (yes, you can import CSS in JavaScript with Vite!).
     * `counter.js`: defines the counter logic and **exports** a function that `main.js` uses.
     * `javascript.svg`: a logo used in the starter UI.
5.  `cd` into the `app` directory and install the `vite` command line tool and other dependencies for the project

    ```sh
    cd app
    npm i
    ```
6.  Start the Vite development server:

    ```sh
    # according to package.json, this runs the vite command
    npm run dev 
    ```
7. Open up [http://localhost:5173](http://localhost:5173) to view the starter application. As you can see, Vite provides you with a simple counter application to get started.

![The Vite starter project is a simple counter application.](../.gitbook/assets/vite-starter-project.png)

{% hint style="info" %}
💡 **Tips**: You don't need to stop and start you Vite development server when you make changes because it has "hot reloading". Make a few changes to the application and notice it auto-update!

To stop the development server enter <kbd>Ctrl + C</kbd>.

To restart the development server, use the command `npm run dev`.
{% endhint %}

### How the Counter App Uses ES Modules

The Vite counter app already uses **ES modules**. This section walks through how it works so you can use the same pattern in your own projects

The key features of a project built with ES modules are:
1. There is only one `<script type="module">` tag in the `index.html` file that loads the "entry point" JavaScript file (`main.js`).
2. Other files are loading into the entry point file using imports.

**1. Only one script in the HTML**

Open `index.html`. The body has a single script tag:

```html
<script type="module" src="/src/main.js"></script>
```

The `type="module"` attribute tells the browser to treat `main.js` as an ECMAScript (ES) module and allows it to import the remaining files.

We often refer to `main.js` as the **"entry point"**.

**2. `main.js` imports what it needs**

Open `src/main.js`. It might look like this:

```js
import './style.css'
import javascriptLogo from './javascript.svg'
import viteLogo from '/vite.svg'
import { setupCounter } from './counter.js'

// ... DOM setup ...
setupCounter(document.querySelector('#counter'))
```

The `import` keyword is how we import values when running JavaScript in the browser. It is the equivalent to `require()` in Node.

* `import './style.css'` — loads the CSS (Vite allows importing CSS in JS). No variable name; the import runs for its side effect.
* `import javascriptLogo from './javascript.svg'` — **default import**: the SVG file exports one value (the URL), so we give it a name.
* `import { setupCounter } from './counter.js'` — **named import**: `counter.js` exports a function named `setupCounter`, so we pull it out with curly braces.

With these imports, the code is able to use `setupCounter` to wire up the button without ever touching the global scope—everything comes from *explicit* imports.

{% hint style="info" %}
The `import` (and `export`) keywords will not work unless the entry point is loaded as a module using the `<script type="module">` tag.
{% endhint %}

**3. `counter.js` exports the function**

Open `src/counter.js`:

```js
export function setupCounter(element) {
  let counter = 0
  const setCounter = (count) => {
    counter = count
    element.innerHTML = `count is ${counter}`
  }
  element.addEventListener('click', () => setCounter(counter + 1))
  setCounter(0)
}
```

The **named export** `export function setupCounter(...)` makes that function available to any file that imports it. Nothing else in `counter.js` is exposed—variables like `counter` and `setCounter` stay private to the file.

So the flow is: **HTML loads `main.js` → `main.js` imports `counter.js` and `style.css` → the counter runs.** The result is only one script tag with clear dependencies.

## ES Modules: A Closer Look

### Why Use Modules?

Without modules, you'd load multiple `<script>` tags and rely on each file adding variables to the **global namespace**—every variable would be shared across all files. 

```html
<script src="src/counter.js"></script>
<script src="src/main.js"></script>
```

That leads to:

* Unclear dependencies making it hard to know where a variable comes from
* Messy global variables with many files mutating the same names.
* Load order issues (e.g. if `main.js` uses code from `counter.js`, you must load `counter.js` first)

With **ES modules**, you explicitly **export** what you share and **import** what you use. Only the entry file is in the HTML; the rest are loaded via imports. Dependencies are clear and the global namespace stays clean. The counter app is already structured this way and should be emulated for your own projects!

If you've used Node, you've seen **CommonJS** (`module.exports` and `require()`). Browsers use **ES module** syntax instead: `export` and `import`. The idea is the same; the syntax is different.

### Import and Export Syntax

* **Named export** — Use when a file exposes one or more values by name. The counter app uses this for `setupCounter`:

  ```js
  export function setupCounter(element) { /* ... */ }

  // Or: export const oneOfMany = () => { };  and  export { anotherOfMany };
  ```

* **Default export** — Use when a file has one main value (e.g. a single function or object). The SVG imports use default exports (the file provides one URL):

  ```js
  const theMainThing = () => { };
  export default theMainThing;
  ```

* **Importing** — Use the path to the file and, for named exports, curly braces. Include the `.js` extension:

  ```js
  import { setupCounter } from './counter.js';   // named
  import theMainThing from './the-main-thing.js'; // default
  import './style.css';                           // side effect only
  ```

### Try It: Add a Small Module

Practice import/export by adding a tiny module the counter app uses.

1. Create `src/greeting.js` with a default export:

   ```js
   const greeting = () => 'Hello from a module!';
   export default greeting;
   ```

2. In `main.js`, add an import at the top and use it somewhere

    ```js
    import greeting from './greeting.js';

    const greetingEl = document.createElement('p');
    greetingEl.textContent = greeting();
    document.body.append(greetingEl);
    ```

3. Save and check the app in the browser. Only `main.js` is in the HTML yet `greeting.js` is loaded automatically when `main.js` imports it.

**Summary:** Use `export` and `export default` to share values, and `import` (with the `.js` extension) to use them. The counter app already follows this pattern; you can extend it or use the same pattern in the Challenge below.

### Importing CSS

As you may have noticed, rather than linking the `style.css` in the `index.html` file, we imported it into `main.js`! For now, compared to linking in HTML, this approach doesn't provide much benefit. However, when we get to React it will provide some benefits.

```js
import './style.css';
```

### Importing JSON and other Files

With Vite we can import JSON files directly and other file types directly.

Take a look at the `data/` directory in the root of the repo for this lesson. In `main.js` import its files and print the imported data to the console.

We can't do this using the `file://` protocol or using simple development servers like Live Server.

{% hint style="info" %}
**A Note on Storing Static Files**:

* Files stored in the `public` directory are treated as if they were stored in the root of the project. `/` represents the `public` folder so the `vite.svg` file in the root of `public` is accessed like so: `/vite.svg` (see `index.html`).
* Files stored anywhere else are accessed using a relative file path. For example, `'../../data/example.json'` is referenced as two levels above `main.js`.
{% endhint %}

## Challenge

### Starter Code

The HTML and CSS code below is starter code to build a photography application called Ada's Photography. Replace the content in `index.html` and `style.css` with this starter code:

{% tabs %}

{% tab title="HTML" %} 

Copy the HTML below into the <code>body</code> of the <code>index.html</code> file

Notice the `#total-posts` and `#posts-container` elements. We'll target those using JavaScript and fill them dynamically.

```html
<body>
  <header>
    <h1>Ada's Photography</h1>
    <nav>
      <ul>
        <li><a href="#">Animals</a></li>
        <li><a href="#">Cities</a></li>
        <li><a href="#">Tech</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <h2>Animals</h2>
    <p>Total posts: <span id="total-posts">0</span></p>
    <ul id="posts-container"></ul>
  </main>
  <footer>
    <p>Made with 💜 by Marcy Lab School</p>
  </footer>
  <script type="module" src="/src/main.js"></script>
</body>
```

{% endtab %}

{% tab title="CSS" %} 

Some basic CSS to get started with.

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
  object-fit: cover;
  border-radius: 10px;
  aspect-ratio: 1;
}

body {
  background: slategray;
  color: white;
  text-align: center;
  font-family: math;
}

header, footer {
  background-color: slategray;
  padding: 1rem;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

nav ul {
  display: flex;
  gap: 1rem;
}
nav a {
  text-decoration: none;
  color: white;
}
nav a:hover {
  text-decoration: underline;
}

main {
  background: white;
  color: black;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 1rem;
  align-items: center;
}

#posts-container {
  display: grid;
  gap: 1rem;
  grid-template-columns: 1fr 1fr;
}

#posts-container li {
  max-width: 400px;
}
```

{% endtab %}

{% endtabs %} 

### Task 1: Render Posts Dynamically

Now, take a look at the `data/posts.json` file. It contains photography data that we want to render dynamically. It is your task to use JavaScript in `main.js` to do the following:

* import the post object from the `data/posts.json` file.
* update the `#total-posts` span element with the proper number of posts
* render a `li` for each post object with the following structure:
    ```html
    <li id="post-1">
      <figure>
        <img src="https://images.unsplash.com/photo-1684857653651-e80e9ed160d7" alt="A bird washing its feathers">
        <figcaption>look at this bird!!</figcaption>
      </figure>
    </li>
    ```

Hint: Use `Object.values(posts).forEach((post) => {})` to iterate through the object of posts

### Task 2: Use Modules

If you were able to implement this all in the `main.js` file, congrats! Now, take advantage of ES modules and split your code into separate files for better organization:
* Keep the `data/posts.json` import in `main.js`
* Create a file called `dom-helpers.js` that exports a single DOM helper function called `renderPosts(posts)`
* `renderPosts` should take in the `posts` object and update the total posts count and render the list items.
* Import the `renderPosts()` method into `main.js` before invoking it.

### Solution

The solution can be found in the `adas-photography-solution/` folder.

## Additional Reading

### Module Scripts, CORS, and Live Server

The script tag must have `type="module"` to use `import`/`export`. Vite's template already does that but you can certainly use module scripts outside of Vite.

One important limitation though: **browsers do not allow module loading over the `file://` protocol.** If you open the HTML file directly from your file system, the browser treats it as origin `null` and blocks loading other local files (including your `.js` modules) because of **CORS**. 

[Learn more about CORS.](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

You must serve the page over `http://` so modules work. That's why we use a development server like Vite.

Another popular option for serving a page over HTTP is the **Live Server** extension. It is quick and easy to setup although it lacks the build tool that Vite comes with and doesn't work with React projects. As such, we only recommend it for simple testing and prefer Vite for building projects from the ground up.


### Vite's Build Tool and Deployment

The other main feature of Vite is its "build tool". When it is time to deploy our project, Vite will bundle all of our files into one HTML file, one JS file, and one CSS file to be efficiently delivered to our users over the internet.

This will improve load times and our user's overall impression and experience using our application!

To run the build command, run the command:

```sh
npm run build
```

This will create a `dist/` folder (short for "distribution", a synonym for the "production" version of your application). Inside will be the aforementioned HTML file and JS and CSS files in the `dist/assets/` folder. Take a look at those files to see how they have been "minified"!

To see how this distribution version runs, run the command:

```sh
npm run preview
```

To deploy this distribution version of your application, check out the article on [How to Deploy on GitHub Pages](https://marcylabschool.gitbook.io/marcy-lab-school-docs/projects/deploying-vite-with-github-pages).


### Importing Node Modules

Lastly, we can install NPM dependencies into our projects and import them by only their name. To test this, install the `uuid` package:

```sh
npm i uuid
```

This `uuid` package provides functions for generating **Universally Unique IDs** which are helpful for identifying objects in a large dataset.

Finally, add the following code to `main.js` below the other imports:

```js
import { v4 as generateUUID } from 'uuid';

const newUUID = generateUUID();
document.body.append(`your new uuid is ${newUUID}`);
```

In order to use this `uuid` package with Live Server, we'd have to specify the full path to this module:

```
./node_modules/uuid/dist/esm-browser/index.js
```

Yuck! 🤮
