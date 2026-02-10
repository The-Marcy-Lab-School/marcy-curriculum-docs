# 6. Forms

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-6-forms)!
{% endhint %}

**Table of Contents**:

* [Key Concepts](6-forms.md#key-concepts)
* [What is a Form?](6-forms.md#what-is-a-form)
  * [When to Use a Form](6-forms.md#when-to-use-a-form)
  * [Form Basic Structure](6-forms.md#form-basic-structure)
  * [Input Basics](6-forms.md#input-basics)
  * [Labels](6-forms.md#labels)
  * [Submit button](6-forms.md#submit-button)
* [Form Submissions](6-forms.md#form-submissions)
  * [Setting Up Formspree](6-forms.md#setting-up-formspree)
* [Input Deep Dive](6-forms.md#input-deep-dive)
  * [Textarea](6-forms.md#textarea)
  * [Checkboxes](6-forms.md#checkboxes)
  * [Select](6-forms.md#select)
  * [Radios, Fieldset, and Legend](6-forms.md#radios-fieldset-and-legend)
  * [When To Use Each Input Type](6-forms.md#when-to-use-each-input-type)
* [Form Best Practices](6-forms.md#form-best-practices)
  * [HTML Form Validation](6-forms.md#html-form-validation)
  * [Styling Forms](6-forms.md#styling-forms)
* [Key Takeaways](6-forms.md#key-takeaways)

## Key Concepts

* A **form** is a collection of inputs that can be filled out by a user to submit a collection of data.
* **Key form elements** include `<form>`, `<label>`, `<input>`, `<textarea>`, `<select>`, `<fieldset>`, and `<button>`.
* The `<input>` element's **`type` attribute** determines what kind of data the input accepts (e.g., `text`, `number`, `date`, `checkbox`, `radio`, `email`, `url`).
* **Labels and inputs connect** using the `for` attribute on `<label>` and a matching `id` attribute on `<input>`. This connection is essential for accessibility.
* The **`name` attribute** on inputs labels the data when the form is submitted.
* The **`action` attribute** on `<form>` specifies the URL where form data gets sent.
* The **`method` attribute** on `<form>` specifies how data is sent (typically `"POST"`).
* **HTML validation attributes** like `required`, `min`/`max`, `minlength`/`maxlength`, and `pattern` can validate form data before submission.

## What is a Form?

A form is a collection of inputs that can be filled out by a user to submit a collection of data.

**Q: What are some forms you've filled out recently? What data did they collect?**

<details>

<summary><strong>Click Here To See Some Examples</strong></summary>

Examples you might see:

* Signing up for a website (username, email, password)
* Applying for a job (name, resume, cover letter)
* Making a purchase (shipping address, payment info)
* Booking an event (date, number of attendees)
* Leaving a review (rating, written feedback)

</details>

Today we'll build a **Job Application Tracker** — a form that helps you stay organized when applying to multiple companies. This is a tool you'll actually use during your job search.

![A job application tracker form collecting company, position, and application details](../.gitbook/assets/form-job-application-tracker.png)

### When to Use a Form

Not every user interaction needs a form. Use a form when you're **collecting multiple pieces of related data that will be submitted together**.

**Use a form when:**

* ✅ Registering a new user (name, email, password, preferences)
* ✅ Recording a job application (company, position, date, status)
* ✅ Paying for something by credit card (cardholder name, credit card number, expiration, zip code)

**A form isn't necessary for:**

* ❌ A single search input that filters results as you type
* ❌ A button that opens/closes a menu
* ❌ A link that navigates to a new page
* ❌ A single checkbox that toggles dark mode

Can you think of other examples?

<details>

<summary><strong>Q: Which of these user interactions would require a form: (A) clicking on a "like" button on a social media post, (B) Searching for a flight, or (C) signing up for a gym membership.</strong></summary>

(A) Clicking on a like button would not require a form since there is just one input to interact with: the button

(B) Searching for a flight and (C) signing up for a gym membership would both require forms since there are multiple pieces of information to submit.

</details>

### Form Basic Structure

Every form follows a similar pattern:

1. A `form` to contain the form elements
2. A heading to describe the form
3. Pairs of `<label>` and `<input>` elements for user input
4. A submit `<button>` at the end.

```html
<!-- The page's title -->
<h1>Job Application Tracker</h1>

<form>
  <!-- The form's title -->
  <h2>Track Your Job Application</h2>

  <!-- Every label/input pair gets a div container -->
  <div>
    <label>Company Name</label>
    <input type="text" />
  </div>

  <div>
    <label>Position Title</label>
    <input type="text" />
  </div>

  <button type="submit">Save Application</button>
</form>
```

**Key parts:**

* `<form>`: Container for all inputs
* `<h2>`: Heading that describes the form's purpose
* `<label>`: Describes what each input is for
* `<input>`: Where users enter data
* `<button>`: Submits the form

**TODO:** Take 5 minutes to recreate this form structure in the `body` of your `index.html` file below the `h1` element.

{% hint style="info" %}
**Why do we wrap each label and input in divs?**

`<label>` and `<input>` elements are both inline elements by default which can make the form look like one long line. By wrapping each pair in a `<div>`, we get each form input on a new line. It also makes it easier to style the pair as a unit.
{% endhint %}

### Input Basics

Inputs are where users enter their data. The `type` attribute determines what kind of data the input accepts.

```html
<input type="text">
<input type="number">
<input type="date">
<input type="url">
<input type="checkbox">
<input type="email">
<input type="password">
<input type="range">
```

Each input type behaves differently — `text` accepts any characters, `number` only accepts digits, `date` shows a calendar picker, and so on.

**TODO:** Inside your form, add in three more `<input>` elements: (1) application date, (2) expected salary, (3) a job posting URL. For each, add a `<label>` describing the form input and surround the label/input pair with a `<div>`.

<details>

<summary><strong>Solution</strong></summary>

We'll use a "date" type input for the application date, a "number" type input for the salary, and a "url" type input for the job posting url. Make sure to add a `<label>` for each `<input>` and wrap them in a `<div>`:

```html
<form>
  <!-- Other form elements... -->

  <div>
    <label>Application Date</label>
    <input type="date" />
  </div>

  <div>
    <label>Expected Salary</label>
    <input type="number" />
  </div>
  
  <div>
    <label>Job Posting URL</label>
    <input type="url" />
  </div>
</form>
```

</details>

### Labels

The `<label>` elements that we currently have are useful for our users with good eyesight. But for our visually impaired users, they aren't accessible.

To improve our form's accessibility we need to connect each `<input>` to its `<label>` using the `id` and `for` attributes:

```html
<div>
  <label for="company-name">Company Name</label>
  <input type="text" id="company-name">
</div>

<div>
  <label for="position-title">Position Title</label>
  <input type="text" id="position-title">
</div>
```

**How labels and inputs connect:**

* Give each `<input>` a unique `id` attribute
  * e.g. `id="company-name"`
* Give each `<label>` a `for` attribute that matches the input's `id`
  * e.g. `for="company-name"`
* The `for` and `id` values must match _exactly_.

**Why this connection matters:**

* **Screen readers announce the label** when the input is focused, so users know what to enter
* **Clicking the label focuses its input** — try it! This is especially helpful for small checkboxes since it makes the clickable area much larger.
* The connection is semantic, not just visual — assistive technologies understand the relationship

<details>

<summary><strong>Q: What happens when you click a label without the <code>for</code> attribute?</strong></summary>

Nothing. The label is just text. The `for` attribute is what creates the connection to the input.

</details>

**TODO:** Add a `for` and matching `id` attribute to each pair of labels and inputs.

<details>

<summary><strong>Solution</strong></summary>

At this point, your form should look like this:

```html
<form>
  <h2>Track Your Job Application</h2>

  <div>
    <label for="company-name">Company Name</label>
    <input type="text" id="company-name"/>
  </div>

  <div>
    <label for="position-title">Position Title</label>
    <input type="text" id="position-title"/>
  </div>

  <div>
    <label for="application-date">Application Date</label>
    <input type="date" id="application-date"/>
  </div>

  <div>
    <label for="salary">Salary</label>
    <input type="number" id="salary"/>
  </div>

  <div>
    <label for="job-posting-url">Job Posting URL</label>
    <input type="url" id="job-posting-url"/>
  </div>

    <button type="submit">Save Application</button>
</form>
```

</details>

### Submit button

Technically, a form can be submitted without a submit button by pressing the <kbd>Enter</kbd> key. However, including a button to click on is an accessibility best practice.

```html
<form>
  <!-- other labels + inputs -->

  <button type="submit">Save Application</button>
</form>
```

Additionally, the `type="submit"` is actually optional (it's the default), but being explicit is good practice. A label isn't needed for this button as long as the text content is descriptive.

When a user clicks the submit button (or presses Enter), the browser collects all the form data and sends it somewhere. But where?

## Form Submissions

In professional web applications, form data is typically sent to a **server** — a computer that receives the data and does something with it:

* Store it in a database
* Send an email notification
* Process a payment
* Create a user account
* Update a record

Building your own server to handle form submissions requires learning backend technologies like APIs and databases like Postgres. We'll get there eventually! But for now, we can use a free service called **Formspree** that handles the server part for us.

### Setting Up Formspree

Formspree receives your form submissions and forwards them to your email. Here's how to set it up for our Job Application Tracker:

**1. Create a Formspree account**

* Go to [formspree.io](https://formspree.io) and sign up with your email (it's free)

**2. Create a new form**

* From your dashboard, click "New Form"
* Give it a name like "Job Application Tracker"
* Formspree will generate a unique form **endpoint URL** (the web address where the form data will be sent)

**3. Add `name` attributes to your inputs**

Formspree needs to know what to call each piece of data. The `name` attribute provides this label (use camelCase):

```html
<div>
  <label for="company-name">Company Name</label>
  <input type="text" id="company-name" name="companyName">
</div>

<div>
  <label for="position-title">Position Title</label>
  <input type="text" id="position-title" name="positionTitle">
</div>
```

When Formspree receives this data, it will show up labeled as "companyName" and "positionTitle" in the email you receive. You can also view all of the form submissions on Formspree.

{% hint style="warning" %}
**Every input needs a `name` attribute for the data to be submitted.** Without it, that input's data won't be included in the submission.
{% endhint %}

**TODO:** Update your form `name` attributes for each input. Use camelCase.

**4. Update your HTML form**

And finally, we need to direct our form to submit the data to Formspree. Add two attributes to your `<form>` element:

* `action` — the Formspree endpoint URL where data gets sent
* `method="POST"` — tells the browser to send data (rather than request it)

```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
  <!-- your labels and inputs -->
</form>
```

**TODO:** Update your form with the `action` and `method` attributes. Then test it by submitting some data and checking your email!

## Input Deep Dive

Now that we know the basics of forms, let's look at a few additional types of inputs to make this form complete.

### Textarea

A `<textarea>` element can be used for longer text inputs. The `rows` and `cols` attributes can set the initial size of the textarea and the `placeholder` attribute shows some placeholder text until we begin typing.

Let's add a textarea where we can take notes about the application.

```html
<!-- Long text (notes, cover letter) -->
 <div>
  <label for="notes">Notes</label>
  <textarea 
    id="notes" 
    rows="5" 
    cols="40" 
    placeholder="Interview prep, follow-up reminders, etc." 
    name="notes">
  </textarea>
</div>
```

**TODO:** Add the textarea input to your form.

### Checkboxes

Checkboxes are useful when you need a yes/no answer to something.

```html
<!-- Checkbox (yes/no, on/off) -->
<div>
  <input type="checkbox" id="has-referral" name="hasReferral" >
  <label for="has-referral">Do you have a referral?</label>
</div>
```

It is common to see the label placed _after_ the checkbox.

**TODO:** Add the checkbox input to your form.

### Select

The `<select>` element creates a dropdown menu. It acts as a container for `<option>` elements which appear in the dropdown menu. Each `option` has a `value` attribute which defines how the selected option will show up in the dataset when the form is submitted.

```html
<!-- Select dropdown (pick ONE from MANY options) -->
<div>
  <label for="job-source">How did you find this job?</label>
  <select id="job-source" name="jobSource">
    <option value="">-- Select an option --</option>
    <option value="linkedin">LinkedIn</option>
    <option value="indeed">Indeed</option>
    <option value="company-site">Company Website</option>
    <option value="referral">Referral</option>
    <option value="recruiter">Recruiter</option>
  </select>
</div>
```

**TODO:** Add the dropdown select input to your form.

### Radios, Fieldset, and Legend

Radio inputs are useful if you want the user to choose one (and only one) option from a set of options. When all radio inputs share the same `name` attribute, selecting one radio option will deselect the others.

Each radio input has a `value` attribute which defines how the selected input will show up in the dataset when the form is submitted.

```html
<!-- Radio buttons (pick ONE option) -->
<fieldset>
  <legend>Application Status</legend>
  <div>
    <input type="radio" id="status-applied" name="status" value="applied" />
    <label for="status-applied">Applied</label>
  </div>
  <div>
    <input type="radio" id="status-interview" name="status" value="interview" />
    <label for="status-interview">Interview Scheduled</label>
  </div>
  <div>
    <input type="radio" id="status-offer" name="status" value="offer" />
    <label for="status-offer">Offer Received</label>
  </div>
</fieldset>
```

The `<fieldset>` and `<legend>` elements are used to group together a set of related input elements. They are particularly useful for radio elements which otherwise may look a bit untidy but can be used to group together any kind of input.

**TODO:** Add the fieldset and radio inputs to your form.

### When To Use Each Input Type

* `type="text"` — Any short, open-ended text
* `type="number"` — Numeric data (can add `min`, `max`, `step` constraints)
* `type="date"` — Dates (gives you a calendar picker)
* `type="email"` / `type="url"` — Validates format automatically
* `<textarea>` — Long, multi-line text
* `type="checkbox"` — Yes/no, on/off (can select multiple if you have several)
* `type="radio"` — Pick ONE from a few options (same `name`, different `value`)
* `<select>` — Pick ONE from MANY options (use when you have 5+ choices)

**Q: When would you use radio buttons vs. a select dropdown?**

<details>

<summary>Answer</summary>

Radio buttons when you have 2-5 options and want them all visible. Select dropdowns when you have many options (6+) and want to save space.

</details>

**Note about radio buttons:**

* All radio buttons in a group must have the **same `name`** attribute
* Each needs a **different `value`** attribute
* The `value` is what gets submitted, not the label text
* Use `<fieldset>` and `<legend>` to group related radio buttons (accessibility best practice)

## Form Best Practices

### HTML Form Validation

You can validate form data before it's submitted using HTML attributes:

```html
<!-- Required field (must be filled out) -->
<input type="text" id="company-name" name="companyName" required>

<!-- Number constraints -->
<input type="number" id="salary" name="salary" min="30000" max="1000000" step="1000" required>

<!-- Date constraints -->
<input type="date" id="application-date" name="applicationDate" min="2023-01-01" max="2024-12-31">
<!-- min sets the earliest allowed date, max sets the latest allowed date -->

<!-- Length constraints between 2 and 50 characters -->
<input type="text" id="position" minlength="2" maxlength="50">

<!-- Pattern matching (regex that requires 5 digits) -->
<input type="text" id="zip-code" pattern="[0-9]{5}">
```

**Common validation attributes:**

* `required` — Field must be filled out
* `min` / `max` — For numbers and dates
* `step` — Specifies the interval between allowed values for numbers and dates
* `minlength` / `maxlength` — For text
* `pattern` — Must match a regex pattern

When validation fails, the browser shows an error message and prevents submission.

**Q: Why is HTML validation helpful even though we'll add JavaScript validation later?**

<details>

<summary>Answer</summary>

\- Provides instant feedback before JavaScript loads - Works even if JavaScript fails or is disabled - Accessible to screen readers - Less code to write (browser does the work)

</details>

### Styling Forms

Forms need styling to be usable. Flexbox is particularly helpful for organizing form layouts.

**Form container and basic styles:**

```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
  color: #333;
}

h2 {
  margin-bottom: 20px;
}

form {
  background-color: white;
  max-width: 800px;
  margin-inline: auto;
  padding: 30px;
}

label {
  font-weight: bold;
  color: #555;
}

input, textarea, select {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  background-color: #007bff;
  color: white;
  padding: 12px 30px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

button:hover {
  background-color: #0056b3;
}
```

**Using flexbox for form fields:**

Each label/input pair can use flexbox with `flex-direction: column` to stack vertically. The `gap` property adds space between the label and input:

```css
.form-field {
  display: flex;
  flex-direction: column;
  gap: 5px;
  margin-bottom: 20px;
}
```

Now, add the `class="form-field"` to each div container:

```html
<div class="form-field">
  <label for="company">Company Name</label>
  <input type="text" id="company" name="company">
</div>
```

**Two-column layouts with flexbox:**

Use a `.form-row` wrapper to place two fields side-by-side. The `flex: 1` on child `.form-field` elements distributes space equally:

```css
.form-row {
  display: flex;
  gap: 20px;
}

.form-row .form-field {
  flex: 1;
}
```

Wrap two form fields in a `div.form-row` element to create a row with two columns:

```html
<div class="form-row">
  <div class="form-field">
    <label for="date">Application Date</label>
    <input type="date" id="date" name="applicationDate">
  </div>
  <div class="form-field">
    <label for="salary">Expected Salary</label>
    <input type="number" id="salary" name="salary">
  </div>
</div>
```

**Checkbox and radio layouts:**

Checkboxes and radios look better with the label _beside_ the input. Use flexbox with `align-items: center`:

```css
.checkbox-field {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 20px;
}

.radio-group {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  padding: 10px;
  margin-bottom: 20px;
}

.radio-option {
  display: flex;
  align-items: center;
  gap: 5px;
}
```

Then set the `class="radio-group"` attribute on the `fieldset` and the `class="radio-option"` attribute on each radio div:

```html
<fieldset class="radio-group">
  <legend>Application Status</legend>
  <div class="radio-option">
    <input type="radio" id="status-applied" name="status" value="applied" />
    <label for="status-applied">Applied</label>
  </div>
  <div class="radio-option">
    <input type="radio" id="status-interview" name="status" value="interview" />
    <label for="status-interview">Interview Scheduled</label>
  </div>
  <div class="radio-option">
    <input type="radio" id="status-offer" name="status" value="offer" />
    <label for="status-offer">Offer Received</label>
  </div>
</fieldset>
```

**Responsive forms:**

Use a media query to stack columns on small screens:

```css
@media (max-width: 600px) {
  .form-row {
    flex-direction: column;
    gap: 0;
  }

  .radio-group {
    flex-direction: column;
    gap: 10px;
  }
}
```

**Key styling considerations:**

* `box-sizing: border-box` on all elements makes width calculations predictable
* `margin-inline: auto` centers the form horizontally
* `gap` in flexbox containers provides consistent spacing without margin hacks
* `flex: 1` distributes space equally among siblings
* `flex-direction: column` in media queries converts rows to stacked layouts

## Key Takeaways

* **Forms collect multiple related pieces of data** that are submitted together
* **Always use labels** for accessibility and usability
* **Different input types** serve different purposes (text, number, date, select, etc.)
* **HTML validation** provides instant feedback before JavaScript runs
* **Vertical layouts** with full-width inputs work well for most forms

Next lesson, we'll use JavaScript to handle form submissions, extract data, and build interactive applications with that data.
