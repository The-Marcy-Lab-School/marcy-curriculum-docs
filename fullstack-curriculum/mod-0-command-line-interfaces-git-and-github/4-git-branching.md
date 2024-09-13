# Git Branching

- [Overview](#overview)
- [Branches](#branches)
  - [Keep the Main Branch Stable](#keep-the-main-branch-stable)
  - [Collaborating with Branches](#collaborating-with-branches)
- [Tips for success](#tips-for-success)


<!-- ## Watch -->
<!-- {% embed url="" %} -->

<!-- ## Slides

{% embed url="https://docs.google.com/presentation/d/1IA9kq0wCzPmeEfel3CwN-KiRev959AWaZ2xQnW0p1uo/embed?start=false&loop=false&delayms=3000" %} -->


## Overview

GitHub enables developers across the world to collaborate on projects. In this lesson, we'll learn how to use GitHub to create and manage branches, merge branches, create pull requests, and resolve merge conflicts.

**Objectives**

You will be able to…
* Define the terms "branch" as it relates to git
* Create a branch through the Github GUI and the CLI.
* Create a pull request.
* Resolve merge conflicts through the Github GUI
* Fork a repository.

**Key Terms**

* **Branch** — a copy of a repository at a point in time that allows developers to work on a feature without impacting the rest of the project.
* **Merge** - to combine two or more branches into one
* **Pull Request** — a request for another developer to pull down your branch and review your code. If they approve the changes, they will merge your branch into the main branch!
* **Merge Conflict** — a situation in which two or more branches need to be merged but have modified the same lines of code, causing the merge to fail. This happens all the time and can be resolved through the Github GUI or the CLI.
* **Fork** — a copy of a repository that is disconnected from the main repository. Typically they include the entire commit history of the main repository at the time the fork was created.

**Important Git commands**

{% hint style="info" %}
**Note:** In the commands below, argument placeholders will be written like this: `<argument>`. When using these commands, replace the `<argument>` with your desired inputs, making sure to leave out the `<>` as well.
{% endhint %}

* `git branch <branch_name>` — create a new branch
* `git checkout <branch_name>` — switch to a branch
* `git merge <branch_name>` — merge a branch into the current branch

## Branches

Every repository's commit history has what is called a **"main branch"** or **"trunk"**. This is the original version of the repository and whenever anyone visits a repository and clones it down, this is what they will see.

Up until now, we've been pushing our commits entirely to the main branch. But as they say...

![](./img/do-not-push-to-main.png)

### Keep the Main Branch Stable

A **branch** is a copy of the repository at a point in time. Branches allow developers to work on a feature without impacting the main branch.

![](./img/branching.png)

Branching ensures that the main branch is always a "stable" version. That is, if someone were to look at the repo, they can look at the main branch and know that it is fully functional. 

Meanwhile, developers can make multiple commits to their branch and only merge the completed feature into the main branch when they are done. 

Essentially, every time you merge, you create a new version!

### Collaborating with Branches

Branches also enable teammates to work in parallel without getting in the way of each other! 

![](./img/multiple-branches.png)

When multiple teammates merge, it often causes merge conflicts, but this is expected and normal.

## Tips for success

* First, check out our [cheatsheet on Git](../cheatsheets/git-cheatsheet.md)
* Pay attention to the details and take notes! Do not shy away from the messages in your terminal. Seek to understand them and you'll gain so much more confidence.