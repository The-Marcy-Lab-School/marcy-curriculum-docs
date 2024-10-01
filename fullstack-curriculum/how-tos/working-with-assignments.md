# How to Set Up Assignments

**Table of Contents:**
- [What is a SWE Assignment?](#what-is-a-swe-assignment)
  - [Feedback on Coding Assignments](#feedback-on-coding-assignments)
- [What is a SWE-SR Assignment?](#what-is-a-swe-sr-assignment)
  - [Feedback on Short Response Assignments](#feedback-on-short-response-assignments)
- [Using AI on Assignments](#using-ai-on-assignments)
- [Submitting On Time](#submitting-on-time)
- [How to Work On Assignments](#how-to-work-on-assignments)
  - [Assignment Setup](#assignment-setup)
  - [Submitting the Assignment](#submitting-the-assignment)


## What is a SWE Assignment?

Software Engineering (SWE) code assignments are opportunities for you to practice the skills learned in your Software Engineering fellowship at The Marcy Lab School. In these assignments, you can expect to:
* Create code from scratch
* Modify existing code
* Debug broken code

Code assignments will typically have an `src` directory where you will be writing your code. They will also have a `tests` directory with `.spec.js` test files that will automatically test your code each time you push to your repository.

### Feedback on Coding Assignments

You are encouraged to look at the `.spec.js` test files in the `tests` directory as they will show you exactly how we expect to be able to use your functions. **DO NOT MODIFY THE TESTS**.

To run tests manually, you can use the command:

```sh
npm test # run the tests once
# or
npm run test:w # run the tests each time the file changes ("watch mode")
```

{% hint style="info" %}

In order for a coding assignment to be marked as "Complete", it must pass at least 75% of the automated tests.

For example, an assignment with 10 automated tests requires 8 passing tests to be marked "Complete".

{% endhint %}

To support you in your growth as a software engineer, your instructor may provide feedback on any of the following areas (roughly in order of importance):

- **Code Quality**
  - Adherence to coding standards and conventions (e.g., ESLint rules)
  - Use of proper naming conventions (variables, functions)
  - Avoidance of code duplication
  - Clear and concise comments
- **Functionality**
  - Does the code meet the project requirements?
  - Does it pass all tests, both automated and edge cases?
  - Correctness of input/output handling
- **Modularity and Reusability**
  - Proper use of functions or classes
  - Modular and reusable code (avoiding large, monolithic functions)
  - Clear separation of logic
- **Version Control Practices**
  - Meaningful commit messages
  - Consistent use of branches
  - Proper use of pull requests and code reviews
- **Code Structure and Organization**
  - File structure
  - Separation of concerns
  - Readability and logical flow
- **Error Handling**
  - Proper use of try-catch blocks
  - Graceful handling of unexpected inputs or states
  - Meaningful error messages
- **Efficiency and Performance**
  - Time and space complexity
  - Use of appropriate data structures and algorithms
  - Avoidance of unnecessary computations

## What is a SWE-SR Assignment?

Software Engineering (SWE) Short Response (SR) assignments are opportunities to develop your technical communication skills. In these assignments, you may encounter the following types of questions:
* Research a new topic and share your findings.
* Explain a piece of syntax with examples and analogies.
* Analyze a programming best practice and list its advantages and disadvantages.

Short Response questions will always be found in the `README.md` file of your assignment. Each question will be listed under a `## Question X` heading and you should add your responses under the `### Response X` heading using Markdown ([read about Markdown syntax here](https://www.markdownguide.org/basic-syntax/)).

For example
```markdown
## Question 2

Modules let us split up our code into separate files. What are benefits of doing this? Are there any downsides?

### Response 2

Response here...
```

For these assignments, your intended audience should be someone with some experience programming but who is still learning. As such, strive to be as clear and concise as possible. There is a fine balance between too much information and just enough. Examples and analogies can often help!

### Feedback on Short Response Assignments

Every short response question will be given a score from 0-3 using the following scoring system:

* 0 — Did not attempt the question 
* 1 — Attempted the question and the response is more wrong than right, or parts of the question are not fully answered, or the answer is lacking in clarity. Examples and/or diagrams are missing (when required).
* 2 — Attempted the question and all parts of the question are answered, but there are some mistakes and/or the clarity could be improved. Examples and/or diagrams may be missing or could be improved.
* 3 — Answered the question correctly and completely. The response is clear and concise. Examples and/or diagrams are well-chosen and enhance the response.

We understand that typos and grammar mistakes can happen, however, you will also lose .5 points on any the question where typos or grammar errors are "distracting". Distracting meaning that if you saw this on the job, it would have to be retyped for clarification or for the sake of presentation.

{% hint style="info" %}

In order for a short response assignment to be marked as "Complete", you must score at least a 75% on the assignment. 

For example, an assignment with 5 short response questions has a total possible score of 15. You must score at least a 12 on that assignment.

{% endhint %}

To support you in your growth as a technical communicator, your instructor may also provide feedback on the following areas:

* **Clarity**: The communication is precise, concise, and unambiguous. Technical jargon is explained, and terms are used consistently.
* **Completeness**: The communication covers all necessary technical details, scenarios, and edge cases, providing sufficient examples or references.
* **Structure and Organization**: For longer answers, answers are logically organized with clear sections, headings, and transitions between ideas. The structure enhances understanding.
* **Use of Examples and Diagrams**: Uses well-chosen examples, diagrams, or code snippets (when appropriate) that significantly aid understanding and clarify complex concepts.
* **Accuracy**: The content is technically correct and adheres to current best practices and standards.
* **Conciseness**: The communication is efficient, avoiding unnecessary detail or excessive language, while still being comprehensive.

## Using AI on Assignments

On short response assignments, you may use generative AI tools like ChatGPT to help structure and refine your responses and to check for spelling and grammar mistakes. However, the majority of the writing should be yours! As a rule, aim to have no more than about 25% of the content written by a generative AI assistant. 

On coding assignments, you may use generative AI tools like ChatGPT to help explain assignment instructions or test code. You may also use it to improve code that you've already written in areas such as code quality, structure, or efficiency. 

If you are really stuck, you can use solutions generated by generative AI tools in the same way that you would use coding solutions found on the internet. However, the majority of the code should be yours! As a rule, aim to have no more than about 25% of the content written by someone other than you.

**All code generated using AI or found on the internet should be cited using comments. Every line of found code should be commented with an explanation of the code** 

**<details><summary>See below for an example</summary>**

> 
> ```js
> // Note: I found this solution online: https://www.geeksforgeeks.org/how-to-find-the-sum-of-all-elements-of-a-given-array-in-javascript/# 
> const sumArray = (arr) => {
>   // this variable will track the growing sum as we loop
>   let total = 0;
> 
>   // this will loop over every index of arr from 0 to arr.length-1
>   for (let i = 0; i < arr.length; i++) {
>     // this will take the current value and add it to the total
>     total += arr[i];
>   }
> 
>   // when we are done looping, return the count
>   return total;
> }
> ```
>

</details>

For more details on our AI Policy, refer to the [Marcy Lab School Docs](https://marcylabschool.gitbook.io/marcy-lab-school-docs/guidelines-and-policies/ai-policy).

## Submitting On Time
 
"Grades" don't exist at Marcy. We only need performance data in order to know how you're doing, and make sure the people who need help get it as quickly as they can. It's ok if you didn't finish by the deadline! Just show us what you have. We'll have office hours and reviews, and we want to know what you are all struggling with so we can use those meetings effectively. **This is not about grades, its about seeing what you know, and where we can help!**

## How to Work On Assignments

For short response and coding assignments, you will create a new branch called `draft` to complete your work. When you are ready to submit, you will create a Pull Request (PR) and tag your instructor for review. 

Below, you will find detailed instructions for setting up your assignments and for submitting your assignments.

{% hint style="info" %}

Want to learn more about git branching and making Pull Requests? Check out the [Git Branching & PRs lesson](https://marcylabschool.gitbook.io/marcy-lab-school-docs/fullstack-curriculum/mod-0-command-line-interfaces-git-and-github/4-git-branching)!

{% endhint %}


### Assignment Setup

Upon receiving any assignment, you should do the following setup steps:
1. Accept the assignment using the provided GitHub classroom link. It should generate a repository that is unique to you.
2. Clone down your repository and `cd` into your repository. 
3. (*For coding assignments only*) Run the following commands:
    
    ```sh
    npm i
    npm test
    ```
    
    This will install any necessary dependencies and then show you all the tests you need to work on. We may explain each function in the `README.md`, but always run the tests because they are crucial to explaining what the code literally must do.

4. Create and checkout a new branch called `draft`

    ```sh
    git checkout -b draft
    ```

### Submitting the Assignment

To submit an assignment, do the following:
1. Add, commit, and push your `draft` branch to your repository. 

    ```bash
    git add -A
    git commit -m "Finished up to problem 5"
    git push
    ```

2. You may need to set an upstream branch using the command

    ```sh
    git push --set-upstream origin draft
    ```

3. Create a pull request using the **Pull Request** tab (and ignore the "Compare & pull request" button).

    ![Create a pull request by clicking on the "Pull Request" tab. Ignore the "Compare & pull request" button.](./img/assignment-create-a-pr.png)

4. Change the **compare** branch to be your `draft` branch and then click **Create pull request**!

    ![Change the compare branch to be your draft branch](./img/assignment-select-draft-branch.png)

5. Tag your instructor as a **Reviewer**.
6. Your instructor will provide feedback on GitHub and will either approve your branch to be merged or will request that you resubmit.