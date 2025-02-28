# Intro to React

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/7-0-0-intro-react-f23)!
{% endhint %}

In this lesson, you will learn the basics of React.

**Table of Contents:**

* [Terms:](1-intro-to-react.md#terms)
* [What is React and Why Use It?](1-intro-to-react.md#what-is-react-and-why-use-it)
  * [A. Say goodbye to the clunky DOM API](1-intro-to-react.md#a-say-goodbye-to-the-clunky-dom-api)
  * [B. Component Composition is fast and easy to read](1-intro-to-react.md#b-component-composition-is-fast-and-easy-to-read)
  * [C. The Virtual DOM offers some performance benefits when re-rendering components](1-intro-to-react.md#c-the-virtual-dom-offers-some-performance-benefits-when-re-rendering-components)
* [Starting a React Project (with Vite)](1-intro-to-react.md#starting-a-react-project-with-vite)
  * [Rendering the Root Component With ReactDOM](1-intro-to-react.md#rendering-the-root-component-with-reactdom)
  * [Components and JSX](1-intro-to-react.md#components-and-jsx)
  * [Nested Components](1-intro-to-react.md#nested-components)
  * [Adding Classes and Style](1-intro-to-react.md#adding-classes-and-style)
* [Rendering Data with React](1-intro-to-react.md#rendering-data-with-react)
  * [Sharing Data Between Components With Props](1-intro-to-react.md#sharing-data-between-components-with-props)
  * [Rendering A List of Elements](1-intro-to-react.md#rendering-a-list-of-elements)
* [Under the hood: JSX Code must be compiled](1-intro-to-react.md#under-the-hood-jsx-code-must-be-compiled)

## Terms:

* **React** — a library for building reusable, composable, and scalable user-interfaces made up of components.
* **Component** — a piece of the UI (user interface) that has its own logic and appearance. Components are functions that return JSX.
* **JSX** — an extension of JavaScript that lets you write HTML in React components.
* **Component Composition** — the process of combining smaller, reusable components together to create larger, more complex components
* **Root Component** — the top-level component that all other components are children of. Typically called `App`.
* **`react-dom/client`** — a React package that lets you render React components on the client (in the browser)
* **Prop** — a piece of data passed from a parent component to a child component.

## What is React and Why Use It?

**React** is a JavaScript library for building user-interfaces (UI).

React focuses on building reusable **components** that can be composed into larger components. This allows developers to build applications that scale efficiently.

Need more reasons to want to learn React?

### A. Say goodbye to the clunky DOM API

Compare and contrast these code snippets for creating a dynamic text element.

Vanilla JS with the DOM API (imperative style):

```js
const makeTextElement = (message) => {
  const p = document.createElement("p");
  p.className = "header";
  p.innerText = message;
  return p;
};
```

React (declarative style):

```jsx
const Text = ({ message }) => {
  return <p className="header">{message}</p>;
};
// This HTML-like syntax ^ is JSX
```

In React, we are able to return markup language (like HTML) from functions. This is called **JSX** (JavaScript XML).

### B. Component Composition is fast and easy to read

**Components** let you split the UI into independent, reusable pieces, and think about each piece in isolation.

**Component Composition** is the process of combining smaller, reusable components together to create larger, more complex components

Vanilla JS with the DOM API (imperative style):

```js
const makeCatInstaElement = () => {
  const container = document.createElement("div");
  container.className = "insta-pic";

  const caption = document.createElement("p");
  caption.className = "caption";
  caption.innerText = "cute cat pics";

  const img = document.createElement("img");
  img.src = "img/cat.jpeg";

  container.append(img, caption);
  return container;
};
```

React (declarative style):

```jsx
const Picture = ({ src }) => {
  return <img src={src} />;
};

const Caption = ({ text }) => {
  return <figcaption className="caption">{text}</figcaption>;
};

const InstagramPost = () => {
  return (
    <figure className="insta-pic">
      <Picture src="./images/my-cat.jpg" />
      <Caption text="cute cat" />
    </figure>
  );
};
```

Notice that `<Caption />` and `<Picture />` start with a capital letter. That’s how you know it’s a React **component**.

React component names must always start with a capital letter, while HTML tags must be lowercase.

### C. The Virtual DOM offers some performance benefits when re-rendering components

[Read more about it here](https://blog.logrocket.com/virtual-dom-react/#comparison-chart-real-virtual-shadow-dom)

## Starting a React Project (with Vite)

React projects are essentially the same as the Vanilla JS projects you've been building so far. At the end of the day, they start with an `index.html` file that runs some JavaScript files.

Vite provides a really great starting template for us to build React projects with:

```bash
npm create vite@latest
# Choose Project Name
? Project name: <name of your project>
# select React
# select JavaScript

cd <name of your project> && npm i
npm run dev
```

By default you will be given the familiar counter app. Take a look around! Every React project made by Vite will have this rough structure:

* `index.html` — the HTML file served to the client. It loads `src/main.jsx`.
* `src/main.jsx` — the entry point of the app. It uses the `react-dom/client` package to render the **root component** `App` into the DOM.
* `src/App.jsx` — the root component.
* `package.json` — note that a React vite project has a few React-related dependencies that the Vanilla project does not have.

> Notice the `.jsx` extension? Without it, we wouldn't be able to write JSX in this file.

### Rendering the Root Component With ReactDOM

How does all of this actually get to the screen? Head over to the `main.jsx` file.

The primary purpose of this file is render the root component `App`. To do so we:

* Import a package called `ReactDOM`
* Use the `ReactDOM.createRoot` method to create a `root` object.
* Then we call `root.render` and pass in the `App` component.

This is mostly handled when Vite creates the project for you so no need to memorize it:

```jsx
// main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

A few notes:

* We're using the `client` version of `ReactDOM` (there is also a `native` version for mobile).
* `React.StrictMode` is a wrapper-component that detects potential React-related in our application. It doesn't render anything visible.

### Components and JSX

React components are functions that return a single JSX element.

The first component that we create is typically called `App`

```jsx
// inside App.jsx
const App = () => {
  return (
    <header>
      <h1>Hello World</h1>
      <p>It's a great day</p>
    </header>
  )
}

export default App
```

* Component are functions that return JSX.
* Note the capitalized name. All components must use PascalCasing.
* Components must return a single surrounding element. Here, we return a `header`.
* When returning multiple elements, wrap the elements parentheses `()`.

### Nested Components

React is at its best when we separate the UI into individual components and then combine them to create the entire UI.

To render a component inside another component, we use the name of the component as if it were a self-closing HTML tag: `<ComponentName />`

```jsx
const Header = () => {
  return <h1>My Pet Pics</h1>;
};

const InstagramPost = () => {
  return (
    <figure>
      <img alt="Reggie the cat" src="images/cat.jpeg" />
      <figcaption>Check out my cute cat Reggie!</figcaption>
    </figure>
  );
};

const App = () => {
  return (
    <> 
      <Header />
      <InstagramPost />
    </>
  );
};

export default App
```

**Q: Compare and Contrast how each of these components return their children. What do you notice?**

<details>

<summary>Answer</summary>

* `InstagramPost` and `App` each return more than one line of JSX so the returned value is wrapped in `()`
* The `App` component uses fragments (`<>`) to wrap its child elements while `InstagramPost` uses a `<figure>`. Fragments let you group elements without a wrapper node. It is the same as if the elements were not grouped.

</details>

### Adding Classes and Style

Imagine we had this style rule defined in a CSS file:

```css
.red {
  color: red;
}
```

We can add style by using the `className` attribute. Note that we aren't using `class`. In React, many of the HTML attributes that we've grown accustomed to will have _slightly_ different names.

```jsx
const Message = () => {
  return <p className="red">Hello World!</p>;
};

const Messages = () => {
  return (
    <div>
      <Message />
      <Message />
      <Message />
    </div>
  );
};

const App = () => {
  return (
    <>
      <Header />
      <NameHeader />
      <Messages />
      <InstagramPost />
    </>
  );
};
```

<details>

<summary><strong>Q: What will this render?</strong></summary>

<img src="img/message-example.png" alt="" data-size="original">

Note how the `className` attribute in JSX is converted into the HTML attribute `class`.

We can't use the name `class` for this attribute because it is a reserved keyword in JavaScript.

The `for` attribute for `<label>` elements is another example of this. Instead, we use the `htmlFor` attribute.

</details>

<details>

<summary><strong>Q: How can I add a <code>class="insta-pic"</code> attribute to the <code>img</code> in my <code>InstagramPost</code> component?</strong></summary>

```jsx
const InstagramPost = () => {
  return (
    <figure >
      <img alt="Reggie the cat" src="images/cat.jpeg" className="insta-pic"/>
      <figcaption>Check out my cute cat Reggie!</figcaption>
    </figure>
  );
};
```

</details>

## Rendering Data with React

One of the most essential and useful features of React and JSX is how easy it is to utilize data to render components.

We can insert any JavaScript expression into our JSX using curly braces `{}`.

```jsx
const catPicture = {
  alt: "Reggie the cat",
  src: "images/cat.jpeg",
  caption: "Check out my cute cat Reggie!"
}

const InstagramPost = () => {
  return (
    <figure>
      <img 
        src={catPicture.src} 
        alt={`Photo of ${catPicture.alt}`}  
        className="insta-pic" 
      />
      <figcaption style={{ fontStyle: 'italic' }}>{catPicture.caption}</figcaption>
    </figure>
  );
};
```

Notice how the `alt` attribute uses string concatenation!

In the above example, `style={{}}` is not a special syntax, but a regular `{}` object inside the `style={ }` JSX curly braces. You can use the `style` attribute when your styles depend on JavaScript variables.

### Sharing Data Between Components With Props

What makes React so powerful is the ability to share data between components using **props**.

Every React function-component is passed an argument called `props`. It is an object containing properties provided to the component by the parent.

In this example, the parent component is `App` and it provides a `pictureData` prop to each instance of the `InstagramPost` component.

```jsx
const catPicture = {
  alt: "Reggie the cat",
  src: "images/cat.jpeg",
  caption: "Check out my cute cat Reggie!"
}
const dogPicture = {
  alt: "Robert the dog",
  src: "images/dog.jpeg",
  caption: "Check out my cute dog Robert!"
}
const duckPicture = {
  alt: "Daffy the Duck",
  src: "images/duck.jpeg",
  caption: "Check out my cute duck Daffy!"
}

const InstagramPost = ({ pictureData }) => {
  return (
    <figure>
      <img 
        src={pictureData.src} 
        alt={`Photo of ${pictureData.alt}`}  
        className="insta-pic" 
      />
      <figcaption style={{ fontStyle: 'italic' }}>{pictureData.caption}</figcaption>
    </figure>
  );
};

// Root component
function App() {
  // Component composition
  return (
    <>
      <h1>My Pet Pics</h1>
      <InstagramPost pictureData={catPicture} />
      <InstagramPost pictureData={dogPicture} />
      <InstagramPost pictureData={duckPicture} />
    </>
  )
}
```

**Q: What will this render?**

### Rendering A List of Elements

* Use array to store data
* Render `{array.map}`
* Give each element a `key` that should be unique (using the index of the array is okay but not ideal)

```jsx
const InstagramPost = ({ pictureData }) => {
  /* ... */
};

// Array of data
const pictures = [
  {
    id: 1,
    alt: "Reggie the cat",
    src: "images/cat.jpeg",
    caption: "Check out my cute cat Reggie!"
  },
  {
    id: 2,
    alt: "Robert the dog",
    src: "images/dog.jpeg",
    caption: "Check out my cute dog Robert!"
  },
  {
    id: 3,
    alt: "Daffy the Duck",
    src: "images/duck.jpeg",
    caption: "Check out my cute duck Daffy!"
  }
]

// Render the array in a ul
const PicturesList = () => {
  return (
    <ul>
      {
        pictures.map((picture) => {
          return (
            <InstagramPost key={picture.id} pictureData={picture} />
          );
        })
      }
    </ul>
  );
};

const App = () => {
  return (
    <>
      <Header />
      <PicturesList />
    </>
  );
};
```

## Under the hood: JSX Code must be compiled

JSX in our code (`<h1>...</h1>`) cannot simply be executed by our browser. It must first be **compiled** (converted) to vanilla JS.

<details>

<summary>See what this code would look like if it were written without JSX</summary>

Note how we have to use `React.createElement` here

```js
const rootEl = document.getElementById("root");
const root = ReactDOM.createRoot(rootEl);

root.render(React.createElement("h1", {}, "Hello World"));
```

</details>

Vite is doing the heavy lifting when it comes to the rendering.
