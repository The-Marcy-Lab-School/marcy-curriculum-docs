# Vite

**Table of Contents**:
- [What is Vite and Why Use It?](#what-is-vite-and-why-use-it)
  - [Live Server is a Development Server](#live-server-is-a-development-server)
  - [Module Bundler](#module-bundler)
  - [Why Use Vite?](#why-use-vite)
- [Setup](#setup)
- [Clean Up The Repo](#clean-up-the-repo)

{% embed url="https://www.youtube.com/watch?v=UnsukVi6hJY&ab_channel=TheMarcyLabSchool" %}

## What is Vite and Why Use It?

Vite is a tool for developing web applications by acting as a **module bundler** and a **development server**. Let's break down each of these terms.

### Live Server is a Development Server

So far, we've been using Live Server as our development server. But what does that mean?

A **server** is just a computer that shares its resources over the internet. A user's computer acts as the **"client"** and requests resources from the server using the `https://` protocol (the hypertext transfer protocol). 

When we visit a URL, like [https://www.google.com](https://www.google.com), our browser converts the **Domain Name** (`google.com`) into the **IP Address** of the server computer where the code for Google lives. Then, our computer sends a **request** to that server computer over the internet and the server sends a **response**.

![The client server interaction](img/client-server-interaction.png)

However, we only get to this point once we **deploy** our project on a **server hosting service**. Until that point, we need to simulate this HTTP request and response cycle using a **development server**. 

With a development server, our computer acts as both the client and the server.

Just like Live Server, Vite provides a development server for us to use! So then, why not just keep using Live Server?

### Module Bundler

Vite also acts as a **module bundler**. A module bundler combines all of the files that a server is providing to the client into a single file to reduce the total number of requests that the client needs to make. For example, if our project uses 10 files, the client would need to make 10 separate requests for those files. 

Instead, Vite will bundle those 10 files together into one (or into only a few) and send those to the client, improving rendering time and performance once the client receives the code.

This also means that we can install third-party Node modules using `npm` and use them in our browser-based applications (not just Node projects)! Vite will bundle those node_modules in a highly optimized manner.

### Why Use Vite?

Sure, you could build a project from scratch and manage your own "development" version and the "production" version. But **Vite is "lightweight"** (it doesn't slow down your process by using it) and the production version of your application will be **optimized for speed**.

**Vite is also quite versatile**. It can be used for both simple and complex projects, from front-end only applications that use nothing but Vanilla JS to robust full-stack applications using frameworks like React.



## Setup

**Objective(s)**: Create a Github repo and the project starter code using Vite

First, create a Github repository and clone it down.

Inside the repo, create a [Vite](https://vitejs.dev/guide/) project called `app` using the `npm create vite` command:

```sh
npm create vite
# > Project Name: app
# > Select a framework: Vanilla
# > Select a variant: JavaScript
```

This will create a folder in your repo called `app`. **This is your development version of the application.**

Then `cd` into the directory, install Vite dependencies and other dependencies for the project, and start the Vite development server:

```sh
cd app
npm i
npm run dev
```

At this point, you should be able to open up http://localhost:5173 to view the application. As you can see, Vite provides you with a simple counter application to get started. 

**TODO: Poke around and see if you can understand how the file structure is organized and how the application works, starting with `app/index.html`**

## Clean Up The Repo

**Objective(s)**: remove unwanted code from the provided files and organize all code into a src/ directory

Clean up the directory by removing some of the provided code. Delete the `counter.js` and `javascript.svg` files and move the `main.js` and `style.css` files into a `src` directory.

```sh
rm counter.js javascript.svg
mkdir src
mv main.js src/
mv style.css src/
```

All future JavaScript and CSS files you create should exist somewhere within `src`. Feel free to create more folders if you'd like.

Edit the provided starter code:
* Empty out the `main.js` file (keep the `import './style.css'` line)
* Empty out the `style.css` file, keeping the styles you want to keep (I like to keep `:root`, `body`, `h1`, and `#app` styles but its up to you — you can start fresh).
* Edit the `<script>` tag `index.html` (line 11) so that it references the new location of `main.js`

To test that everything works, add some code to your `main.js` file...

```js
console.log("hello world");
document.querySelector("#app").innerHTML += '<h1>Hello world</h1>';
```

...and then reopen http://localhost:5173. 

Once you've confirmed everything is connected, go ahead and **commit and push**. Let's get started!

> 💡 **Tip**: You don't need to stop and start you Vite Development Server because it has "hot reloading". If you ever need to run it again though, use the command `npm run dev`