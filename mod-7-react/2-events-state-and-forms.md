# 2. Events, State, and Forms

{% hint style="info" %}
Follow along with code examples in the lecture repo!
{% endhint %}

In this lesson, we will look at how to respond to events in React and use those events to manage the ever-changing state in our application.

**Table of Contents**

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Local Todo List](#local-todo-list)
- [Handling Changing State](#handling-changing-state)
  - [Our Components Already Render Data](#our-components-already-render-data)
  - [Changing A Variable In Reaction to Events](#changing-a-variable-in-reaction-to-events)
- [`useState`](#usestate)
  - [Invoke useState at the top of your component](#invoke-usestate-at-the-top-of-your-component)
  - [Use the setter function to update the state](#use-the-setter-function-to-update-the-state)
  - [All Together Now](#all-together-now)
- [Forms](#forms)
  - [Controlled Forms](#controlled-forms)
- [Discussion! Lifting State Up](#discussion-lifting-state-up)
- [Conditional Rendering](#conditional-rendering)
  - [Ternary Operator](#ternary-operator)
  - [Short-Circuit &&](#short-circuit-)

## Essential Questions

By the end of this lesson you should be able to answer:

1. What is state, and why can't a regular variable be used to track data that changes in a component?
2. What does `useState` return, and how do you use each of the two returned values?
3. What is a controlled form, and how does it differ from reading values directly from the DOM?
4. What does "lifting state up" mean, and when is it necessary?
5. What is conditional rendering, and what are the two common JSX patterns for it?

## Key Concepts

* **State** — Data that is used by an application at a particular point in time. State is often mutable, meaning it can be changed over time, usually in response to user actions or other events
* **Stateful Component** — A component that depends on state and is re-rendered whenever the state changes.
* **Hooks** — Functions that provide a wide variety of features for React components. They all begin with `use()`.
* **`useState`** – A react hook for managing state within a React component. It returns an array with a state value and a setter function. It triggers the component to re-render when the state changes.
* **Lifting state up** — A practice where state is defined in a parent component so that it can be used by its child components.
* **Controlled Form** — A form whose value changes are controlled by a piece of state.
* **Conditional Rendering** — Rendering different JSX depending on the current state or props.

## Local Todo List

In this lesson, we'll build a **local Todo list** to demonstrate **stateful components**. A stateful component is one that depends on state and re-renders whenever the state changes.

**State** is the data that is used by an application at a particular point in time. State is often mutable, meaning it can change over time in response to user actions.

Our todo list will start with a static array of todos and grow to support:
- Toggling a todo between complete and incomplete
- Adding new todos via a form

Right now the app is not stateful — it renders hard-coded todo items and neither the toggle buttons nor the form work yet.

> **TODO: Add screenshot of static Todo list UI here**

Let's build this thing!

## Handling Changing State

Let's tackle the toggle buttons first.

### Our Components Already Render Data

Each `TodoItem` component renders a todo's title and a button to toggle it complete.

> Notice how we added an `onClick` prop with the `handleClick` callback function.

```jsx
const TodoItem = ({ todo }) => {

  let isComplete = false;

  const handleClick = () => {
    
  }

  return (
    <li className="todo-item">
      <span>{todo.title}</span>
      <button onClick={handleClick}>
        {isComplete ? '✅' : '⬜'}
      </button>
    </li>
  );
};

export default TodoItem;
```

A stateful component is one that renders state — data values that may change.

**<details><summary>Q: What data values does this component render? Is any of that considered "state"?</summary>**

`isComplete` and `todo.title`. These values are not considered state because they are hard-coded or passed in as props! They will not change in response to user actions.

</details>

### Changing A Variable In Reaction to Events

Let's make `isComplete` a piece of mutable state.

We want to toggle `isComplete` each time we click on the button. So, maybe this will work?

```jsx
const TodoItem = ({ todo }) => {
  console.log('rendering TodoItem');
  
  let isComplete = false;

  const handleClick = () => {
    isComplete = !isComplete;
    console.log(isComplete);
  }

  return (
    <li className="todo-item">
      <span>{todo.title}</span>
      <button onClick={handleClick}>
        {isComplete ? '✅' : '⬜'}
      </button>
    </li>
  );
};
```

While this _does_ flip the `isComplete` value, it doesn't cause the component to re-render because React isn't watching this value for changes.

## `useState`

So how do we make the component re-render with the updated `isComplete` value?

We need a **hook**. Hooks in react are functions that perform a variety of jobs. They can be identified by their name which starts with "use":

* `useState()`
* `useEffect()`
* `useNavigate()`
* `useParams()`
* `useContext()`
* etc...

The `useState` hook allows us to create a piece of state that React will watch and when the state changes, it will re-render.

Here's how it works:

### Invoke useState at the top of your component

Import `useState` as a named export from `react`, then call it at the top of your component:

```jsx
// TodoItem.jsx
import { useState } from "react";

const TodoItem = ({ todo }) => {
  const [isComplete, setIsComplete] = useState(false);

  // handleClick and the return statement
};
```

* `useState` _must_ be called at the top of a component. [Otherwise weird stuff happens](https://legacy.reactjs.org/docs/hooks-rules.html).
* `useState(false)` returns an array with two values:
  1. A piece of state data (`isComplete`) with a starting value (`false`)
  2. A "setter" function for updating that state data and re-rendering the component
* The convention is to name state variables like `[something, setSomething]` using array destructuring.

### Use the setter function to update the state

```jsx
const handleClick = () => {
  setIsComplete(!isComplete);           // this is OK
  setIsComplete((current) => !current); // this is better when next value depends on current
  isComplete = !isComplete;             // Don't do this
};
```

* When the button is clicked, we invoke `setIsComplete` which either accepts:
  * the new value we want to set `isComplete` to, or...
  * a callback function for turning the current value into the next value.
* As we saw, mutating `isComplete` directly does not cause the component to re-render.
* `setIsComplete` will cause the component to re-render with the provided value as the new `isComplete`.

### All Together Now

```jsx
import { useState } from 'react';

const TodoItem = ({ todo }) => {
  const [isComplete, setIsComplete] = useState(false);

  const handleClick = () => {
    setIsComplete((current) => !current);
  }

  return (
    <li className="todo-item">
      <span>{todo.title}</span>
      <button onClick={handleClick}>
        {isComplete ? '✅' : '⬜'}
      </button>
    </li>
  );
};
```

**Quiz!**

* Why did we pass in `false` when we invoked `useState`?
* What does `useState()` return?
* What does `setIsComplete()` do? What kinds of inputs does it take?

{% hint style="warning" %}
`setIsComplete` does NOT change `isComplete` within the current function call — it schedules a re-render with the new value. Add `console.log(isComplete)` inside `handleClick` to see this: the logged value is still the old one. `setIsComplete` tells React what the next value _should_ be; the variable only updates on the next render.
{% endhint %}

## Forms

Next up we'll make a form for the user to add new todos.

Creating a form using JSX in React is almost identical to creating a form using HTML. Take a look at `AddTodoForm`:

```jsx
const AddTodoForm = () => {
  const handleSubmit = (e) => {
    e.preventDefault();
  }

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="title-input">Todo:</label>
      <input type="text" name="title" id="title-input" />
      <button>Add</button>
    </form>
  )
}
```

* Instead of `for` we use `htmlFor` when connecting labels and inputs.
* We use `onSubmit` instead of using `addEventListener`.

Now, how do we handle the submission event?

### Controlled Forms

A **controlled form** is a form element whose input values are controlled by React state rather than through DOM manipulation.

To create a controlled form, we will:

1. Create a piece of state for each input we want to control
2. Assign the `value` prop of the input to the input state value we just created
3. Assign an `onChange` handler to the input that invokes the state setter function
4. When handling submissions, we can simply reference the input state values.
5. Remember to reset the state values after submission.

```jsx
const AddTodoForm = () => {

  // 1. Create a piece of state for each input we want to control
  const [title, setTitle] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();

    // 4. When handling submissions, we can simply reference the input state values.
    console.log(title);

    // 5. Remember to reset the state values after submission.
    setTitle('');
  }

  // 2. Assign the `value` prop of the input to the input state value we just created
  // 3. Assign an `onChange` handler to the input that invokes the state setter function
  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="title-input">Todo:</label>
      <input 
        type="text" 
        name="title" 
        id="title-input" 
        value={title} 
        onChange={(e) => setTitle(e.target.value)} 
      />
      <button>Add</button>
    </form>
  )
}
```

* Notice how each input has a `value` and an `onChange` prop associated with a particular piece of state.
* When it is time to submit the form, we can easily use the `title` state value without digging through the form.

## Discussion! Lifting State Up

The last step to putting this together is having the form submission actually add a new todo to the list.

Here is the component tree of the application:

```
App
├── TodoList
│   └── TodoItem (× n)
└── AddTodoForm
```

> **TODO: Add component tree diagram image here**

The challenge is that `TodoList` is where the `todos` are rendered but we want to update the list from `AddTodoForm`.

If we were to turn the `todos` array into some state like this:

```jsx
const [todos, setTodos] = useState(initialTodos);
```

**<details><summary>Q: Where should I put this? Why?</summary>**

The state should be defined in `App`, which is the closest shared ancestor of both `AddTodoForm` and `TodoList`. `App` can then pass the values down to its children as props. This is called **"lifting state up"**.

* `App` uses `useState` to define the `todos` and `setTodos` values
* It passes down `todos` to `TodoList`
* It makes an `addTodo` helper function and passes it down to `AddTodoForm` to invoke upon submission

```jsx
const App = () => {
  const [todos, setTodos] = useState([
    { id: 1, title: 'Buy groceries', isComplete: false },
    { id: 2, title: 'Walk the dog', isComplete: false },
  ]);

  const addTodo = (title) => {
    const newTodo = { id: Date.now(), title, isComplete: false };
    setTodos((current) => [...current, newTodo]);
  };

  return (
    <>
      <h1>My Todos</h1>
      <TodoList todos={todos} />
      <AddTodoForm onAddTodo={addTodo} />
    </>
  );
};
```

Notice how `addTodo` sets the state by spreading the existing array and adding a new object — never mutating the array directly.

</details>

## Conditional Rendering

Often we want to show different UI based on the current state. React makes this easy with two common patterns.

### Ternary Operator

Use a **ternary** when you want to render one thing _or_ another:

```jsx
condition ? <ComponentA /> : <ComponentB />
```

We already have `isComplete` state in `TodoItem`. Use it to change the button label and apply a strikethrough style:

```jsx
const TodoItem = ({ todo }) => {
  const [isComplete, setIsComplete] = useState(false);

  return (
    <li>
      <span style={{ textDecoration: isComplete ? 'line-through' : 'none' }}>
        {todo.title}
      </span>
      <button onClick={() => setIsComplete((c) => !c)}>
        {isComplete ? 'Mark Incomplete' : 'Mark Complete'}
      </button>
    </li>
  );
};
```

### Short-Circuit &&

Use **short-circuit** when you want to render something _only if_ a condition is true and render nothing otherwise:

```jsx
condition && <Component />
```

A practical example — only show the "Delete" button when the user owns the todo (we'll have a `currentUser` in a later lesson):

```jsx
const TodoItem = ({ todo, currentUser }) => {
  return (
    <li>
      <span>{todo.title}</span>
      {currentUser && (
        <button onClick={() => deleteTodo(todo.id)}>Delete</button>
      )}
    </li>
  );
};
```

If `currentUser` is `null` or `undefined`, the button is simply not rendered. No need for an `if/else`.
