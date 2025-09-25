# Git & GitHub

In this lesson, we'll learn how software engineers manage the changes to their project using a tool called Git. We'll also they can back up and share their projects online using the closest thing to a social network for programmers, GitHub.

You will be able to…

* Develop mental models for repositories, Git, and GitHub
* Differentiate between local and remote repos
* Create a remote repository on Github
* Know the steps of the Git workflow

**Table of Contents**
- [Setup](#setup)
- [Key Terms and Commands](#key-terms-and-commands)
- [Mental Model: Git \& GitHub](#mental-model-git--github)
  - [What is Git?](#what-is-git)
  - [What is GitHub?](#what-is-github)
- [The Git Workflow](#the-git-workflow)
  - [`git init` and `git status`](#git-init-and-git-status)
  - [`git add`, `git commit`, and `git log`](#git-add-git-commit-and-git-log)
- [The GitHub Workflow](#the-github-workflow)
  - [1. Create A Repository On GitHub](#1-create-a-repository-on-github)
  - [2. Make A Local Clone Of The Repository with `git clone`](#2-make-a-local-clone-of-the-repository-with-git-clone)
  - [3. Use the normal Git Workflow](#3-use-the-normal-git-workflow)
  - [4. Upload local commits to GitHub with `git push`](#4-upload-local-commits-to-github-with-git-push)
- [Summary](#summary)

## Setup

To follow along in this lecture, navigate to your `Development/mod-0` folder in your VS Code terminal and create a new folder inside called `2-git-lecture`.

## Key Terms and Commands

**Key Terms**

* **Repository (or just "repo")** — a centralized location where files are stored and managed. Any folder can be considered a repository.
* **Git** — A "version control system" that allows us to manage the history of changes made to a repo through commits.
* **Commit** — A "snapshot" of the changes made to a repo. A commit is typically created when a key milestone is reached in a project (e.g. a feature is completed).
* **Staging Area** — A place to temporarily store changed files to include in the next commit.
* **Github** — An online host of git repositories with tools for managing git projects and features for collaboration.
* **Local Repository** — A repository stored on a developers computer.
* **Remote Repository** — A repository stored online on a service like GitHub.
* **Clone** — Copy a remote repo's files and commit history and store them locally (creates a local repository)
* **Push** — Send a local repo's commit history to a remote repo to be synchronized.

**Important Git commands**

{% hint style="info" %}
**Note:** In the commands below, argument placeholders will be written like this: `[argument]`. When using these commands, replace the `[argument]` with your desired inputs, making sure to leave out the `[]` as well.
{% endhint %}

```sh
# add git to the current repo
git init            
# check the status of changed files in the repo
git status          
# add the given file to the staging area
git add [filename]  
# add all changed files to the staging area
git add -A          
# creates a new commit from the staged files
git commit -m "[commit message]" 
# upload local commits to the remote (GitHub)
git push            
# download a git repository from the remote (GitHub)
git clone           
```

## Mental Model: Git & GitHub

Imagine you're working on a paper. You've got all of your drafts saved in a folder and it looks something like this:

![](img/my-paper-file-names.png)

**<details><summary>Q: Even though it is messy, what are the benefits of doing this versus just keeping one version of the file?</summary>**

It is easier to see how the file has changed over time and to revert to an older version of the file if necessary.

</details>

### What is Git?

The files of our software projects are changing constantly and we will often want to go back to previous versions of our files. To help manage the versions of our project, software engineers use **Git**.

> **Git** is a tool that takes snapshots of the files in a project and allows developers to manage them and, if needed, revert to prior version. Git is an example of a **version control system**.

Git maintains a history of changes through **"commits"** — snapshots of our code that represent a completed unit of work such as a new feature or bug fix. 

![A commit is a snapshot of our code that represents the completion of a unit of work.](img/commit-history.png)

<details>

<summary><strong>Q: What are the benefits of maintaining a "commit history" like this?</strong></summary>

Developers can revert to previous versions of their project if they need to (for example, if a feature is not working as expected).

</details>

When we use Git for a project, the commits are stored alongside the code files. Together, the files and this commit history form a **Git repository**.

> A **repository (or "repo")** is a centralized location where data is stored and managed. Any folder with files can be considered a repository. A **Git repository** is simply a folder whose files are managed by Git.

### What is GitHub?

![The camera app is like Git. GitHub is where you store the photos online.](./img/person-taking-photo.png)

Think about the camera application on a smartphone. It allows you to take snapshots of the world around you so that you can look back at the history of your life. This is similar to Git (if your camera also allowed you to travel back in time and make changes).

The camera app may also allow you to backup your photos and save them online "in the cloud". This is similar to **GitHub**.

> **GitHub** is a cloud-based "host" for Git repositories. In other words, it stores Git repositories online. GitHub allows developers to store, share and collaborate on their projects.

<figure><img src="img/github.png" alt="GitHub is a social network for developers to backup, share, and collaborate on projects." width="375"><figcaption><p>GitHub is a social network for developers to backup, share, and collaborate on projects.</p></figcaption></figure>

Check out the GitHub repository for [Node](https://github.com/nodejs/node). Here are few things to look for:
* It has ~45000 commits, each with clear messages, and made by many different developers.
* It has dozens of branches, each for a different version of the software.
* The README on the front page gives a detailed overview of the project and instructions for downloading and contributing.
* It has been forked (duplicated) over 30 thousand times

So, you'll use Git and GitHub in tandem to manage your projects. You'll use Git on your own computers to manage the changes to your project and then you will upload those changes to GitHub.

## The Git Workflow

First, let's look at how we can create commits for our project.

Using git in a project involves moving our code between three phases:

1. The working directory (where we are editing files)
2. The staging area (where we temporarily store changes before committing them)
3. The repository (where we store our committed changes)

![](img/local-git.png)

<details>

<summary><strong>Q: What is the point of temporarily storing changes before committing them?</strong></summary>

Check out [this StackOverflow post which does a great job of explaining!](https://stackoverflow.com/questions/49228209/whats-the-use-of-the-staging-area-in-git)

</details>

### `git init` and `git status`

To use Git in a project, we can start by turning the project into a Git repository with the command `git init`. We can then make changes and use `git status` to see what changes have been made:

![](img/git-init-git-status.png)

1. In the Terminal, we navigated to a folder and ran `git init`, turning the folder into a Git repository.
2. The `git status` command can be used at any time to see what changes have been made to the repository. At first, we can see that the repo has no changes.
3. The `echo` command combined with the `>` operator creates a new file called `myfile.txt` with the text `"insert text here"`
4. After the `ls` and `git status` commands, we can see that the `myfile.txt` file has been created.

### `git add`, `git commit`, and `git log`

One we have changes that we want to save in the repository, we use the `git add` command to "stage" the changes and `git commit` to save those changes!

![](img/git-add-git-commit.png)

1. In the Terminal, we use `git add myfile.txt` to move the `myfile.txt` file into the staging area.
2. `git status` shows us that `myfile.txt` is ready to be committed.
3. `git commit -m "added myfile.txt"` saves the changes to the repository with a message describing the change: `"added myfile.txt"`
4. `git status` now shows that all changes have been committed
5. Finally, `git log` shows that our new commit has been added to the commit history!

## The GitHub Workflow

Storing our repos on GitHub involves a few setup steps...

1. Create a new repo on GitHub. We call this a **remote repository**.
2. **Clone** (copy) the repo from GitHub onto our own computer. Now we have a **local repository** that is linked to the remote repository.

After setup, whenever we want to make changes to the repo we:

3. Use the normal git workflow (make changes › `git add` › `git commit`)
4. After making commits on our local repository, we **push** the changes to the remote repository.

![](img/git-github-workflow.png)

Let's practice this:

### 1. Create A Repository On GitHub

Instead of using the `git init` command to create a _local repository_. We're going to start by creating a _remote repository_.

In the upper-right corner of any page, select +, then click **New repository**.

![](img/new-repo.png)

Choose an owner of the repository (you) and give the repository a name.

{% hint style="warning" %}
Make sure to check the **Add a README file** box.
{% endhint %}

Then click **Create repository**.

![](img/new-repo-settings.png)

### 2. Make A Local Clone Of The Repository with `git clone`

After setting up the repo, you should be brought to the repo's page on GitHub. Click on the **Code** button (1), make sure to select **SSH** (2), and then click on the **copy** button (3) to copy the git clone url beginning with `git@github.com:...`.

![](img/new-repo-clone.png)

Then, back in your Visual Studio Code terminal, run the command:

```sh
git clone <pasted git clone url>
```

For example, it could look like this:

```sh
git clone git@github.com:benspector-mls/my-first-repo.git
```

{% hint style="info" %}
Wherever your VC Code terminal is, the repository will be downloaded. If you want to clone the repository to a different location, you need to first change the directory you're in using `cd` before using `git clone`.
{% endhint %}

### 3. Use the normal Git Workflow

Now that you have cloned the repository, you can use the same Git workflow as above:

1. Make edits (for example, edit the `README.md` file)
2. Save your code
3. Use `git add -A` to add ALL changes to the staging area.
4. Use `git commit -m "commit message"` to commit those changes to your local repository

### 4. Upload local commits to GitHub with `git push`

Now here comes the fun part.

![](img/commit-ahead.png)

In the example above, notice how the first `git status` says

```
On branch main
Your branch is up to date with 'origin/main'.
```

But the last `git status` says

```
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)
```

Your local repository (on your computer) and your remote repository (on GitHub) are out of sync because I have a commit that exists locally but not on GitHub.

To upload the local commits to GitHub, run the command `git push`.

![](img/git-push.png)

If we look at the repo on GitHub, we should see the latest commit message and the updated `README.md` file!

![](img/updated-repo-with-commit.png)

For future changes, repeat steps 3 and 4!

## Summary

So, in summary, Git and GitHub are invaluable tools in the toolkit of a software developer that make managing projects and versions easier!

**Git** is a "version control system" that allows us to manage the history of changes made to a repo. Think of the Camera app on your phone documenting the world around you.

**Github** is an online host of git repositories with tools for managing git projects and features for collaboration. Think of where your photos are stored online.

Together, we can manage code in **local repositories** and sync them with **remote repositories**.

![](img/git-github-workflow.png)

The one command in this diagram that we didn't cover is `git pull`. We'll learn more about this in the next lesson!

Happy coding!
