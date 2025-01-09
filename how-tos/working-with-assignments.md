# How to Do Short Response and Coding Assignments

**Table of Contents:**

- [What is a SWE Assignment?](#what-is-a-swe-assignment)
  - [Tips for Working on SWE Coding Assignments](#tips-for-working-on-swe-coding-assignments)
  - [Jest and Testing](#jest-and-testing)
  - [Feedback on Coding Assignments](#feedback-on-coding-assignments)
- [What is a SWE-SR Assignment?](#what-is-a-swe-sr-assignment)
  - [Scoring on Short Response Assignments](#scoring-on-short-response-assignments)
  - [Feedback on Short Response Assignments](#feedback-on-short-response-assignments)
  - [Examples of Short Responses](#examples-of-short-responses)
    - [A Response That Could Use Improvement](#a-response-that-could-use-improvement)
    - [An Exemplar Response](#an-exemplar-response)
- [Using AI on Assignments](#using-ai-on-assignments)
- [Submitting On Time](#submitting-on-time)
- [How to Work On Assignments](#how-to-work-on-assignments)
  - [Assignment Setup](#assignment-setup)
  - [Submitting the Assignment](#submitting-the-assignment)
  - [How to Undo a Commit to Main and Move Commits to Draft](#how-to-undo-a-commit-to-main-and-move-commits-to-draft)


## What is a SWE Assignment?

Software Engineering (SWE) code assignments are opportunities for you to practice the skills learned in your Software Engineering fellowship at The Marcy Lab School. In these assignments, you can expect to:

* Create code from scratch
* Modify existing code
* Debug broken code

Code assignments will typically have an `src` directory where you will be writing your code. They will also have a `tests` directory with `.spec.js` test files that will automatically test your code each time you push to your repository.

### Tips for Working on SWE Coding Assignments

As you work on your coding assignment, you may be tempted to jump right in and start coding. However, it is often best to slow down and plan out your approach first. Look at the problem and make sure you understand fully what it is asking you to do. At Marcy, we recommend that you use the PEDAC approach ([read more about PEDAC here](how-to-pedac.md)).

You may get stuck along the way, which can always be frustrating. Debugging is an essential skill but its hard to know where to start sometimes. That's why we love the "rubber duck" approach ([read more about debugging with the rubber duck approach here](how-to-debug.md))

### Jest and Testing

Tests are an essential part of professional software development. Without testing our code, we run the risk of deploying code with unexpected bugs. With testing, we are forced to think critically about how we expect our program to behave and then write our code to satisfy those tests. This process of first writing tests and then writing code to satisfy those tests is called **Test-Driven Development (TDD)**.

At Marcy, we use [Jest](https://jestjs.io/) to write automated tests for coding assignments. Automated tests are JavaScript files that:

1. Import functions from source code
2. Run those functions with various inputs
3. Compare the returned values against the expected values.

A Jest test is written in a file ending in `.spec.js` or `.test.js` and looks like this:

{% code title="circle-helpers.spec.js" lineNumbers="true" %}
```javascript
// Import the functions to be tested
const {
  getArea,
  getDiameter,
  getCircumference
} = require('./circle-helpers')

// Create a "Test Suite" for these functions
describe('Circle Helper Tests', () => {
  // Test the getArea function with a series of expectations
  test('getArea returns the Area of a circle', () => {
    // Translation: We expect getArea(2) to return Math.PI * 2 * 2
    expect(getArea(2)).toBe(Math.PI * 2 * 2);
    expect(getArea(3)).toBe(Math.PI * 3 * 3);

    // It is useful to think about what should be returned for "bad" inputs. 
    expect(getArea()).toBe(NaN);
    expect(getArea('hello')).toBe(NaN);
  });

  // Test the getDiameter function with a series of expectations
  test('getDiameter returns the Diameter of a circle', () => {
    // ...tests for getDiameter
  });

  // ...more tests
})
```
{% endcode %}

Each assignment will have automated test files in the `tests` directory. They will show you exactly how we expect your functions to behave. **DO NOT MODIFY THE TESTS**.

To run these automated tests, use the command:

```sh
npm test # run the tests once
# or
npm run test:w # run the tests each time the file changes ("watch mode")
```

After running this command, you will see the following output. Initially, all tests will fail.

![failing Jest tests](img/failing-tests.png)

The test output provides some really useful information.

* We can see which tests failed
* For each failing test, we can see which `expect()` statement failed
* We can see what the expected value is (`12.566...`) and what our function actually returned (`undefined`).

Armed with this information, we can more confidently build our functions knowing that we have a specific set of targets to aim for. Automated tests allow us to repeatedly run our code against the same set of tests until all expectations are met.

All assignments will also come with a `src/playground.js` file that you can use to manually test your code as you develop. We recommend the following workflow:

1. Run the automated tests to see what you are aiming for.
2. Copy the function you are currently working on into the `playground.js` file and use the test cases to manually test your code
3. Once you are satisfied, copy your function back into the original file and run the automated tests again.
4. If anything fails, return to the `playground.js` file to make adjustments.
5. Repeat.

### Feedback on Coding Assignments

In order for a coding assignment to be marked as "Complete", it must pass at least 75% of the automated tests (though you should always strive to pass 100% of the tests!).

For example, an assignment with 10 automated tests requires 8 passing tests to be marked "Complete".

**There is no limit to the number of times you can submit an assignment.** As such, you are encouraged to **always submit your assignments on time to ensure that your instructor can provide you feedback and support** to complete your assignments.

To support you in your growth as a software engineer, your instructor may provide feedback on any of the following areas (expand each category to see more details):

<details><summary>Code Quality</summary>

  * Adherence to coding standards and conventions (e.g., ESLint rules)
  * Use of proper naming conventions (variables, functions)
  * Avoidance of code duplication
  * Clear and concise comments

</details>

<details><summary>Functionality</summary>

  * Does the code meet the project requirements?
  * Does it pass all tests, both automated and edge cases?
  * Correctness of input/output handling

</details>

<details><summary>Modularity and Reusability</summary>

  * Proper use of functions or classes
  * Modular and reusable code (avoiding large, monolithic functions)
  * Clear separation of logic

</details>

<details><summary>Version Control Practices</summary>

  * Meaningful commit messages
  * Consistent use of branches
  * Proper use of pull requests and code reviews

</details>

<details><summary>Code Structure and Organization</summary>

  * File structure
  * Separation of concerns
  * Readability and logical flow

</details>

<details><summary>Error Handling</summary>

  * Proper use of try-catch blocks
  * Graceful handling of unexpected inputs or states
  * Meaningful error messages

</details>

<details><summary>Efficiency and Performance</summary>

  * Time and space complexity
  * Use of appropriate data structures and algorithms
  * Avoidance of unnecessary computations

</details>

## What is a SWE-SR Assignment?

Software Engineering (SWE) Short Response (SR) assignments are opportunities to develop your technical communication skills.

Short Response prompts will always be found in the `README.md` file of your assignment. Each prompt will be listed under a `## Prompt X` heading and you should add your responses under the `### Response X` heading using Markdown ([read about Markdown syntax here](https://www.markdownguide.org/basic-syntax/)).

For example:

{% code overflow="wrap" %}
```md
## Prompt 2

Modules let us split up our code into separate files. What are benefits of doing this? Are there any downsides?

### Response 2

Response here...
```
{% endcode %}

In these assignments, you may encounter the following types of prompts:

* Research a new topic and share your findings.
* Analyze a programming best practice and list its advantages and disadvantages.
* Compare and contrast approaches for solving a problem and provide an argument in support of one.
* Teach a piece of syntax or a concept with examples and analogies.

Your intended audience should be someone with _some_ experience programming but who is still learning. As such, strive to be as clear and concise as possible. There is a fine balance between too much information and just enough but when in doubt, provide more details. Examples and analogies can often help!

Strive to do more than just answer the prompts. Use the prompts as opportunities to practice your technical communication. While anyone can write functional code, not everyone can communicate clearly so take pride in this work!

### Scoring on Short Response Assignments

Every response will be given a score from 0-3 using the following scoring system which grades assignments primarily based on accuracy and completion (rather than on writing quality):

* 0 — Did not attempt to answer the prompt
* 1 — Did not answer all parts of the prompt AND information and examples provided are inaccurate and non-functional.
* 2 — Did not answer all parts of the prompt OR information and examples provided are inaccurate and non-functional.
* 3 — Answered all parts of the prompt AND information and examples provided are accurate and functional.

We understand that typos and grammar mistakes can happen, however, you will also lose .5 points on any the prompt where typos or grammar errors are "distracting". Distracting meaning that if you saw this on the job, it would have to be retyped for clarification or for the sake of presentation.

{% hint style="info" %}
In order for a short response assignment to be marked as "Complete", you must score at least a 75% on the assignment.

For example, an assignment with 5 short response prompts has a total possible score of 15. You must score at least a 12 on that assignment.

Assignments below 75% must be revised and resubmitted.
{% endhint %}

### Feedback on Short Response Assignments

While the score will primarily reflect the accuracy and completion of your responses, to support you in your growth as a technical communicator, your instructor may also provide feedback on the following areas:

<details><summary>Clarity</summary>

- Is the main idea or goal of the writing clear?
- Are technical terms defined or explained for the intended audience?
- Is the writing free of unnecessary jargon or overly complex language?

</details>

<details><summary>Structure</summary>

- Does the content follow a logical flow?
- Are headings, subheadings, and paragraphs effectively used to guide the reader?
- Are transitions between paragraphs / sections smooth and easy to follow?

</details>

<details><summary>Engagement</summary>

- Is the tone appropriate for the audience (e.g., conversational vs. formal)?
- Does the writing capture and maintain interest?
- Are examples, analogies and visuals used effectively to enhance understanding?

</details>

<details><summary>Audience Fit</summary>

- Does the writing match the knowledge level of the intended audience?
- Are key questions or concerns the audience might have addressed?

</details>

<details><summary>Mechanics</summary>

- Are there grammar, spelling, or punctuation errors?
- Is the formatting consistent and professional (e.g., indentation in code snippets, use of backticks and code fences to highlight code, use of bolding and italics to emphasize key words)?

</details>

### Examples of Short Responses

Let's look at an example of two responses that are complete and accurate but in very different ways.

The examples below each respond to the following prompt:

> Explain what `if` and `else` statements are used for. Provide a code example to enhance your response.

#### A Response That Could Use Improvement

First, let's look at an example of a response that is technically accurate and complete but would likely receive feedback for improvements. As you read, what feedback would you provide?

{% code overflow="wrap" %}
<pre><code>They are used if you want to like do one thing or another in your code. Here is an example.

if (true) {
  console.log('this');
} 
else {
  console.log('that');
}
</code></pre>
{% endcode %}

**<details><summary>See how this response is rendered in Markdown!</summary>**

They are used if you want to like do one thing or another in your code. Here is an example.

if (true) {
  console.log('this');
} 
else {
  console.log('that');
}

</details>

For this response, the following feedback would be provided:
* **Clarity**: Without the prompt, it would be hard to know that "they" is referring to `if` and `else` statements at the beginning of the sentence. Additionally, no explanation of the example is provided, leaving open the possibility that the reader misunderstands the syntax.
* **Engagement**: The example demonstrates the syntax but the example is hard to relate to a real-world scenario for programming.
* **Mechanics**: By adding in markdown formatting, key terms could be highlighted and the provided code block can be placed inside of a code fence (see below for an example of this). Look at the rendered markdown above to see how the structure of the code block is lost without a code fence.

#### An Exemplar Response

Below is a great example of a short response answer. As you read, what do you notice about it?

{% code overflow="wrap" %}
<pre><code>In JavaScript, `if` statements are used by programmers to decide whether or not to perform an action in their program. This decision is based on a **boolean expression** (a bit of code that evaluates to `true` or `false`). 

For example, we can simulate flipping a coin using an `if` statement and the `Math.random()` function which generates a number between `0` and `1`. 

```js
const random = Math.random();

// only print out heads if the random number is between 0.5 and 1
if (random > 0.5) {
  console.log("heads");
}

// otherwise, do nothing
```

In this example, only when the boolean expression `random > 0.5` is `true` will the string `"heads"` be printed. Otherwise nothing will happen.

When combined with an `else` statement, the programmer can provide an alternative action to perform when the boolean expression is `false`.

```js
const random = Math.random();

if (random > 0.5) {
  console.log("heads");
} else {
  console.log("tails");
}
```

With the addition of the `else` statement, the program will print out `"heads"` if `random > 0.5` is `true` or `"tails"` if it is `false`.</code></pre>
{% endcode %}

**<details><summary>See how this response is rendered in Markdown!</summary>**

In JavaScript, `if` statements are used by programmers to decide whether or not to perform an action in their program. This decision is based on a **boolean expression** (a bit of code that evaluates to `true` or `false`). 

For example, we can simulate flipping a coin using an `if` statement and the `Math.random()` function which generates a number between `0` and `1`. In this example, the program will print `"heads"` only _if_ the boolean expression `random > 0.5` is `true`. Otherwise nothing will happen:

```js
const random = Math.random();

// Print out "heads" if the random number is between 0.5 and 1, otherwise, do nothing
if (random > 0.5) {
  console.log("heads");
}
```

When combined with an `else` statement, the programmer can provide an alternative action to perform when the boolean expression is `false`.

```js
const random = Math.random();

// Print out "heads" if the random number is between 0.5 and 1
if (random > 0.5) {
  console.log("heads");
} 
// Otherwise, print tails
else {
  console.log("tails");
}
```

With the addition of the `else` statement, the program will print out `"tails"` if `random > 0.5` is `false`.

</details>

Among other things, this response does the following things well:
* It answers all parts of the prompt, the information provided is accurate, and the provided code is functional.
* The writing is easy to understand and information is presented in a logical order.
* The provided JavaScript example is engaging as it is highly relatable and demonstrates a practical use case of `if` and `else` statements.
* The example is explained afterwards.
* It uses markdown formatting to bold key terms and highlight code with backticks (e.g. `event.target`) and wraps the code blocks in code fences. Look at the rendered markdown above to see how the use of markdown dramatically improves the appearance of the writing.

## Using AI on Assignments

On short response assignments, you may use generative AI tools like ChatGPT to help structure and refine your responses and to check for spelling and grammar mistakes. However, the majority of the writing should be yours! As a rule, aim to have no more than about 25% of the content written by a generative AI assistant.

On coding assignments, you may use generative AI tools like ChatGPT to help explain assignment instructions or test code. You may also use it to improve code that you've already written in areas such as code quality, structure, or efficiency.

If you are really stuck, you can use solutions generated by generative AI tools in the same way that you would use coding solutions found on the internet. However, the majority of the code should be yours! As a rule, aim to have no more than about 25% of the content written by someone other than you.

**All code generated using AI or found on the internet should be cited using comments. Every line of found code should be commented with an explanation of the code**

<details>

<summary><strong>See below for an example</strong></summary>

```js
// Note: I found this solution online: https://www.geeksforgeeks.org/how-to-find-the-sum-of-all-elements-of-a-given-array-in-javascript/# 
const sumArray = (arr) => {
  // this variable will track the growing sum as we loop
  let total = 0;

  // this will loop over every index of arr from 0 to arr.length-1
  for (let i = 0; i < arr.length; i++) {
    // this will take the current value and add it to the total
    total += arr[i];
  }

  // when we are done looping, return the count
  return total;
}
```

</details>

For more details on our AI Policy, refer to the [Marcy Lab School Docs](https://marcylabschool.gitbook.io/marcy-lab-school-docs/guidelines-and-policies/ai-policy).

## Submitting On Time

"Grades" don't exist at Marcy. We only need performance data in order to know how you're doing, and make sure the people who need help get it as quickly as they can. It's ok if you didn't finish by the deadline! Just show us what you have. We'll have office hours and reviews, and we want to know what you are all struggling with so we can use those meetings effectively. **This is not about grades, its about seeing what you know, and where we can help!**

## How to Work On Assignments

For short response and coding assignments, you will create a new branch called `draft` to complete your work. When you are ready to submit, you will create a Pull Request (PR) and tag your instructor for review.

Below, you will find detailed instructions for setting up your assignments and for submitting your assignments.

{% hint style="info" %}
Want to learn more about git branching and making Pull Requests? Check out the [Git Branching & PRs lesson](https://marcylabschool.gitbook.io/marcy-lab-school-docs/mod-0-command-line-interfaces-git-and-github/4-git-branching)!
{% endhint %}

### Assignment Setup

Upon receiving any assignment, you should do the following setup steps:

1. Accept the assignment using the provided GitHub classroom link. It should generate a repository that is unique to you.
2. Clone down your repository and `cd` into your repository.
3.  (_For coding assignments only_) Run the following commands:

    ```sh
    npm i
    npm test
    ```

    This will install any necessary dependencies and then show you all the tests you need to work on. We may explain each function in the `README.md`, but always run the tests because they are crucial to explaining what the code literally must do.
4.  Create and checkout a new branch called `draft`

    ```sh
    git checkout -b draft
    ```

### Submitting the Assignment

To submit an assignment, do the following:

1.  Add, commit, and push your `draft` branch to your repository.

    ```bash
    git add -A
    git commit -m "Finished up to problem 5"
    git push
    ```
2.  You may need to set an upstream branch using the command

    ```sh
    git push --set-upstream origin draft
    ```
3.  Create a pull request using the **Pull Request** tab (and ignore the "Compare & pull request" button).

    ![Create a pull request by clicking on the "Pull Request" tab. Ignore the "Compare & pull request" button.](img/assignment-create-a-pr.png)
4.  Change the **compare** branch to be your `draft` branch and then click **Create pull request**!

    ![Change the compare branch to be your draft branch](img/assignment-select-draft-branch.png)
5.  Tag your instructor as a **Reviewer**.

    ![alt text](img/assignment-tag-reviewer.png)
6. Submit the URL of the pull request on canvas. The URL should start with `https://github.com` and end with `/pull/NUMBER`, like this: https://github.com/benspector-mls/hello-world/pull/3
7. Your instructor will provide feedback on GitHub and will either approve your branch to be merged or will request that you resubmit.

### How to Undo a Commit to Main and Move Commits to Draft

If you've accidentally added code to your `main` branch and want to move it into a `draft` branch, no need to worry. You can undo this by moving your code into a `draft` branch and reverting your `main` branch to the previous commit.

First, do the following to create / update your `draft` branch with the latest changes in `main`:

1. On your own computer, `cd` into the repo and `git checkout main` and `git pull` to make sure your local main branch is in sync with the remote repository.
2. Then `git checkout draft` (`git checkout -b draft` if you don't have a `draft` branch).
3. Run `git merge main` to make sure that the `draft` branch is up to date with the `main` branch.
4. `git push` to push your `draft` branch to GitHub. You may need to run `git push --set-upstream origin draft` if this is your first time pushing your `draft` branch.
5. Go to GitHub and double check that your `draft` branch has been pushed and that it contains your work.

Then, do the following to revert your `main` branch back to the initial commit:

1. `git checkout main` and `git log` to see the full commit history.
2.  Find the commit you want to return to. Copy the commit SHA code (a 40-digit code identifying the commit).

    ![A git log showing the commit SHA codes](img/git-log-commit-sha.png)
3. Run the command `git reset --hard <commit_sha>` replacing `<commit_sha>` with the copied SHA from the last step. This will return your `main` branch back to that commit.

{% hint style="danger" %}
**DANGER: Running this next command will permanently delete the most recent commit from your `main` branch's commit history. Make sure that your `draft` branch on GitHub contains all of your work before proceeding.**
{% endhint %}

4. Finally, in your `main` branch `git push -f` to force the remote `main` branch return to the previous commit as well.

Once you've done this, return to GitHub and confirm that the `main` branch has returned to the previous commit and that your `draft` branch still contains your work. Then follow the steps above to create a Pull Request.
