# 1. Intro to React

{% hint style="info" %}
Follow along with code examples in the lecture repo!
{% endhint %}

In this lesson, you will learn the basics of React.

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [What is React and Why Use It?](#what-is-react-and-why-use-it)
  - [JSX](#jsx)
  - [Components](#components)
- [The Structure of a React Application](#the-structure-of-a-react-application)
  - [index.html: All Content is Added to the Root Div](#indexhtml-all-content-is-added-to-the-root-div)
  - [main.js: Rendering the Root Component (App)](#mainjs-rendering-the-root-component-app)
  - [App.jsx: The Root Component](#appjsx-the-root-component)
  - [Challenge: Component Composition](#challenge-component-composition)
- [Inserting Data into Components](#inserting-data-into-components)
  - [Props: Sharing Data Between Components](#props-sharing-data-between-components)
  - [Challenge: Props Refactor](#challenge-props-refactor)
  - [Rendering A List of Elements](#rendering-a-list-of-elements)
- [Under the Hood: JSX Code Must Be Compiled](#under-the-hood-jsx-code-must-be-compiled)
  - [Vite in Development: The Dev Server](#vite-in-development-the-dev-server)
  - [Vite in Production: npm run build](#vite-in-production-npm-run-build)
  - [Serving the Build from Express](#serving-the-build-from-express)
- [Bonus Reading: React's Virtual DOM](#bonus-reading-reacts-virtual-dom)


## Essential Questions

By the end of this lesson you should be able to answer:

1. How does building UIs with React compare and contrast with building UIs with Vanilla JS and the DOM API?
2. What is JSX and how do we write it?
3. What is a component and how do you make one?
4. What are props and how do we use them?
5. What is compilation, when is it necessary, and how do we compile React code?

## Key Concepts

* **React** — a library for building reusable, composable, and scalable user-interfaces made up of components.
* **Component** — a piece of the UI (user interface) that has its own logic and appearance. Components are functions that return JSX.
* **JSX** — an extension of JavaScript that lets you write HTML-like syntax in React components.
* **Component Composition** — the process of combining smaller, reusable components together to create larger, more complex components
* **Root Component** — the top-level component that all other components are children of. Typically called `App`.
* **`react-dom/client`** — a React package that lets you render React components on the client (in the browser)
* **Prop** — a piece of data passed from a parent component to a child component.
* **Compilation** — the process of translating code written in one programming language (which is easy for humans to read) into another format (which is easy for a computer to execute).

## What is React and Why Use It?

**React** is a JavaScript library for building user-interfaces (UI). Similar to the DOM API (`document.createElement()`), it lets us programmatically create and update HTML in a JavaScript file — but with two key advantages: JSX and components.

Keep in mind that React is just a "wrapper" built on top of the DOM API. The result is a better experience for the developer and, in some cases, a more optimized experience for the user. But everything that we do in React can be implemented, with much more effort, with Vanilla JavaScript.

### JSX 

**JSX** is an extension of JavaScript that lets us write HTML-like syntax inside of functions.

JSX allows developers to create user interfaces **declaratively** (we describe the resulting HTML structure that we want to produce):

```jsx
// React — declarative, readable
const Text = ({ message }) => {
  return <p className="header">{message}</p>;
};

// Used like an HTML tag:
const helloWorld = <Text message="hello world" />
```

Notice how we "invoke" the `Text` function by using it like an HTML element `<Text message="hello world" />`

To produce the same structure with the DOM API and Vanilla JavaScript, developers write **imperatively** (we give step-by-step instructions for assembling an element):

{% hint style="danger" %}
```js
// Vanilla JS + DOM API — imperative, verbose
const makeHeaderElement = (message) => {
  const p = document.createElement("p");
  p.className = "header";
  p.innerText = message;
  return p;
};
// Invoke the function to create the element
const helloWorld = makeHeaderElement('Hello World');
```
{% endhint %}

If you know what you're doing, the Vanilla JS approach works fine. However, React makes the process of designing user interfaces more enjoyable while introducing performance optimizations under the hood.

### Components

**Components** are the functions that return JSX. Every piece of a user interface can be built as a component: a navigation bar, a product card, a single button.

Because they are functions, they are inherently **reusable** and **composable**. This means that you can build small components and combine them into larger ones. For example, a group of `ProductCard` components can be assembled into a larger `ProductGrid` component.

Here, we have a `InstagramPost` component made up of `Figure` and `LikeButton` components:

```jsx
// Components are functions that return JSX. This is a one-liner with an implicit return
const LikeButton = () => <button>❤️</button>;

// Components that return multiple lines use (). 
// `src` and `text` are like parameters for the component called "props"
const Figure = ({ src, text }) => {
  return (
    <figure>
      <img src={src} />
      <figcaption>{text}</figcaption>
    </figure>
  );
}

// A component can be "composed" of other components
const InstagramPost = ({ src, text }) => {
  return (
    <div className="instagram-post-card">
      <Figure src={src} text={text} />
      <LikeButton />
    </div>
  );
};

// To "invoke" a component function, write it like an HTML element
const post = <InstagramPost src={"img/cat.jpeg"} text={"meow"} />
```

Here are a few rules about creating React components:
* Component function names must use PascalCasing (UpperCamelCase).
* A component must return a single surrounding element. When returning multiple elements, wrap them in parentheses `()`.
* Components can take in values from the parent component called "props". They behave like parameters.

{% hint style="info" %}
Since we are writing this HTML syntax in a JavaScript file, we can't add a `class=""` attribute to our element because `class` is a reserved keyword. Instead we use `className=""`. Inspect the element in your browser and you'll see that the `className` attribute in JSX is converted into the HTML attribute `class` when rendered in the browser.

Any HTML attribute that shares a name with a JavaScript keyword has an alternate name in React. The `for` attribute for `<label>` elements is another example of this since `for` is a reserved JavaScript keyword for making a loop. Instead, we use the `htmlFor` attribute.

```jsx
<label htmlFor="email">Email</label>
<input id="email" name="email" type="text" />
```
{% endhint %}

## The Structure of a React Application

Now that we know the basics of React, let's see how a React application can be built.

React projects are essentially the same as the Vanilla JS projects you've been building so far: they start with an `index.html` file that runs some JavaScript files.

Vite provides a really great starting template for us to build React projects with:

```bash
npm create vite
# Choose Project Name
? Project name: <name of your project>
# select React
# select JavaScript

cd <name of your project> && npm i
npm run dev
```

Let's look at the rest of the files. Every React project made by Vite will have the following key files:

* `index.html` — the HTML file served to the client. It loads `src/main.jsx`.
* `src/main.jsx` — the entry point of the app. It uses the `react-dom/client` package to render the **root component** `App` into the DOM.
* `src/App.jsx` — the root component.
* `package.json` — note that a React vite project has a few React-related dependencies that the Vanilla project does not have.

Run the React application with `npm run dev` and you will see a simple counter application.

> Notice the `.jsx` extension? Without it, we wouldn't be able to write JSX in this file.

### index.html: All Content is Added to the Root Div

First, look at the `index.html` file and notice that it has an empty `body` except for a `div#root` element. In addition, it loads the `main.jsx` file. 

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + React</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

With React, instead of adding HTML content directly to the `index.html` file, we will use JavaScript to dynamically generate *all* of the content inside of the `div#root` element!

### main.js: Rendering the Root Component (App)

Head over to the `main.jsx` file, this is the "entry point" for our React code.

The primary purpose of this file is to render the root component `App` inside of the `div#root` element. The code is boilerplate (the same every time) and looks like this:

```jsx
// main.jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

When building a project with Vite, all of this logic is provided for you so no need to memorize it. That said, here are a few noteworthy things about this setup code:
* `StrictMode` is a wrapper-component that detects potential React-related issues in our application. It doesn't render anything visible.
* `createRoot` is imported from the `client` version of `react-dom` but there is also a `native` version for building mobile apps with React (a.k.a. "React Native").
* We can import `.css` files directly into our JavaScript!
* `createRoot()` takes in an HTML node (`document.getElementById('root')`) to use as the root of the HTML structure.
* The `.render()` method is invoked with React components and JSX inside. This is what inserts the `App` component inside of the `div#root` element.

### App.jsx: The Root Component

`App` is the standard name for the **root component** — every other component in the app will eventually be nested inside it.

Delete the Vite boilerplate and replace the contents of `App` with some simple HTML:

{% code title="App.jsx" lineNumbers="true" %}
```jsx
import './App.css'

const App = () => {
  return (
    <main>
      <h1>My Pet Pics</h1>
      <figure>
        <img alt="Reggie the cat" src="images/cat.jpeg" />
        <figcaption>Check out my cute cat Reggie!</figcaption>
      </figure>
    </main>
  )
}

export default App
```
{% endcode %}

Everything is in one big component right now. That's fine to start — but as the app grows, we'll want to break pieces out into their own components.

### Challenge: Component Composition

**Your turn:** Take the `App` component above and refactor it so that:
1. The `<h1>` lives in its own `Header` component.
2. The `<figure>` and its child elements live in its own `InstagramPost` component.
3. `App` renders both using components inside of the `<main></main>` element. 
4. Add the following CSS rule to the `index.css` file and apply it to the `figcaption` in the `InstagramPost` component.

```css
.italic {
  font-style: italic;
}
```

To render a component, use it like a self-closing HTML tag: 

```jsx
<element>
  <ChildComponentName />
</element>
```

**<details><summary>Solution</summary>**

```jsx
const Header = () => <h1>My Pet Pics</h1>

const InstagramPost = () => {
  return (
    <figure>
      <img alt="Reggie the cat" src="images/cat.jpeg" />
      <figcaption className="italic">Check out my cute cat Reggie!</figcaption>
    </figure>
  );
};

const App = () => {
  return (
    <main>
      <Header />
      <InstagramPost />
    </main>
  );
};

export default App
```

**Notice:**
* `InstagramPost` and `App` each return more than one line of JSX, so the return value is wrapped in `()`
* `Header` only returns a single line of JSX so we can write it as a one-liner without `()`

</details>

## Inserting Data into Components

One of the most essential and useful features of React and JSX is how easy it is to utilize data to render components.

We can insert any JavaScript expression into our JSX using curly braces `{}`.

```jsx
const pictures = [
  { id: 1, user: "ada123", alt: "Reggie the cat", src: "images/cat.jpeg", caption: "Check out my cute cat Reggie!" },
  { id: 2, user: "ada123", alt: "Robert the dog", src: "images/dog.jpeg", caption: "Check out my cute dog Robert!" },
  { id: 3, user: "ada123", alt: "Daffy the Duck", src: "images/duck.jpeg", caption: "Check out my cute duck Daffy!" },
];

const Header = () => <h1>My Pet Pics — {pictures.length} Posts</h1>;
// Produces the HTML: <h1>My Pet Pics — 3 Posts</h1>
```

We can also use JavaScript expressions to set attribute values:

```jsx
const InstagramPost = () => {
  return (
    <figure>
      <img 
        src={pictures[0].src} 
        alt={`${pictures[0].alt} by ${pictures[0].user}`}
        className="insta-pic" 
      />
      <figcaption>{pictures[0].caption} by {pictures[0].user}</figcaption>
    </figure>
  );
};
```

There are two things to note here:
1. Attributes expect a single value. So, we use string interpolation to combine the values `pictures[0].alt` and `pictures[0].user` into a single string.
2. For the inner content of the `figcaption` element, React is smart enough to handle a mixture of values and will combine them intelligently without the need for interpolation.

{% hint style="info" %}
You *could* write the contents of `figcaption` as a single expression:

```jsx
<figcaption>{`${pictures[0].alt} by ${pictures[0].user}`}</figcaption>
```

But this is not necessary and results in a poorer reading experience.
{% endhint %}

### Props: Sharing Data Between Components

Look at the previous example and notice that the `InstagramPost` component is hard-coded. It will always render the first picture in the `pictures` array, minimizing its value as a reusable component.

To make components reusable while incorporating outside data, we use **props** ("properties").

Props are values passed down to a React component by the parent component. Those properties are defined using `prop={value}` syntax, similar to attribute assignment:

```jsx
const pictures = [
  { id: 1, user: "ada123", alt: "Reggie the cat", src: "images/cat.jpeg", caption: "Check out my cute cat Reggie!" },
  { id: 2, user: "ada123", alt: "Robert the dog", src: "images/dog.jpeg", caption: "Check out my cute dog Robert!" },
  { id: 3, user: "ada123", alt: "Daffy the Duck", src: "images/duck.jpeg", caption: "Check out my cute duck Daffy!" },
];

// props will always be an object
const InstagramPost = (props) => {
  return (
    <figure>
      <img 
        src={props.picture.src} 
        alt={`${props.picture.alt} by ${props.picture.user}`}
        className="insta-pic" 
      />
      <figcaption>{props.picture.caption} by {props.picture.user}</figcaption>
    </figure>
  );
};

// We set props with the syntax propName={value}
<InstagramPost picture={pictures[0]} />
<InstagramPost picture={pictures[1]} />
<InstagramPost picture={pictures[2]} />
```

You can pass as many props as you'd like and each will be listed inside the `props` argument:

```jsx
<ChildComponent prop1={value1} prop2={value2}>
```

Since `props` is an object, we often will immediately destructure the provided values in the parameter list of the child component:

```jsx
const ChildComponent = ({prop1, prop2}) => {
  // ...
};
```

### Challenge: Props Refactor

Take the code sample above and refactor it such that `picture` prop is destructured from the `props` object. Update the rest of the code accordingly. How does this improve the code?

**<details><summary>Solution</summary>**
```jsx
// Destructure picture out of the props object and replace `props.picture.X` with `picture.X`
const InstagramPost = ({picture}) => {
  return (
    <figure>
      <img 
        src={picture.src} 
        alt={`${picture.alt} by ${picture.user}`}
        className="insta-pic" 
      />
      <figcaption>{picture.caption} by {picture.user}</figcaption>
    </figure>
  );
};
```
</details>

### Rendering A List of Elements

When we have an array of data that we want to turn into components, like `pictures`, we can use `array.map()` to create an array of components. We can insert that array of components directly inside of our JSX using the `{}` syntax, often inside of a `ul`:

```jsx
const Header = () => { /* ... */ }
const InstagramPost = ({picture}) => { /* ... */ }

const App = () => {
  // We can take the pictures variable out of the global scope and use props to share its data
  const pictures = [
    { id: 1, user: "ada123", alt: "Reggie the cat", src: "images/cat.jpeg", caption: "Check out my cute cat Reggie!" },
    { id: 2, user: "ada123", alt: "Robert the dog", src: "images/dog.jpeg", caption: "Check out my cute dog Robert!" },
    { id: 3, user: "ada123", alt: "Daffy the Duck", src: "images/duck.jpeg", caption: "Check out my cute duck Daffy!" },
  ];

  return (
    <main>
      <Header />
      <ul>
        {pictures.map((picture) => <InstagramPost picture={picture} key={picture.id} />)}
      </ul>
    </main>
  );
};
```

**Note:** Each rendered element needs a unique `key` prop so React can efficiently differentiate each element, typically an `id` is used.

## Under the Hood: JSX Code Must Be Compiled

JSX in our code cannot simply be executed by our browser: browsers only understand plain JavaScript.

Try opening your `index.html` file directly in a browser — or trying to serve it from Express as a static file — and the browser will throw a `SyntaxError`. 

The JSX syntax in `main.jsx` and `App.jsx` has to be transformed first in a process called **compilation**. Compilation is the process of translating code written in one programming language (which is easy for humans to read) into another format (which is easy for a computer to execute).

Below you can see a simplified example of how React code in a `.jsx` file is compiled into pure JavaScript that can be executed by your browser.

{% tabs %}

{% tab title="JSX (Uncompiled)" %}

This is what you write in your `.jsx` files. It looks like HTML, making it very intuitive to define the structure of your UI.

```jsx
function WelcomeCard({ user }) {
  return (
    <div className="card">
      <h1 title="Header">Hello, {user.name}</h1>
      <p>Your role is: {user.role}</p>
    </div>
  );
}
```

{% endtab %}

{% tab title="JavaScript (Compiled)" %}

This is what the browser actually receives. The compiler has stripped away the "HTML" tags and replaced them with standard JavaScript function calls.

```js
// This is a simplified version of the output
function WelcomeCard({ user }) {
  return React.createElement(
    "div",
    { className: "card" },
    React.createElement(
      "h1", 
      { title: "Header" }, 
      "Hello, ", 
      user.name
    ),
    React.createElement(
      "p", 
      null, 
      "Your role is: ", 
      user.role
    )
  );
}
```

{% endtab %}

{% endtabs %} 

### Vite in Development: The Dev Server

When you run `npm run dev`, Vite starts a **development server** that intercepts every request for a `.jsx` file and transforms JSX into plain JavaScript on the fly before sending it to the browser. This transformation happens in memory — nothing is written to disk.

This is why the dev server is **development only**. It is fast and convenient for iteration, but you wouldn't run a Vite dev server in production.

### Vite in Production: npm run build

When you're ready to deploy, run:

```bash
npm run build
```

Vite compiles every `.jsx` file into plain JavaScript (and bundles everything together) and outputs the result into a `dist/` folder. The `dist/` folder contains only browser-ready files: HTML, CSS, and plain `.js`.

```
dist/
├── index.html
└── assets/
    ├── index-abc123.js   ← your entire React app compiled to plain JS
    └── index-abc123.css
```

You can open `dist/index.html` directly in a browser with no server at all — it's just static files.

### Serving the Build from Express

Now that the build output is plain static files, your Express server can serve them with a single line:

```js
// server/index.js
app.use(express.static('frontend/dist'));
```

This tells Express: "for any request that doesn't match an API route, look for a matching file in `frontend/dist/` and send it."

The full-stack setup looks like this:

```
my-app/
├── server/          ← Express (serves API + the built frontend)
│   └── index.js
└── frontend/        ← Vite React app
    ├── src/
    └── dist/        ← npm run build outputs here
```

In development you run both servers separately (Vite dev server + Express). In production, only Express runs — it serves the static `dist/` files for the frontend and handles `/api/*` routes for the backend.

## Bonus Reading: React's Virtual DOM

[React's Virtual DOM](https://blog.logrocket.com/virtual-dom-react/#comparison-chart-real-virtual-shadow-dom).
