In order to see how well you're doing with this project, here are all the things we need to see from you. If you get all of these, then you know that you're where you need to be!

**Table of Contents:**
- [Fetching Requirements](#fetching-requirements)
- [User Interface: Structure and Accessibility](#user-interface-structure-and-accessibility)
- [Meta](#meta)
- [Form Requirements](#form-requirements)

## Fetching Requirements

- [ ] The application makes two fetches:  
    - [ ] A fetch is made to an API that returns an array of data.  
    - [ ] A fetch is made to an API that returns a single data value  
- [ ] On page load, a fetch call is made, rendering initial fetch data to the screen  
- [ ] Asynchronous code uses `async` and `await`  
- [ ] Fetch calls are made using a `try` / `catch` block  
    - [ ] Errors are handled by `console.warn()`  
- [ ] `response.ok` is checked before parsing  
    - [ ] Throw a new error if the response is NOT ok

## User Interface: Structure and Accessibility

- [ ] The `head` has a `title`  
- [ ] There is a single `main` element on the page  
- [ ] There is a single `h1` element on the page  
- [ ] There is a `section` for the rendering an array of fetched data  
    - [ ] The section has an `h2`  
    - [ ] The section has a `ul` with `li` items that display the fetched data  
    - [ ] Fetched items appear next to each other in a grid-like pattern (flex or grid presentations fine)  
- [ ] There is a `section` for displaying a single fetched item  
    - [ ] The section has an `h2`  
    - [ ] The section displays information about the single fetched item  
- [ ] `section` elements have an `aria-label` or `aria-labelledby` attribute that describes the section   
- [ ] There are no instances of recreating any semantic elements (using a `div` for a `nav`)  
- [ ] The entire application is responsive with mobile and desktop compatibility (we recommend a mobile-first approach)

## Meta

- [ ] The project is created using Vite  
- [ ] The code exists in more than one JS file  
- [ ] The project is deployed via GitHub Pages properly  
- [ ] CSS Flexbox or Grid was used  
- [ ] The code does not render unescaped text directly to the DOM (`createElement` or other escape method used)

## Form Requirements

- [ ] There is a `form` for triggering a fetch or for modifying the fetch results  
    - [ ] `form` elements have an aria-label or aria-labelledby attribute that describes the form  
    - [ ] The form has an `h2`   
    - [ ] The form has at least one `input`  
    - [ ] Every `input` element has an associated `label` element (it can be invisible but it must be in the HTML)  
    - [ ] The form has a `button` to submit the form  
- [ ] At least one field is a required field, and the form cannot be submitted without it  
- [ ] Clicking the form submit button does not reload the page because the default behavior is prevented  
- [ ] Clicking the form submit button clears the form