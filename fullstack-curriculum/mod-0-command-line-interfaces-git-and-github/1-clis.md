# Command Line Interfaces

- [Slides](#slides)
- [Overview](#overview)
- [Terminal vs. Finder/Explorer](#terminal-vs-finderexplorer)
- [Using the Terminal in VS Code](#using-the-terminal-in-vs-code)
- [Commands](#commands)
  - [Printing Directory Information with `pwd` and `ls`](#printing-directory-information-with-pwd-and-ls)
  - [Navigating Between Directories with `cd`](#navigating-between-directories-with-cd)
  - [Making Files and Directories with `mkdir` and `touch`](#making-files-and-directories-with-mkdir-and-touch)
  - [Executing JavaScript files with `node`](#executing-javascript-files-with-node)
  - [Terminating a Program with `Control+C`](#terminating-a-program-with-controlc)
- [Challenge](#challenge)

## Slides

{% embed url="https://docs.google.com/presentation/d/1JCyTysddqGIObjgmGS6iLcIcvyyFXKFe6wMVsnSYh64/embed?start=false&loop=false&delayms=3000" %}

## Overview

At the end of the day, a program is just a text file on a computer. So, before we begin programming, we need to learn how programmers create, organize, and otherwise manage the files on their computers. 

In this lesson, we'll learn about the Terminal, a program for interacting with a computer's files and executing programs through a command line interface (CLI).

**Objectives**

You will be able to…
* Understand what a command line interface (CLI) is.
* Compare and Contrast CLIs and Graphical User Interfaces (GUIs)
* Navigate your local file tree
* Create, copy, delete, and move files and directories
* How to run a JavaScript program using your CLI
* Explain what an "argument" is

**Key Terms**

* **Terminal** — A program for interacting with a computer's files and executing programs through a command line interface. 
* **Command Line Interface** — a type of user interface (UI) that let's a users perform actions by entering text-based commands.
* **Graphical User Interface** — a type of user interface that uses visual elements such as icons, buttons, windows, and dialog boxes, allowing users to perform actions such as clicking, drag-and-drop, and more.
* **Directory** — Another term for a "folder" in your computer that contains references to files or possibly other directories.
* **Command** - A single action to be performed on your computer. Examples include creating a new file, listing the contents of the current directory, navigating to a different directory, or executing a program.
* **Argument** — An additional piece of information provided to a command to change the command's behavior. 
* **Node** — A program for executing JavaScript code directly on your computer (as opposed to in a browser).

**Important CLI commands**

{% hint style="info" %}
**Note:** In the commands below, argument placeholders will be written like this: `<argument>`. When using these commands, replace the `<argument>` with your desired inputs, making sure to leave out the `<>` as well.
{% endhint %}

* `node <file_name.js>` — execute a given `.js` file using Node.
* `Control + C` — Terminate the currently running program
* `pwd` — Print the working directory, a.k.a where your terminal navigation currently is located.
* `ls` — Prints ("lists") the contents of the working directory
* `cd <directory>` — Change directories to the given directory
* `cd ../` — Change directories to the parent of the working directory
* `mkdir <dir_name>` — Make a new directory with the given name.
* `touch <file_name>` — Make a new file with the given name
* `cp <file> <dest>` — Move a file to the given directory

## Terminal vs. Finder/Explorer

As a programmer, you must become a master of the files on your computer. If you've ever dug into your computer's file system, you likely have done so using a program like Finder or Windows Explorer.

![The finder program](./img/finder.png)

These applications allow you to manage files through a GUI (graphical user interface) — a user interface with buttons and icons that let you do things like click and drag-and-drop. 

While these features are very beginner-friendly, they are often too slow for "power users" who want to perform more complex tasks or rapidly perform many repeated tasks.

Instead, programmers typically use a tool called the Terminal. The Terminal is a program for interacting with a computer's files and executing programs through a command line interface (CLI).

![The Terminal program](./img/terminal-cli.png)

In the screenshot above, you can see this user entering commands:
1. `ls` — list the contents of the current folder (a.k.a **directory**)
2. `cd Documents` — change directories and move into the `Documents` directory
3. `ls` — list the contents of the `Documents` directory
4. `mkdir 2024-fall` — make a new directory inside of `Documents` called `2024-fall`
5. `cd 2024-fall` — change directories and move into the `2024-fall` directory
6. `pwd` — prints the full filepath to the current "working" directory
7. `touch hello.txt` — create a new file called `hello.txt` inside of `2024-fall`
8. `ls` — list the contents of the `2024-fall` directory

**<details><summary style="color: purple">Q: Why use The Terminal?? It would be wayyy faster to do this in the Finder</summary>**
> For this particular task it might be faster to use a GUI file manager like Finder, however there are many tasks where a CLI like the Terminal can outpace a GUI like Finder. In addition, there are some things that Finder simply can't do, like execute files with code.
</details><br>

## Using the Terminal in VS Code

While you can use the Terminal application that comes with your laptop, it is often just as convenient to use the one that comes built into your VS Code code editor.

To open up the Terminal panel, go to **File** > **Terminal** and it should show up at the bottom:

![](./img/vscode-terminal.png)

## Commands

Let's go through some of the most important and commonly used commands.

### Printing Directory Information with `pwd` and `ls`

A **directory** is another term for a "folder" in your computer's file system that contains references to files or possibly other directories.

The **working directory** is the current location of your terminal's navigation through that file system. Think of it as the "you are here" icon in a map.

The `pwd` command prints the full file path to the working directory while the `ls` command prints the contents of the working directory:

![](./img/ls.png)

{% hint style="info" %}
**Note:** In computing, most actions fall into one of the four categories called CRUD: **c**reating, **r**eading, **u**pdating, or **d**eleting data. Which of these actions do you think `pwd` and `ls` are?
{% endhint %}

### Navigating Between Directories with `cd`

The `cd <directory>` command allows you to move to another directory in the file system. However, unlike the previous commands, it requires an **argument**. 

An **argument** is an additional piece of information that changes that behavior of a given command. For the `cd` command, we have to also provide a destination.

For example, suppose we were located in the `/Users` directory inside the following file system:

![](./img/file-structure.png)

I could navigate to "down" to the `/Users/smith` directory with the command:

```
cd smith
```

You can also extend the directory provided with a `/` to quickly navigate to directories within directories. For example, if I were located in the `/Users` directory, I could navigate to the `/Users/smith/Documents` directory in one command:

```
cd smith/Documents
```

To navigate back "up" to the "parent" directory, you can use the special directory name `..` which always refers to the parent directory of the working directory.

For example, if I were in the directory `/Users/smith/Documents` and wanted to go back up to the `/Users/smith` directory, I could enter the command:

```
cd ..
```

If I had wanted to go up two levels from `/Users/smith/Documents` to `/Users`, I can again use `/` to extend the provided directory name:

```
cd ../..
```

**<details><summary style="color: purple">Suppose I were located in the `/Users/smith/Documents` directory, how could I navigate to the `/Users/jones/Desktop` directory in one `cd` command?</summary>**
> ```
> cd ../../jones/Desktop
> ```
</details><br>

### Making Files and Directories with `mkdir` and `touch`

`mkdir <dir_name>` creates a new directory in the working directory

`touch <file_name>` creates a new file in the working directory. Make sure to include the file extension!

`cp <file_name> <dir_name>` copies a file into a directory

### Executing JavaScript files with `node`

A JavaScript program is any file with a `.js` extension, like `hello.js`

The code can be as simple as `console.log("Hello World")`

To run the program, use the command `node hello.js`

### Terminating a Program with `Control+C`

Many programs will end ("terminate") on their own when each statement has been executed.

Other programs can run forever, requiring us to stop them ourselves. For example, the code below will run forever if we put it in our JavaScript program:

```js
while (true) {
  // this is an infinite loop!
}
```

To terminate the program, use the keyboard shortcut `Control+C`

## Challenge

Create this file structure using the command line.

![](./img/cli-filestructure-challenge.png)

**<details><summary style="color: purple">Q: If I were in the hobbies folder, how would you navigate to the code folder?</summary>**
> `cd ../code`
</details><br>