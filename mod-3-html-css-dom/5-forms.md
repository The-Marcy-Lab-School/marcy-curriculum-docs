# Forms

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/2-2-2-lecture-forms)!
{% endhint %}

**Table of Contents**

- [What is a Form?](#what-is-a-form)
  - [Form Basic Structure](#form-basic-structure)
  - [Inputs](#inputs)
  - [Accessibility: Labels and aria-label](#accessibility-labels-and-aria-label)
  - [Submit button](#submit-button)
  - [Input types](#input-types)
  - [HTML Form Validation](#html-form-validation)
- [Styling Forms](#styling-forms)


**Q: What is a form and why is a form useful?**

## What is a Form?

A form is a collection of inputs that can be filled out by a user to submit a collection of data.

<figure><img src="../.gitbook/assets/form-demo.gif" alt="A user fills out the inputs and the values are combined to display a sentence"><figcaption><p>A user fills out the inputs and the values are combined to display a sentence</p></figcaption></figure>

**Q: What kinds of data might we collect in a form?**

**Q:When should we use a form? For all user input?**

1. Click a button to pop open a tab with more data - use a `button`, not a `form`
2. A text input that filters the list of data shown - use an `input`, but not a full `form`
3. A link that takes the user to a new location - use an `anchor` or a `button`, not a `form`
4. Registering a new user with a name, password, and user details - use a `form`!

### Form Basic Structure

Forms are made up of a few parts:

* `<form>`: the container for the form
  * `h2 (or other header)`: A heading to describe your form
  * `<label>`: A label for each input
  * `<input>`: A place for the user to enter some data. There are many types (text, radio, checkbox, etc...)
  * `<button>`: A button to submit the form

Below is a full example! For now, just focus on the Elements that are in the example (`form`, `h2`, `label`, `input`, `button`)

```html
<form id="mood-form" aria-label="mood-form">
  <h2 id="mood-form-header">What is your mood?</h2>
  <p> Tell us how you are feeling </p>

  <!-- We often use divs to group together labels and inputs -->
  <div>
    <label for="current-mood">Current mood</label>
    <input type="text" id="current-mood" name="currentMood" placeholder="How are you feeling?">
  </div>

  <div>
    <label for="is-hungry">Are you hungry?</label>
    <input type="checkbox" id="is-hungry" name="isHungry">
  </div>

  <button class="submit">Submit</button>
</form>
```

There are a lot of attributes to learn, particularly for `label` and `input`. We'll go over them but here are the essential new ones that we'll cover:

* `form` Elements have an `aria-label` for accessibility
* `input` Elements have a `type` attribute to determine the kind of input (text, number, color, etc...)
* `input` Elements have a `name` attribute which will be used to extract data from the form
* `input` Elements MUST have an `id` attribute
* `label` Elements MUST have a `for` attribute equal to the `input` Element's `id`. This connects them.

### Inputs

Inputs are where users can input their data. Each `input` Element has a type — the basic inputs are `"text"` or `"number"` but there are many more cool ones.

Here is an example (note we're missing `label`s!)

```html
<form>
  <input type="text" id="username-input" name="username">
  <input type="number" id="age-input" name="age">
  <input type="checkbox" id="is-hungry-input" name="isHungry">
  <input type="color" id="favorite-color-input" name="favoriteColor">
</form>
```

> 💡 **Best Practice:** Use `kabob-case` for `id` and `camelCase` for `name` (we'll learn why in a moment)

Some other types of inputs Elements (other than the literal `input` tag) are the `select` and `textarea` (and technically `buttons`).

**Inputs must have a `name` attribute.** We'll use those later when we are getting data from the form when it is submitted.

### Accessibility: Labels and aria-label

Right now, our form is just a bunch of inputs. But how does the user know which input is for which value? Well, for one, we could add a header:

```html
<form>
  <h2>Sign Up Form </h2>
  <input type="text" id="username-input" name="username">
  <input type="number" id="age-input" name="age">
  <input type="checkbox" id="is-hungry-input" name="isHungry">
  <input type="color" id="favorite-color-input" name="favoriteColor">
</form>
```

But that only helps our seeing users! When designing websites, we must design for ALL of our users.

Labels are _crucial_ for our non-seeing users and accessibility. They tell screen readers what the purpose of an element is. There are two kinds of labels that we'll use:

1. The `aria-label`/`aria-labelledby` attribute — describes the purpose of a `form` (or really of any element).
2. The `<label>` element — describes the purpose of an `<input>` element.

```html
<form aria-labelled="sign-up-header">
  <h2 id="sign-up-header">Sign Up Form </h2>
  <div>
    <label for="username-input">Username:</label>
    <input type="text" id="username-input" name="username">
  </div>
  <div>
    <label for="age-input">Age:</label>
    <input type="number" id="age-input" name="age">
  </div>
  <div>
    <label for="is-hungry-input">Are you Hungry?</label>
    <input type="checkbox" id="is-hungry-input" name="isHungry">
  </div>
  <div>
    <label for="favorite-color-input">Favorite Color:</label>
    <input type="color" id="favorite-color-input" name="favoriteColor">
  </div>
</form>
```

A few notes about `aria-lablledby`

* **A**ccessible **R**ich **I**nternet **A**pplications (ARIA) is a set of roles and attributes that define ways to make web content and web applications more accessible to people with disabilities.
* We added an `id` to the `h2` element
* We added the `aria-lablledby` attribute to the `form` element so that screen readers know to use the `h2` element text as the aria label.

Here are a few notes about the `<label>` element:

* Connect the `<label>` to the `<input>` with the `for` attribute. It should be the same as the input's `id`:
* Labels make it so that clicking a label will focus the input or check the checkbox.
* Labels help our seeing users too by describing the input!

Notice that we also wrap each `label` + `input` pair inside of a `div`. This isn't necessary but it makes styling each pair of elements a lot easier.

### Submit button

All forms should end with a submit button.

```html
<form aria-label="sign-up">

  <!-- labels + inputs -->

  <button type="submit">Submit</button>
  <!-- type="submit" is optional here -->
</form>
```

By default `button` elements have a `type="submit"` so you don't _need_ to put it. But you can if you want! There are other types of buttons we'll discuss later.

Clicking the submit button (or pressing enter while focused inside a form) will fire the `'submit'` event on the form.

Our objective is to...

1. listen for this `'submit'` event
2. create an event handler that extracts the user input data
3. do something with that data! (save it in a database, show it in the UI, etc...)

### Input types

On the `index.html` go over the basic text inputs, but then also radio groups (with fieldsets and legends), and the select. Point out when to use them:

* `<input type="text">`: any open ended but short data
* `<input type="number">`: any number, min and max can lock you in (not featured on form, just mention)
* `<input type="radio">`: when you want to pick one of a few options
* `<textarea>`: any open ended but long data
* `<select>` and `<option>`: when you want to pick one of a _lot_ of options

```html
<label for="favorite-fruit"> Choose your favorite fruit </label>
<select id="favorite-fruit" name="favoriteFruit">
  <option value="apple">Apple</option>
  <option value="banana">Banana</option>
  <option value="orange">Orange</option>
</select>
```

### HTML Form Validation

* required
* min
* max
* pattern

## Styling Forms

* Vertical form layouts
* Horizontal form layouts (multi-column)
* Button groups
* Responsive Forms