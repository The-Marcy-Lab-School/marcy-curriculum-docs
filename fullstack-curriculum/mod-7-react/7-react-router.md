# React Router

**Table of Contents**:

- [Terms](#terms)
- [React Router Basics](#react-router-basics)
- [Using React Router](#using-react-router)
  - [0) Install the package `react-router-dom`](#0-install-the-package-react-router-dom)
  - [1) Render a `<BrowserRouter>`](#1-render-a-browserrouter)
  - [2) Create your `<Routes>` and `<Route>`s](#2-create-your-routes-and-routes)
  - [3) Create `<Link>`s](#3-create-links)
- [Fallback Component](#fallback-component)
- [Creating a Product Page](#creating-a-product-page)
  - [I want to render a page to display a single product...](#i-want-to-render-a-page-to-display-a-single-product)
  - [Path Parameters](#path-parameters)
  - [`useParams`](#useparams)
  - [Rendering a dynamic list of links](#rendering-a-dynamic-list-of-links)
- [Route Specificity](#route-specificity)
- [Nesting Routes](#nesting-routes)

[React docs](https://reactrouter.com/en/main/router-components/browser-router)
[Full Example](https://github.com/benspector-mls/react-router-with-fetch-and-context)

## Terms

- **React Router** - Enables navigation and routing in single-page applications (SPAs). It allows you to define routes and their corresponding components.

- **React Router DOM** — Provides components like `BrowserRouter`, `Route`, and `Link` to manage routing and navigation.

- **`<BrowserRouter>`** — It ensures that when users visit different URLs in your app, the right components are rendered based on those URLs.

- **`<Route>`** — Like a signpost that tells your app which page to show when you visit a certain web address.

- **`<Link>`** - Special navigation buttons that you can use to move between different pages or views in your web application.

- **Fallback Component** — A component used to render content when no other routes match the current path.

- **Path Parameters** - a dynamic component of a `Route` path, identified using `:pathParam`. For example, in the path `/products/:productName`, the path parameter is `:productName` and will match any URL with that pattern including `/products/iPhone`, `/products/foo` or even `/products/5` but NOT `/products/iPhone/details` 
  
- **`useParams`** - a React hook that returns an object with key:value pairs for each path parameter in the URL.

## React Router Basics

React Router is a package for handling **client-side routing** in a React application and lets us simulate navigation between different "pages" in our application.

> **What do we mean by "simulate navigation"?**

Normally, when we click on a link like...

```html
<a href="./home.html">Home</a>
```

...we are redirected to a separate HTML page that our browser needs to fetch from the server.

With React Router, we can do the same kind of navigation, _but we actually never leave the page_. **Instead of opening a separate HTML file, the same HTML file is used, but we just render different components to the screen.**

![gif of react router](./img/react-router-demo.gif)

😮 _oooooh so smooth_...

## Using React Router

### 0) Install the package `react-router-dom`

React Router is not provided by default in Vite projects so we have to install it.

```sh
npm i react-router-dom
```

_There is also a `react-router-native` version for building mobile React apps with React Router_

### 1) Render a `<BrowserRouter>`

We're going to be importing a lot of things from the `react-router-dom` package. The first is the `BrowserRouter` component.

<!-- prettier-ignore -->
```jsx
import ReactDOM from 'react-dom/client'
import App from './App.jsx'

// import BrowserRouter
import { BrowserRouter } from 'react-router-dom'; 

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter> {/* wrap the entire App */}
    <App />
  </BrowserRouter>
)
```

The `BrowserRouter` component hijacks the URL bar, letting us:

- render what we want to render based on the current URL (the "browser location")
- use back-and-forward navigation
- directly update the current browser location

### 2) Create your `<Routes>` and `<Route>`s

Whenever the location changes, `<Routes>` looks through all its child `<Route>`s to find the best matching `path` and renders the provided `element`.

```jsx
import { Routes, Route } from "react-router-dom";

const Dashboard = () => <h1>Dashboard</h1>;
const About = () => <h1>About</h1>;
const Products = () => <h1>Products</h1>;

function App() {
  return (
    <>
      <Routes>
        <Route path="/" element={<Dashboard />}></Route>
        <Route path="/about" element={<About />}></Route>
        <Route path="/products" element={<Products />}></Route>
      </Routes>
    </>
  );
}
```

### 3) Create `<Link>`s

The `<Link>` component will replace the `<a>` tags in our HTML.

- Like the `<a>` tag, the `<Link>` will provide a clickable button to redirect to another page.
- Unlike the `<a>` tag, the `<Link>` will NOT actually navigate to a different file, causing a fetch. Instead, only the URL is changed, causing the `<Routes>` to render a new `<Route>`, but we stay on the same "page".

<!-- prettier-ignore -->
```jsx
function App() {
  return (
    <>
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/products">Products</Link></li>
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/about" element={<About />} />
        <Route path="/products" element={<Products />} />
      </Routes>
    </>
  )
}
```

## Fallback Component

Suppose I had this component that I wanted to render when a URL was entered that didn't match _any_ of the routes I defined:

```jsx
const NotFound = () => <h1>Not Found</h1>;
```

This can easily be accomplished using the `"*"` catch-all route. If none of the previous `<Route>` had a matching `path`, then this `"*"` will match.

```jsx
<Routes>
  <Route path="/" element={<Dashboard />} />
  <Route path="/about" element={<About />} />
  <Route path="/products" element={<Products />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

## Creating a Product Page

### I want to render a page to display a single product...

Suppose I had an array of products that I wanted to show to the user.

```js
const products = [
  { id: 1, name: 'apple', price: 1 },
  { id: 2, name: 'banana', price: 0.5 },
  { id: 3, name: 'cherries', price: 3 },
]
```

One solution would be to create a separate `Route` and page component for each of these products:

```jsx
<Route path="/products/apple" element={<Apple />} />
<Route path="/products/banana" element={<Banana />} />
<Route path="/products/cherries" element={<Cherries />} />
```

**Q: What is the issue with this approach?**

### Path Parameters

In React Router, the strategy for achieving this is using **path parameters**.

A **path parameter** is a dynamic portion of a route path, indicated by a colon `:`. 

```jsx
<Route path="/products/:productName" element={<Product />} />
```

In the example above, we use the path parameter `:productName`. The `:productName` value can be replaced by anything and as a result, the `Product` page component will render.

For example, what is the value of the `:productName` path parameter for the following URLs?
* `/products/5`
* `/products/hello`
* `/products/banana`

### `useParams`

Okay so the `Product` page component will render for `/products/apple` and `/products/banana` and `/products/cherries`, but how does it know which product to show?

The `useParams()` hook from `react-router-dom` returns an object with key:value pairs that correspond to the path parameters in the current URL.

```js
const Product = () => {
  const params = useParams();

  // if the URL is /products/apple then
  // params = { productName: 'apple' }
}
```

Below, you can see how we use `productName` param value to find the product from the array of products.

```jsx
import { useParams } from "react-router-dom";

// ideally, this data would come from props or context so it could be shared by the `Products` and the `Product` page components.
const products = [
  { id: 1, name: 'apple', price: 1 },
  { id: 2, name: 'banana', price: 0.5 },
  { id: 3, name: 'cherries', price: 3 },
]

const Product = () => {
  // immediately destructure the params object
  const { productName } = useParams();

  const product = product.find((product) => product.name === productName);

  return (
    <>
      <h1>Product Page</h1>
      <p>{product.name} — ${product.price}</p>
    </>
  )
};
```

### Rendering a dynamic list of links

Finally, the `Products` page component should show a list of links that let me select which product I want to view.

This is often done by generating a `Link` for each value in the dataset:

<!-- prettier-ignore -->
```jsx
// ideally, this data would come from props or context so it could be shared by the `Products` and the `Product` page components.
const products = [
  { id: 1, name: 'apple', price: 1 },
  { id: 2, name: 'banana', price: 0.5 },
  { id: 3, name: 'cherries', price: 3 },
]

const Products = () => {
  return (
    <>
      <h1>Products</h1>
      <ul>
        {
          products.map((product) => {
            return (
              <li>
                <Link to={`/products/${product.name}`}>{product.name}</Link>
              </li>
            )
          })
        }
      </ul>
    </>
  )
}
```

## Route Specificity

**Q: Which of the following paths will match the `/products/:id` path?**

- /products
- /products/apple
- /products/5
- /products/5/about

Suppose I wanted to create a page where a user could create a new product. Let's say the path is `/products/create`:

```jsx
<Routes>
  {/* Other routes... */}
  <Route path="/products" element={<Products />} />
  <Route path="/products/:id" element={<Product />} />
  <Route path="/products/create" element={<CreateProduct />} />
</Routes>
```

Even though the `/create` portion of the last `<Route>` would match the `:id`, **React Router is smart enough to know that the `/products/create` path is hard-coded and therefore more specific** than the dynamic path `/products/:id` so it properly redirects us.

## Nesting Routes

Currently, I'm defining all of the `/products` routes by providing the full path.

```jsx
<Routes>
  <Route path="/" element={<Dashboard />} />
  <Route path="/about" element={<About />} />
  <Route path="/products" element={<Products />} />
  <Route path="/products/:id" element={<Product />} />
  <Route path="/products/create" element={<CreateProduct />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

We can nest `<Route>`s to create sections of our application that share the same base path.

```jsx
<Routes>
  <Route path="/" element={<Dashboard />} />
  <Route path="/about" element={<About />} />
  <Route path="/products">
    <Route index element={<Products />} />
    <Route path=":id" element={<Product />} />
    <Route path="create" element={<CreateProduct />} />
  </Route>
  <Route path="*" element={<NotFound />} />
</Routes>
```

- We wrap our three `<Route>`s in an outer `<Route>` whose `path` is the base path that the child `<Route>`s use.
- For the first `<Route>` which renders the `<Products />` component, we remove the `path` entirely and instead use `index` which will match with the base path (`/products`) provided in the parent `<Route>`
- For each child, we can remove the `/products/` part of the `path`
