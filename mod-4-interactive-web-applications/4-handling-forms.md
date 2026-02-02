# Handling Forms

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/4-4-handling-forms)!
{% endhint %}

**Table of Contents:**
- [Key Concepts](#key-concepts)
- [Forms Review: What We Learned in Module 3](#forms-review-what-we-learned-in-module-3)
- [Why Handle Forms with JavaScript?](#why-handle-forms-with-javascript)
- [Handling Form Submissions](#handling-form-submissions)
  - [Original Form Submissions](#original-form-submissions)
  - [The Modern Way: Preventing Default and Extracting Data](#the-modern-way-preventing-default-and-extracting-data)
  - [Challenge: Login Form Handler](#challenge-login-form-handler)
- [Two Ways to Extract Form Data](#two-ways-to-extract-form-data)
  - [Method 1: form.elements](#method-1-formelements)
  - [Method 2: FormData API](#method-2-formdata-api)
  - [One annoying gotcha: checkboxes](#one-annoying-gotcha-checkboxes)
  - [Which Method Should You Use?](#which-method-should-you-use)
- [Combining Forms with Dynamic Content](#combining-forms-with-dynamic-content)
  - [Challenge: Todo List with Form](#challenge-todo-list-with-form)
- [Form Validation with JavaScript](#form-validation-with-javascript)
  - [Challenge: Registration Form with Validation](#challenge-registration-form-with-validation)
- [Additional Reading](#additional-reading)
  - [Resetting Forms](#resetting-forms)
  - [Other Form Events](#other-form-events)
- [Looking Ahead: Sending Form Data to APIs](#looking-ahead-sending-form-data-to-apis)

## Key Concepts

* **Default form behavior** - collect input data, refresh the current page, and send the data to the URL specified in the `action` attribute using the HTTP `GET` method (or `POST` if specified)
* **FormData API** — the modern approach to extracting all form values as an object
* **Form validation** — checking user input before processing the form data
* **Form reset** — clearing all form inputs after successful submission

**Key Syntax**
* **`event.preventDefault()`** — prevents default form submission behavior
* **`form.elements.fieldName.value`** — access input values by name using form.elements
* **`form.elements.checkboxName.checked`** — access checkbox checked state (returns `true`/`false`)
* **`new FormData(form)`** — creates a FormData object from a form element
* **`Object.fromEntries(formData)`** — converts FormData to a plain JavaScript object
* **`form.reset()`** — clears all form inputs to their default values

## Forms Review: What We Learned in Module 3

In Module 3, we learned how to build forms using HTML and CSS:

* How to structure forms with `<form>`, `<label>`, `<input>`, and `<button>` elements
* Different input types: `text`, `number`, `email`, `date`, `checkbox`, `radio`, `<textarea>`, `<select>`
* Connecting labels to inputs using `for` and `id` attributes for accessibility
* The `name` attribute on inputs (which we'll use extensively in this lesson!)
* HTML validation attributes: `required`, `min`, `max`, `minlength`, `maxlength`, `pattern`
* Sending form data to Formspree using the `action` and `method` attributes

Here's a quick example of what we learned:

```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
  <div>
    <label for="username">Username</label>
    <input type="text" id="username" name="username" required>
  </div>

  <div>
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required>
  </div>

  <button type="submit">Sign Up</button>
</form>
```

When submitted, this form would send the data to Formspree, which would then email it to you. The page would also redirect you to the Formspree confirmation page after submission.

## Why Handle Forms with JavaScript?

Using services like Formspree is great for simple contact forms, but it has limitations:

* ❌ **You lose control** — the data is sent away and you can't do anything with it in your app
* ❌ **The page reloads/redirects** — this breaks the user experience in modern single-page applications
* ❌ **No custom validation** — you can only use basic HTML validation
* ❌ **No dynamic behavior** — you can't update the page based on the submitted data

Modern web applications handle forms with JavaScript instead, which allows us to:

* ✅ **Keep users on the same page** — no reload or redirect
* ✅ **Do whatever we want with the data** — display it, store it, send it to an API
* ✅ **Provide instant feedback** — show success messages, validation errors, loading states
* ✅ **Create dynamic experiences** — add items to a todo list, submit reviews, create posts

Think about apps you use every day:
- Twitter: typing a tweet and clicking "Post" doesn't reload the page
- Gmail: sending an email doesn't take you to a new page
- Instagram: posting a comment happens instantly without page refresh

All of these use JavaScript to handle form submissions!

## Handling Form Submissions

### Original Form Submissions

Before we learn the modern way, let's understand what forms do by default.

Originally (and still with some frameworks), form submissions actually _change the page_. When you submit a form, the browser:

1. Collects all the form data
2. Navigates to the URL specified in the `action` attribute
3. Sends the data as query parameters (if `method="GET"`) or in the request body (if `method="POST"`)

Here's an example:

```html
<form action="./results-page.html" method="GET">
  <div>
    <label for="username">Username</label>
    <input type="text" id="username" name="username">
  </div>

  <div>
    <label for="password">Password</label>
    <input type="password" id="password" name="password">
  </div>

  <button type="submit">Log In</button>
</form>
```

If you fill out this form with username "ben" and password "123", the browser will navigate to:

```
results-page.html?username=ben&password=123
```

Notice how the form data appears in the URL! This is because we used `method="GET"`.

**Key attributes:**
* `action` — the URL to navigate to when the form is submitted
* `method` — `"GET"` (default, data in URL) or `"POST"` (data in request body)

This default behavior is **NOT what we want** for modern web applications. We want to stay on the same page and handle the data with JavaScript!

### The Modern Way: Preventing Default and Extracting Data

To handle form submissions with JavaScript, we need to:

1. **Prevent** the default page reload/redirect behavior
2. **Extract** the form data from the inputs
3. **Use** the data (display it, send it to an API, etc.)
4. **Reset** the form (optional)

Let's see this in action with a simple contact form:

{% tabs %}

{% tab title="HTML" %} 

In the form HTML, the key details to pay attention to are the `name` attributes for each `<input>` element

```html
<form id="contact-form">
  <div>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required>
  </div>

  <div>
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required>
  </div>

  <div>
    <label for="message">Message</label>
    <textarea id="message" name="message" rows="4" required></textarea>
  </div>

  <button type="submit">Send Message</button>
</form>

<div id="output">
  <h3 id="output-status"></h3>
  <p>From: <span id="output-from"></span></p>
  <p>Message: <span id="output-message"></span></p>
</div>
```

{% endtab %}

{% tab title="JavaScript" %} 

Notice how this example:
1. prevents the default behavior
2. extracts the form data
3. uses the data (displays it)
4. resets the form

```js
const form = document.querySelector('#contact-form');
const output = document.querySelector('#output');

form.addEventListener('submit', (event) => {
  // 1. Prevent the default form submission (page reload)
  event.preventDefault();

  // 2. Extract the form data
  const name = form.elements.name.value;
  const email = form.elements.email.value;
  const message = form.elements.message.value;

  // 3. Use the data (here we're just displaying it)
  document.querySelector("#output-status").textContent = "Message Received!";
  document.querySelector("#output-from").textContent = `${name} (${email})`;
  document.querySelector("#output-message").textContent = `${message}`;
  
  // 4. Reset the form (clear the inputs)
  form.reset();
});
```

{% endtab %}

{% endtabs %} 

Let's break down the key parts of the JavaScript:

**`event.preventDefault()`**
* Stops the browser from doing its default action (reload/redirect)
* MUST be called at the start of the handler
* Without this, the page will reload and your JavaScript won't run!

**`form.elements.fieldName.value`**
* `form.elements` is an object containing all inputs in the form
* Access inputs by their `name` attribute (e.g., `form.elements.name`)
* Use `.value` to get the current value of the input (e.g. `form.elements.name.value`)

**`form.reset()`**
* Clears all inputs back to their default values
* Useful after successful submission

### Challenge: Login Form Handler

Build a login form handler that displays a personalized welcome message.

**HTML (provided):**
```html
<form id="login-form">
  <div>
    <label for="username">Username</label>
    <input type="text" id="username" name="username" required>
  </div>

  <div>
    <label for="password">Password</label>
    <input type="password" id="password" name="password" required minlength="8">
  </div>

  <div>
    <input type="checkbox" id="remember" name="remember">
    <label for="remember">Remember me</label>
  </div>

  <button type="submit">Log In</button>
</form>

<div id="welcome-message"></div>
```

**Your task:**
1. Add a submit event listener to the form
2. Prevent the default form submission
3. Extract the username, password, and "Remember me" checkbox values
4. Display a welcome message: `"Welcome back, [username]! Remember me: [yes/no]"`
5. Reset the form after submission

**Hint:** Use `.checked` instead of `.value` for checkboxes (returns `true` or `false`)

**<details><summary>Solution</summary>**

```js
const loginForm = document.querySelector('#login-form');
const welcomeMessage = document.querySelector('#welcome-message');

loginForm.addEventListener('submit', (event) => {
  event.preventDefault();

  // Extract form data
  const username = loginForm.elements.username.value;
  const password = loginForm.elements.password.value;
  const remember = loginForm.elements.remember.checked; // checkboxes use .checked!

  // Display welcome message
  welcomeMessage.textContent = `Welcome back, ${username}! Remember me: ${remember ? 'yes' : 'no'}`;

  // In a real app, you would:
  // - Validate the password
  // - Send credentials to a server for authentication
  // - Store a session token if "remember me" is checked

  // Reset the form
  loginForm.reset();
});
```

</details>

## Two Ways to Extract Form Data

There are two main approaches to extracting data from forms. Both are valid and you'll see both in the wild!

### Method 1: form.elements

This is the approach we've been using. It gives you fine-grained control over each input.

```js
const loginForm = document.querySelector('#login-form');
const welcomeMessage = document.querySelector('#welcome-message');

loginForm.addEventListener('submit', (event) => {
  event.preventDefault();

  // Access each input individually by its name
  const username = loginForm.elements.username.value;
  const password = loginForm.elements.password.value;
  const remember = loginForm.elements.remember.checked; // remember, checkboxes use .checked

  welcomeMessage.textContent = `Welcome back, ${username}! Remember me: ${remember ? 'yes' : 'no'}`;
  loginForm.reset();
});
```

**Pros:**
* ✅ Explicit — you see exactly which fields you're accessing
* ✅ Easy to handle checkboxes (just use `.checked`)
* ✅ Easy to access individual inputs for validation

**Cons:**
* ❌ Repetitive — lots of `form.elements.fieldName.value`
* ❌ Verbose — you have to list every field

**Tip:** You can reduce repetition by destructuring:

```js
const loginForm = document.querySelector('#login-form');
const welcomeMessage = document.querySelector('#welcome-message');

loginForm.addEventListener('submit', (event) => {
  event.preventDefault();

  // destructure the elements to get each form input
  const { username, password, remember } = loginForm.elements;

  welcomeMessage.textContent = `Welcome back, ${username.value}! Remember me: ${remember.checked ? 'yes' : 'no'}`;
  loginForm.reset();
});
```

### Method 2: FormData API

The `FormData` API is a more modern approach that automatically extracts ALL form values into an object.

```js
const loginForm = document.querySelector('#login-form');
const welcomeMessage = document.querySelector('#welcome-message');

loginForm.addEventListener('submit', (event) => {
  event.preventDefault();

  // 1. Create a FormData object from the form
  const formData = new FormData(form);

  // 2. Convert FormData to a plain JavaScript object
  const formValues = Object.fromEntries(formData);
  
  // Form inputs are stored in name:value pairs
  console.log(formValues);
  // { username: 'ben', password: 'c0d3r4lyfe!?', remember: 'on' }

  welcomeMessage.textContent = `Welcome back, ${formValues.username}! Remember me: ${formValues.remember ? 'yes' : 'no'}`;
  loginForm.reset();
});
```

Let's break this down:

**`new FormData(form)`**
* Creates a `FormData` object containing all the form's input values
* Automatically finds all inputs with a `name` attribute
* The `FormData` object is iterable but not directly usable as a normal object

**`Object.fromEntries(formData)`**
* Converts the FormData object into a plain JavaScript object
* Each input's `name` becomes a property
* Each input's `value` becomes the property value

**Pros:**
* ✅ Concise — just two lines to get all form data
* ✅ Automatic — grabs all inputs without listing them
* ✅ Less code to maintain when adding/removing fields

**Cons:**
* ❌ Less explicit — harder to see which fields exist
* ❌ Checkbox gotcha (see below)

### One annoying gotcha: checkboxes

When using the FormData API with checkboxes, you'll notice something unexpected:

```js
const formData = new FormData(form);
const formValues = Object.fromEntries(formData);
console.log(formValues);
/*
{
  username: 'ben',
  password: 'c0d3r4lyfe!?',
  remember: 'on',        // <-- Checked checkbox gives "on"
}
*/
```

Checkboxes don't give you `true`/`false` like you'd expect. Instead:
* **Checked** checkboxes have a value of `"on"`
* **Unchecked** checkboxes have a value of `undefined`

**For basic conditionals, this works fine** because of JavaScript's truthiness:

```js
const formValues = Object.fromEntries(new FormData(loginForm));

// This works! "on" is truthy, undefined is falsy
if (formValues.remember) {
  console.log("User wants to be remembered!");
}
```

**However, when sending data to APIs or storing it, you should convert to proper booleans:**

```js
const formValues = Object.fromEntries(new FormData(loginForm));

// Convert checkbox to boolean for cleaner data
formValues.remember = Boolean(formValues.remember);

console.log(formValues);
// { username: 'ben', password: 'c0d3r4lyfe!?', remember: true }
```

**Why convert to boolean?**
* APIs expect `true`/`false`, not `"on"`/`undefined`
* It's more semantically clear: `remember: true` vs `remember: "on"`
* Easier to work with: `data.remember === true` vs `data.remember === "on"`

**How does `Boolean()` work?**
* If checked: `formValues.remember` is `"on"` → `Boolean("on")` is `true`
* If unchecked: `formValues.remember` is `undefined` → `Boolean(undefined)` is `false`

If you have multiple checkboxes, convert each one:

```js
const formValues = Object.fromEntries(new FormData(form));
formValues.isHungry = Boolean(formValues.isHungry);
formValues.acceptTerms = Boolean(formValues.acceptTerms);
formValues.subscribeNewsletter = Boolean(formValues.subscribeNewsletter);
```

### Which Method Should You Use?

**Use `form.elements`** when:
* You have a small form with just a few inputs
* You need fine control over individual fields
* You want explicit, readable code
* You're still learning form handling

**Use `FormData API`** when:
* You have a large form with many inputs
* You want concise code
* You're sending the data to an API (we'll learn this soon!)
* You don't mind converting checkboxes manually

Both are valid! Choose what makes sense for your use case.

## Combining Forms with Dynamic Content

Forms become really powerful when combined with dynamic content. We can use form submissions to create, update, or delete items on the page!

Let's build a simple contact list. When the user submits the form, we'll add a new contact card to the page.

{% tabs %}

{% tab title="HTML" %} 

```html
<form id="contact-form">
  <div>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required>
  </div>

  <div>
    <label for="phone">Phone</label>
    <input type="tel" id="phone" name="phone" required>
  </div>

  <button type="submit">Add Contact</button>
</form>

<ul id="contacts-list">
  <!-- Add contact cards here. For example:
   <li>
    <strong>Ada Lovelace:</strong>
    <a href="tel:1234567890">1234567890</a>
   </li>
   -->
</ul>
```

{% endtab %}

{% tab title="JavaScript" %} 

```js
const contactForm = document.querySelector('#contact-form');
const contactsList = document.querySelector('#contacts-list');

const handleSubmit = (event) => {
  event.preventDefault();

  // Extract form data using FormData API
  const formData = Object.fromEntries(new FormData(contactForm));
  const { name, phone } = formData;

  // Create, Modify, Append pattern!
  const li = document.createElement('li');
  const strong = document.createElement('strong');
  const phoneLink = document.createElement('a');

  strong.textContent = `${name}: `;
  phoneLink.href = `tel:${phone}`;
  phoneLink.textContent = phone;

  li.append(strong, phoneLink);
  contactsList.append(li);

  // Reset form for next entry
  contactForm.reset();
};

contactForm.addEventListener('submit', handleSubmit);
```

{% endtab %}

{% endtabs %} 


Notice how we:
1. Extract the form data
2. Use the Create, Modify, Append pattern to add new content
3. Reset the form so it's ready for the next contact

### Challenge: Todo List with Form

Build a todo list application where users can add and remove todos.

**HTML (provided):**
```html
<form id="todo-form">
  <div>
    <label for="todo-text">New Todo</label>
    <input type="text" id="todo-text" name="todoText" required>
  </div>
  <button type="submit">Add Todo</button>
</form>

<ul id="todo-list"></ul>
```

**Requirements:**
1. When the form is submitted:
   - Prevent the default behavior
   - Extract the todo text from the input
   - Create a new `<li>` element
   - The `<li>` should contain:
     - The todo text
     - A "Delete" button
   - Append the `<li>` to the `#todo-list`
   - Reset the form
2. When a Delete button is clicked:
   - Remove its parent `<li>` from the list
   - HINT: Use event delegation on the `<ul>`

**<details><summary>Solution</summary>**

```js
const todoForm = document.querySelector('#todo-form');
const todoList = document.querySelector('#todo-list');

// Handle form submission
const handleSubmit = (event) => {
  event.preventDefault();
  const form = event.target;

  // Extract the todo text
  const todoText = form.elements.todoText.value;

  // Create, Modify, Append
  const li = document.createElement('li');
  const deleteBtn = document.createElement('button');

  li.textContent = todoText;
  deleteBtn.textContent = 'Delete';
  deleteBtn.type = 'button'; // Prevent form submission if button is inside a form

  li.append(deleteBtn);
  todoList.append(li);

  // Reset form
  form.reset();
};

// Handle delete button clicks using event delegation
const handleDelete = (event) => {
  // Check if a button was clicked
  if (event.target.matches('button')) {
    // Remove the parent li
    event.target.closest('li').remove();
  }
};

todoForm.addEventListener('submit', handleSubmit);
todoList.addEventListener('click', handleDelete);
```

**Bonus improvements:**
- Add a counter showing the number of todos
- Add a "Clear All" button
- Store todos in an array and use a `renderTodos()` function
- Add the ability to mark todos as complete

</details>

## Form Validation with JavaScript

HTML validation attributes (`required`, `min`, `max`, etc.) are great, but sometimes we need custom validation logic. JavaScript gives us complete control!

Let's build a registration form with custom validation:

{% tabs %}

{% tab title="HTML" %} 

In the HTML, pay attention to the `<span class="error" id="input-name-error"></span>` elements that have been added for each input. 

```html
<form id="registration-form">
  <div>
    <label for="username">Username</label>
    <input type="text" id="username" name="username" required>
    <span class="error" id="username-error"></span>
  </div>

  <div>
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required>
    <span class="error" id="email-error"></span>
  </div>

  <div>
    <label for="password">Password</label>
    <input type="password" id="password" name="password" required>
    <span class="error" id="password-error"></span>
  </div>

  <div>
    <label for="confirm-password">Confirm Password</label>
    <input type="password" id="confirm-password" name="confirmPassword" required>
    <span class="error" id="confirm-error"></span>
  </div>

  <button type="submit">Register</button>
</form>

<div id="success-message">
  <h3 id="success-heading"></h3>
  <p id="success-welcome"></p>
</div>
```

{% endtab %}

{% tab title="CSS" %}

Here, we've created a class to make errors stand out.

```css
.error {
  color: red;
  font-size: 0.875rem;
  display: block;
  
  /* Reserve space even when empty */
  min-height: 1.25rem; 
}
```

{% endtab %}

{% tab title="JavaScript" %}

In the JavaScript, pay attention to how the helper functions work within the form submission event handler.

```js
const registrationForm = document.querySelector('#registration-form');
const successHeading = document.querySelector('#success-heading');
const successWelcome = document.querySelector('#success-welcome');

// Helper Function: Form validation
const validateForm = (formData) => {
  const errors = {};

  // Username must be at least 3 characters
  if (formData.username.length < 3) {
    errors.username = 'Username must be at least 3 characters';
  }

  // Email must contain @ symbol (basic check)
  if (!formData.email.includes('@')) {
    errors.email = 'Please enter a valid email address';
  }

  // Password must be at least 8 characters and contain a number
  if (formData.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  } else if (!/\d/.test(formData.password)) {
    errors.password = 'Password must contain at least one number';
  }

  // Passwords must match
  if (formData.password !== formData.confirmPassword) {
    errors.confirm = 'Passwords do not match';
  }

  return errors;
};

// Helper Function: Display error messages
const displayErrors = (errors) => {
  // Clear all previous errors
  document.querySelectorAll('.error').forEach(span => span.textContent = '');

  // Display new errors
  Object.keys(errors).forEach(field => {
    const errorSpan = document.querySelector(`#${field}-error`);
    if (errorSpan) {
      errorSpan.textContent = errors[field];
    }
  });
};

registrationForm.addEventListener('submit', (event) => {
  event.preventDefault();
  const form = event.target;

  const formData = Object.fromEntries(new FormData(form));

  // Validate the form data to get any errors
  const errors = validateForm(formData);

  // If there are errors, display them and stop
  if (Object.keys(errors).length > 0) {
    displayErrors(errors);
    return; // Don't submit if there are errors!
  }

  // If there are no errors, clear out any previous errors
  displayErrors({});

  // Update the success message
  successHeading.textContent = 'Registration Successful!';
  successWelcome.textContent = `Welcome, ${formData.username}!`;

  form.reset();
});
```

{% endtab %}

{% endtabs %} 


This example demonstrates:
* Custom validation logic (username length, password requirements, matching passwords)
* Displaying error messages next to the relevant fields
* Preventing submission if validation fails
* Clearing errors when validation passes

### Challenge: Registration Form with Validation

Enhance the registration form above by adding these validation rules:

1. Username must:
   - Be at least 3 characters
   - Contain only letters, numbers, and underscores
   - HINT: Use regex `/^[a-zA-Z0-9_]+$/`

2. Password must:
   - Be at least 8 characters
   - Contain at least one uppercase letter
   - Contain at least one lowercase letter
   - Contain at least one number

3. Add visual feedback:
   - Input borders turn red when there's an error
   - Input borders turn green when valid
   - Add CSS classes `.error` and `.valid` to inputs

**<details><summary>Solution</summary>**

```js
const registrationForm = document.querySelector('#registration-form');
const successMessage = document.querySelector('#success-message');

const validateForm = (formData) => {
  const errors = {};

  // Username validation
  if (formData.username.length < 3) {
    errors.username = 'Username must be at least 3 characters';
  } else if (!/^[a-zA-Z0-9_]+$/.test(formData.username)) {
    errors.username = 'Username can only contain letters, numbers, and underscores';
  }

  // Email validation
  if (!formData.email.includes('@')) {
    errors.email = 'Please enter a valid email address';
  }

  // Password validation
  if (formData.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  } else if (!/[A-Z]/.test(formData.password)) {
    errors.password = 'Password must contain at least one uppercase letter';
  } else if (!/[a-z]/.test(formData.password)) {
    errors.password = 'Password must contain at least one lowercase letter';
  } else if (!/\d/.test(formData.password)) {
    errors.password = 'Password must contain at least one number';
  }

  // Confirm password
  if (formData.password !== formData.confirmPassword) {
    errors.confirm = 'Passwords do not match';
  }

  return errors;
};

const displayErrors = (errors) => {
  // Clear all previous error messages and classes
  document.querySelectorAll('.error').forEach(span => span.textContent = '');
  document.querySelectorAll('input').forEach(input => {
    input.classList.remove('error', 'valid');
  });

  // Display new errors and add error class to inputs
  Object.keys(errors).forEach(field => {
    const errorSpan = document.querySelector(`#${field}-error`);
    const input = document.querySelector(`[name="${field}"]`) ||
                  document.querySelector('#confirm-password');

    if (errorSpan) {
      errorSpan.textContent = errors[field];
    }
    if (input) {
      input.classList.add('error');
    }
  });

  // Add valid class to inputs without errors
  const formInputs = ['username', 'email', 'password', 'confirmPassword'];
  formInputs.forEach(field => {
    if (!errors[field]) {
      const input = document.querySelector(`[name="${field}"]`) ||
                    document.querySelector('#confirm-password');
      if (input && input.value) {
        input.classList.add('valid');
      }
    }
  });
};

const handleSubmit = (event) => {
  event.preventDefault();
  const form = event.target;

  const formData = Object.fromEntries(new FormData(form));
  const errors = validateForm(formData);

  if (Object.keys(errors).length > 0) {
    displayErrors(errors);
    return;
  }

  displayErrors({});

  // Clear previous success message
  successMessage.textContent = '';

  const successHeading = document.createElement('h3');
  const welcomeText = document.createElement('p');

  successHeading.textContent = 'Registration Successful!';
  welcomeText.textContent = `Welcome, ${formData.username}!`;

  successMessage.append(successHeading, welcomeText);

  form.reset();
};

registrationForm.addEventListener('submit', handleSubmit);
```

**Additional CSS:**
```css
input.error {
  border: 2px solid red;
}

input.valid {
  border: 2px solid green;
}

.error {
  color: red;
  font-size: 0.875rem;
  display: block;
  min-height: 1.25rem;
}
```

</details>

## Additional Reading

### Resetting Forms

After successfully processing a form, it's common to clear the inputs so the form is ready for the next entry.

**Method 1: `form.reset()` in JavaScript**

```js
const handleSubmit = (event) => {
  event.preventDefault();
  const form = event.target;

  // ... process the form data ...

  // Reset all inputs to their default values
  form.reset();
};
```

**Method 2: Add a Reset button in HTML**

```html
<form>
  <!-- form inputs -->

  <button type="submit">Submit</button>
  <button type="reset">Clear Form</button>
</form>
```

A reset button automatically clears the form without any JavaScript needed!

**When to reset:**
* ✅ After successfully adding an item to a list
* ✅ After successfully sending data to a server
* ❌ When validation fails (keep the data so users can fix errors)
* ❌ When editing existing data (you want to keep the current values)

### Other Form Events

Besides the `submit` event, there are several other useful form events:

**`input` event** — fires every time an input's value changes (as you type)

```js
const nameInput = document.querySelector('#name');

nameInput.addEventListener('input', (event) => {
  console.log('Current value:', event.target.value);
  // Great for: live character counters, search-as-you-type, instant validation
});
```

**`change` event** — fires when an input's value changes AND the input loses focus

```js
const selectMenu = document.querySelector('#country');

selectMenu.addEventListener('change', (event) => {
  console.log('Selected:', event.target.value);
  // Great for: dropdowns, radio buttons, checkboxes
});
```

**`focus` and `blur` events** — fires when an input gains or loses focus

```js
const emailInput = document.querySelector('#email');

emailInput.addEventListener('focus', () => {
  console.log('Email input focused');
  // Great for: showing help text, highlighting the field
});

emailInput.addEventListener('blur', () => {
  console.log('Email input lost focus');
  // Great for: validating after user finishes typing
});
```

**Non-submit buttons** — buttons with `type="button"` don't submit the form

```html
<button type="button" id="normal-button">Capitalize Name</button>
```

```js
const normalButton = document.querySelector('#normal-button');

normalButton.addEventListener('click', () => {
  const form = document.querySelector('form');
  const nameInput = form.elements.name;
  nameInput.value = nameInput.value.toUpperCase();
});
```

This is useful for buttons that manipulate form data without submitting!

## Looking Ahead: Sending Form Data to APIs

So far, we've been displaying form data on the page or storing it in arrays. But in real applications, we typically want to:

* **Save the data permanently** — store it in a database
* **Share the data across devices** — access it from different computers/phones
* **Sync the data** — keep it updated across multiple users

To do this, we need to send our form data to a **server** using an **API**.

Here's a preview of what's coming in the next lessons:

```js
const handleSubmit = async (event) => {
  event.preventDefault();
  const form = event.target;

  // Extract form data
  const formData = Object.fromEntries(new FormData(form));

  try {
    // Send data to a server (we'll learn this next!)
    const response = await fetch('https://api.example.com/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData)
    });

    if (!response.ok) {
      throw new Error('Failed to create user');
    }

    const newUser = await response.json();
    console.log('User created:', newUser);

    form.reset();
  } catch (error) {
    console.error('Error:', error);
  }
};
```

Don't worry if this looks confusing! We'll learn about:
* **Promises** (lesson 4) — handling asynchronous operations
* **fetch()** (lesson 5) — sending HTTP requests to APIs
* **async/await** (lesson 6) — cleaner syntax for working with promises

For now, focus on mastering form handling with JavaScript. Once you're comfortable extracting and validating form data, you'll be ready to send it anywhere!

---

**Key Takeaways:**
* Use `event.preventDefault()` to stop page reloads
* Extract data with `form.elements` or FormData API
* Combine forms with dynamic content to build interactive features
* Validate user input before processing
* Reset forms after successful submission
* Form handling is the foundation for sending data to APIs
