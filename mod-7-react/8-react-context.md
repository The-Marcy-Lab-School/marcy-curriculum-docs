# 6. React Context

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/7-1-0-react-context)!
{% endhint %}

**Table of Contents**:

* [Terms](6-react-context.md#terms)
* [Problem: Props Drilling](6-react-context.md#problem-props-drilling)
  * [Passing State From App to LikesButton](6-react-context.md#passing-state-from-app-to-likesbutton)
* [Solution: useContext](6-react-context.md#solution-usecontext)
  * [1. Create a context object](6-react-context.md#1-create-a-context-object)
  * [2. Render a Context Provider](6-react-context.md#2-render-a-context-provider)
  * [3. Use the Context](6-react-context.md#3-use-the-context)
* [Summary](6-react-context.md#summary)

## Key Concepts

* **Context (English Definition)** — The circumstances that form the setting for an event, statement, or idea, and in terms of which it can be fully understood and assessed.
* **Props Drilling** - When we pass a piece of state through 2 or more child components before reaching the component that uses the state.
* **React Context** — the combination of:
  1. an object where we can store global data and
  2. a "wrapper" component that provides its data to all descendants
* **`createContext`** — A function exported from `react` that creates a new `Context` object.
* **`Context.Provider`** — A component created from a `Context` object that provides all of its descendants with access to the `Context` object's values.
* **`useContext`** — A React hook for retrieving the values of a `Context` object.

## Problem: Props Drilling

Consider the instagram clone app that renders something like this:

> Note how there is a **total tally** of likes at the top that sums the likes on each individual picture.

![](../.gitbook/assets/instapets-screenshot.png)

![](../.gitbook/assets/instacat-component-tree.svg)

### Passing State From App to LikesButton

To achieve the `totalLikes` feature, The `App` component defines a piece of state called `totalLikes` and a function `incrementTotalLikes` that invokes `setTotalLikes`.

```jsx
const App = () => {
  const [totalLikes, setTotalLikes] = useState(0);
  const incrementTotalLikes = () => {
    setTotalLikes((totalLikes) => totalLikes + 1);
  }

  return (
    <>
      <Header likes={totalLikes} />
      <PicturesList incrementTotalLikes={incrementTotalLikes} />
    </>
  );
};
```

However, the component that uses `incrementTotalLikes` is `LikesButton` which is 3 layers away from `App`.

* So we first pass `incrementTotalLikes` to `PicturesList`...
* Which passes it to each `InstagramPost` instance...
* Which passes it down to `LikesButton`...
* Which finally uses it.

```jsx
const LikesButton = ({ incrementTotalLikes }) => {
  const [likes, setLikes] = useState(0);

  const handleClick = () => {
    incrementTotalLikes();
    setLikes(likes + 1)
  }

  return (
    <div className="likes-container">
      <button onClick={handleClick}>❤️ {likes}</button>
    </div>
  )
}
```

This is called **props drilling** — when we pass a piece of state through 2 or more child components before reaching the component that uses the state.

It is okay to pass a piece of state through 1 intermediate component but passing state through 2 or more can start to feel tedious.

## Solution: useContext

The solution is to create something called a **Context**.

In plain english, "context" means:

> the circumstances that form the setting for an event, statement, or idea, and in terms of which it can be fully understood and assessed.

In React, Context is the combination of:

1. an object where we can store global data and
2. a "wrapper" component that provides its data to all descendants

![](../.gitbook/assets/instagram-context-diagram.svg)

To use React's Context API, there are **3 concepts to understand**.

* Making a Context object - `const Context = createContext(startingValue)`
* Rendering a Context Provider - `<Context.Provider values={values} />`
* Getting values from a Context - `useContext(Context)`

### 1. Create a context object

This is certainly the simplest step. It will almost always look like this:

```jsx
// src/context/InstagramContext.jsx
import { createContext } from "react";

const defaultValue = {}

const InstagramContext = createContext({});

export default InstagramContext;
```

* We create a new folder called `context/` in our `src` folder
* `createContext` is a named export of the `react` library
* We invoke `createContext` to create a new `InstagramContext` object which we export.
* It can take in a `defaultValue` argument. For now, we'll just use an empty object.

The `InstagramContext` object is the "glue" that makes context possible. We are going to use it to:

1. Wrap our app's components in a `<InstagramContext.Provider value={contextValues} />` component, providing all descendants with access to the `contextValues` that we choose.
2. Use the `useContext(InstagramContext)` hook to get access to the `contextValues` within a descendent component.

### 2. Render a Context Provider

`<InstagramContext.Provider values={contextValues}/>` is a React component that we wrap around a portion of our app that we want to have access to the `InstagramContext` (we can even wrap it around the entire `App` if we wanted to).

```jsx
import "./App.css";
import Header from "./components/Header";
import PicturesList from "./components/PicturesList";
import { useState } from "react";

// 1. Import the Context
import InstagramContext from "./context/InstagramContext";

const App = () => {
  const [totalLikes, setTotalLikes] = useState(0);
  const incrementTotalLikes = () => {
    setTotalLikes((totalLikes) => totalLikes + 1);
  }

  // 2. Create the context values
  const contextValues = { totalLikes, incrementTotalLikes }

  // 3. Wrap the components that use the context values in the Provider
  // 4. Set the `value` prop and remove the prop drilling
  return (
    <>
      <InstagramContext.Provider value={contextValues}>
        <Header />
        <PicturesList />
      </InstagramContext.Provider>
    </>
  );
};

export default App;
```

We first import the `InstagramContext` we just created.

* `InstagramContext.Provider` is a component that we can wrap around any piece of the application that we want to have access to the `InstagramContext`.
* The `value` prop of the `InstagramContext.Provider` determines the data available to the children of the `InstagramContext.Provider`
* Now, we can safely remove the `incrementTotalLikes` prop from the `PicturesList` and all intermediate components.
* We can also remove the `totalLikes` prop from the `Header`

### 3. Use the Context

Any component that is a descendant from a `InstagramContext.Provider` may utilize the `value` of that provider using the `useContext` hook from `react`:

```jsx
// 1. Import useContext and the InstagramContext
import { useState, useContext } from 'react';
import InstagramContext from "../context/InstagramContext";

const LikesButton = () => {
  const [likes, setLikes] = useState(0);

  // 2. Get the contextValues
  const contextValues = useContext(InstagramContext);

  const handleClick = () => {
    // 3. Use the contextValues
    contextValues.incrementTotalLikes();
    setLikes(likes + 1)
  }

  return (
    <div className="likes-container">
      <button onClick={handleClick}>❤️ {likes}</button>
    </div>
  )
}

export default LikesButton;
```

* `useContext` is imported from `react` alongside `useState`
* The `InstagramContext` itself is also imported. This will be needed when we invoke `useContext`
* `useContext` is invoked at the top of the `LikesButton` component. It takes in a `Context` object and returns the `value` prop of the associated `InstagramContext.Provider`.

> ⚠️ We can take this even further and use the Context for every value in the application. However, there is a delicate balance between storing TOO much in context and keeping the state close to the components that need it.

## Summary

Context provides an alternative to **props drilling**.

Think of Context as an object where we can store global data and that any component within that context's scope can access that data.

To use React's Context API, there are **3 concepts to understand**.

* Making a Context object - `const Context = createContext()`
* Rendering a Context Provider - `<Context.Provider values={} />`
* Getting values from a Context - `useContext()`

![](../.gitbook/assets/instagram-context-diagram.svg)
