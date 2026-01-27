# Handling Forms

**Table of Contents:**
- [Handling Form Submissions](#handling-form-submissions)
  - [Original Form Submissions](#original-form-submissions)
  - [The Modern Way](#the-modern-way)
  - [FormData API](#formdata-api)
    - [One annoying gotcha: checkboxes](#one-annoying-gotcha-checkboxes)
  - [Resetting](#resetting)
- [Tracking Input and Non Submission Events](#tracking-input-and-non-submission-events)

## Handling Form Submissions

### Original Form Submissions

But first...

Originally (and still with some frameworks) form submissions actually _change the page_.

See this difference by filling out the form in `1/old-form/original-way.html` which redirects the user to the new page after submitting.

**Q: See how the URL changes to `results-page.html`? Notice the stuff appended to the end?**

```
results-page.html?username=ben&password=123
```

Old forms used a few attributes to achieve this behavior:

* `action` = the new page to go to
* `method` = `"get"` or `"post"` (`"get"` is default). `"get"` means we are requesting data using the form, `"post"` means we are sending data to be stored by the application
* The form data becomes “query params” in the URL of the new page if you use `"get"`

```html
<form action="./results-page.html" method="get">
  <!-- labels + inputs + submit button -->
</form>
```

This is the “default” behavior of the forms, which is NOT what we want.

### The Modern Way

Instead of having the browser take us away from the page, most modern web applications use JavaScript to change the contents of the screen WITHOUT leaving the page.

To handle a form submission with JavaScript, we will need to:

1. prevent the default behavior of navigating to a new page
2. collect the form data using `form.elements`
3. utilize the form data in some way (maybe render it to the screen, or send it to an API)
4. reset the form

If these are our inputs:

```html
<input type="text" name="username">
<input type="number" name="age">
<input type="checkbox" name="isHungry">
<input type="color" name="favoriteColor">
```

Then, we can handle this form on the same page like this:

* `event.preventDefault()` stops the form submission from redirecting/reloading the page
* `event.target` is a reference the `form` element that caused the `"submit"` event to occur. We'll save it in a variable since we'll reference it a lot.
* `form.elements` is an object containing all of the `input` elements of the form (the HTMLElement, not the value), accessible using the input `name` (e.g. `form.elements.username` or `form.elements.age`).
* We can access the value of most `input` elements using the `.value` property (e.g. `form.elements.username.value`)
* Checkboxes use the `.checked` property (e.g. `form.elements.isHungry.checked`).
* `form.reset()` empties out the form!

```js
const handleSubmit = (event) => {
  // stop the reload/redirect
  event.preventDefault(); 

  // the form will be the target of the "submit" event
  const form = event.target;

  // We can access the inputs of a form by name using the form's elements:
  const username = form.elements.username.value;
  const age = form.elements.age.value;
  const checkbox = form.elements.isHungry.isChecked // <-- checkboxes are different!
  const color = form.elements.favoriteColor.value;

  // what should we do with those values?? here we are printing a sentence to the console
  // where only developers will see it. Can we put it on the screen somehow?
  console.log(`hello, I am user ${username}, I am ${age} years old and my favorite color is ${color}. ${isHungry ? "I am hungry" : "I am full"}.`);

  form.reset();
}

document.querySelector('form').addEventListener('submit', handleSubmit);
```

Q: How can you improve this code so that you don't have to type out `form.elements` over and over again?

### FormData API

Another, potentially faster, way to get the values of a form is to use the `FormData` API. It starts off the same, we have to grab the `form` using `event.target`:

```js
const form = event.target;
const formValues = Object.fromEntries(new FormData(form));
console.log(formValues);
/* 
{
  currentMood: 'happy', 
  color: 'blue', 
  isHungry: 'on', 
  favoriteFruit: 'apple'
}
^ Notice something weird about isHungry?
*/
```

Let's break this down:

* We again store the `form` using `event.target`
* We invoke the function `new FormData()` with that `form` as an argument
* We immediately take the returned value and pass it to `Object.fromEntries()` which generates an Object with our form values!

#### One annoying gotcha: checkboxes

When using checkboxes, you would think they'd use a `true`/`false` setup, but nope! They use `"on"` and `undefined`.

So you'll need to do a little extra work to get them to be `true`/`false`:

```js
const form = e.target;
const formValues = Object.fromEntries(new FormData(form));
formValues.isHungry = Boolean(formValues.isHungry)
```

By converting the `formValues.isHungry` property into a Boolean, it will be `true` if the value is `"on"` or `false` if the value is `undefined`.

### Resetting

After you submit, sometimes you want to clear the form, so you can do that with `form.reset()` in the js, _or_ on the form html itself, add a button with a type of reset

```html
<button type="reset">Reset</button>
```

## Tracking Input and Non Submission Events

You can use an `input` event on an input (or fieldset) to track the changes at a more granular level:

```js
// Input elements have a `.value` property holding the current value
const handleInput = (e) => {
  console.log('new value:', e.target.value);
};

// We can grab inputs whenever they change, not just submissions
const moodTextInput = document.querySelector('#current-mood');
moodTextInput.addEventListener('input', handleInput);
```

If you specify a type of `button` on a button, it will not trigger a submit automatically. This is a pretty rare occurrence, but it's good to know!

HTML:

```html
<button type="button" id="normal-button">Capitalize Mood</button>
```

JS:

```js
// here's an example of an event fired by a button that does not trigger a submission
const capitalizeMood = (e) => {
  console.log('e.target:', e.target);
  const form = document.querySelector('form');
  
  const currentMoodInput = form.elements.currentMood;
  
  // we can also use query selector to get this input, but if we used checkboxes or radio elements, using the form is easier
  currentMoodInput.value = currentMoodInput.value.toUpperCase();
};

const normalButton = document.querySelector('#normal-button');
normalButton.addEventListener('click', capitalizeMood);
```
