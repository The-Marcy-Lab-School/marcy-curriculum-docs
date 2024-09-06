# React Cheatsheet

React is a library for building reusable, composable, and scalable user-interfaces made up of components.

- [Components, JSX, Props](#components-jsx-props)
- [Rendering a List](#rendering-a-list)
- [State, Event Handlers and Lifting State](#state-event-handlers-and-lifting-state)
- [Controlled Form](#controlled-form)
- [Fetching with useEffect](#fetching-with-useeffect)

## Components, JSX, Props

- **React** — a library for building reusable, composable, and scalable user-interfaces made up of components.
- **Component** — a piece of the UI (user interface) that has its own logic and appearance. Components are functions that return JSX.
- **JSX** — an extension of JavaScript that lets you write HTML in React components.
- **Component Composition** — the process of combining smaller, reusable components together to create larger, more complex components
- **Root Component** — the top-level component that all other components are children of. Typically called `App`.
- **`react-dom/client`** — a React package that lets you render React components on the client (in the browser)
- **Prop** — a piece of data passed from a parent component to a child component.

```jsx
const Friend = ({friendName}) => {
  return (
    <p>My friend is {friendName}</p>
  )
}

function App () {
  return (
    <>
      <h1>My App</h1>
      <Friend friendName='gonzalo'/>
      <Friend friendName='maya'/>
      <Friend friendName='reuben'/>
    </>
  )
}
```

## Rendering a List

* When we have an array that we want to render, use a `ul`
* We use the `thingsToRender?.map` syntax to only map over the array if it is defined
* Use `.map` to convert each element in the array to a `li`
* Each `li` should have a unique `key` value (often the id of the element)
* The `li` can contain other elements or components.

```jsx
function List({ thingsToRender }) {
  return (
    <ul>
      {
        thingsToRender?.map((thing) => (
          <li key={thing.id}>
            <p>{thing.information}</p>
          </li>
        ))
      }
    </ul>
  )
}
```

## State, Event Handlers and Lifting State

- **State** — Data that is used by an application at a particular point in time. State is often mutable, meaning it can be changed over time, usually in response to user actions or other events
- **Stateful Component** — A component that depends on state and is re-rendered whenever the state changes.
- **Hooks** — Functions that provide a wide variety of features for React components. They all begin with `use()`.
- **`useState`** – A react hook for managing state within a React component. It returns an array with a state value and a setter function. It triggers the component to re-render when the state changes.
- **Lifting state up** — A practice where state is defined in a parent component so that it can be used by its child components.

```jsx
// useState is imported at the top
import { useState } from 'react';

// component names use PascalCase
const CounterDisplay = ({ count }) => {
  return <p>{count}</p>
}

const CounterButtons = ({ increment, decrement }) => {
  // props are destructured ^          ^
  
  // multiple returned components are wrapped with () and <> </>
  return (
    <>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </>
  )
}

// App is exported (default or named is fine)
export const App = () => {
  // state is "lifted up" and passed down with props
  const [count, setCount] = useState(0);

  // helper functions can be passed down instead of the setter function itself
  const increment = () => { setCount(count + 1) }
  const decrement = () => { setCount(count - 1) }

  return (
    <>
      <CounterDisplay count={count} />
      <CounterButtons increment={increment} decrement={decrement} />
    </>
  )
}
```

When the child component changes the `count` state, the `App` component that "owns" that state will re-render.

When the parent component `App` re-renders, so do all of the children (`CounterDisplay` and `CounterButtons`)

## Controlled Form

A **controlled form** is a form element whose input values are controlled by React state rather than through DOM manipulation. 

```jsx
const NewPetForm = () => {
  const [src, setSrc] = useState('');
  const [caption, setCaption] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();

    // do something with the values
    console.log(src, caption);

    // reset the form
    setSrc('');
    setCaption('');
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="src-input">Image Source:</label>
      <input 
        type="text" 
        name="src" 
        id="src-input" 
        value={src} 
        onChange={(e) => setSrc(e.target.value)} 
      />
      <label htmlFor="caption-input">Caption:</label>
      <input 
        type="text" 
        name="caption" 
        id="caption-input" 
        value={caption} 
        onChange={(e) => setCaption(e.target.value)} 
      />
      <button>Submit</button>
    </form>
  )
}
```

* Notice how each input has a `value` and an `onChange` prop associated with a particular piece of state.
* When it is time to submit the form, we can easily use the `src` and `caption` state values without digging through the form.
* At the end of submitting the form, we reset the form by resetting the state.

## Fetching with useEffect

- **Side effect** — Anything that happens outside of React such sending a `fetch` request, starting an animation, or setting up a server connection. 
  - Side effects can be triggered by user events like submitting a form or clicking a button.
- **`useEffect`** – A react hook for executing "side effects" caused by a component rendering, not a particular event.
- **Dependency Array** — The array of values provided to `useEffect` that React will watch for changes. If changes occur in the dependency array, the effect will run again.
- **Conditional Rendering** — Rendering different JSX depending on the current state. This can be useful when fetching to show either the fetched data or an error message if the fetch failed.

```jsx
const DataComponent = () => {
  const [data, setData] = useState([]);
  const [error, setError] = useState(null)

  useEffect(() => {
    const doFetch = async () => {
      const [data, error] = await fetchData('http://someAPI');
      if (data) setData(data);
      if (error) setError(error);
    }
    doFetch();
  }, []); // empty array will run only once

  // conditionally return the error message
  if (error) return <p>{error.message}</p>

  // render the data
  return (
    <div>
      <p>{data}</p>
    </div>
  )
}
```

* Remember to create state for the data and the error
* We CANNOT use an `async` callback with useEffect
* We have to define an `async` function within the useEffect callback and then invoke it
* The second argument to `useEffect` is the dependency array:
  * `[]` - An empty array means the effect will run only on the first render
  * `[a, b, c]` - Adding variables to the array will trigger the effect to run when those variables change
  * No array provided will trigger the effect to run EVERY time the component re-renders
