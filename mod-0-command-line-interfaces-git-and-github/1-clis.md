# Command Line Interfaces

At the end of the day, a program is just a text file on a computer. So, before we begin programming, we need to learn how programmers create, organize, and otherwise manage the files on their computers.

In this lesson, we'll learn about the Terminal, a program for interacting with a computer's files and executing programs through a command line interface (CLI).

You will be able to…

* Understand what a command line interface (CLI) is.
* Compare and Contrast CLIs and Graphical User Interfaces (GUIs)
* Navigate your local file tree
* Create, copy, delete, and move files and directories
* How to run a JavaScript program using your CLI
* Explain what an "argument" is
  
  
**Table of Contents:**
- [Key Terms \& Commands](#key-terms--commands)
- [The File Tree](#the-file-tree)
  - [Applications for Viewing the File Tree (CLI vs. GUI)](#applications-for-viewing-the-file-tree-cli-vs-gui)
  - [Using the Terminal in VS Code](#using-the-terminal-in-vs-code)
- [Essential Commands](#essential-commands)
  - [Looking at the Working Directory with `pwd` and `ls`](#looking-at-the-working-directory-with-pwd-and-ls)
  - [Navigating Between Directories with `cd`](#navigating-between-directories-with-cd)
    - [Be Careful when using the `cd` command!](#be-careful-when-using-the-cd-command)
  - [Making Files and Directories with `mkdir` and `touch`](#making-files-and-directories-with-mkdir-and-touch)
  - [Executing JavaScript files with `node`](#executing-javascript-files-with-node)
    - [Terminating a Program with `Control+C`](#terminating-a-program-with-controlc)
- [Additional Commands](#additional-commands)
  - [Unfinished Double Quotes, `echo`, and `>>`](#unfinished-double-quotes-echo-and-)
  - [The `cat` Command and Combining Commands with `&&`](#the-cat-command-and-combining-commands-with-)
  - [Removing, Renaming, Moving, and Copying](#removing-renaming-moving-and-copying)
- [Challenges](#challenges)

## Key Terms & Commands

**Key Terms**

* **Terminal** — A program for interacting with a computer's files and executing programs through a command line interface.
* **Command Line Interface** — a type of user interface (UI) that let's a users perform actions by entering text-based commands.
* **Graphical User Interface** — a type of user interface that uses visual elements such as icons, buttons, windows, and dialog boxes, allowing users to perform actions such as clicking, drag-and-drop, and more.
* **Directory** — Another term for a "folder" in your computer that contains references to files or possibly other directories.
* **Working Directory** — The directory where your commands will be executed.
* **Command** - A single action to be performed on your computer. Examples include creating a new file, listing the contents of the current directory, navigating to a different directory, or executing a program.
* **Argument** — An additional piece of information provided to a command to change the command's behavior.
* **Node** — A program for executing JavaScript code directly on your computer (as opposed to in a browser).

**Important CLI commands**

{% hint style="info" %}
**Note:** In the commands below, argument placeholders will be written like this: `[argument]`. When using these commands, replace the `[argument]` with your desired inputs, making sure to leave out the `[]` as well.
{% endhint %}

```sh
# Run a given `.js` file using Node.
node [filename.js]

# Terminate the currently running program
Control + C

# Print the working directory, a.k.a where your terminal navigation currently is located.
pwd

# Prints ("lists") the contents of the working directory
ls    

# Change directories to the given subdirectory
cd [subdirectory]

# Change directories to the parent of the working directory
cd ../

# Make a new directory with the given name.
mkdir [subdirectory]

# Make a new file with the given name
touch [filename]

# Move a file to the given directory
cp [file] [dest]
```

## The File Tree

The files and folders in your computer are organized in a tree-like structure called the **File Tree**.

![](img/file-structure.png)

We refer to each folder in the file tree as a **directory**. The **root directory** is the top-most folder that contains all other **sub-directories**.

**<details><summary>Q: What are the ways that the file tree is tree-like?</summary>**

The root is like the trunk of the tree and each sub-directory is a branch that can have more branches.

</details>

### Applications for Viewing the File Tree (CLI vs. GUI)

Most operating systems have an application that lets you view the device's file tree. For example, on MacOS there is Finder and on Windows there is Explorer:

![The finder program](img/finder.png)

These applications allow you to manage files through a **graphical user interface (GUI)** — a user interface with buttons and icons that let you do things like click and drag-and-drop.

These GUIs are examples of an "abstraction"—a layer that hides complexity—and GUIs present a tradeoff:
* GUIs are easy to use and easy to learn for beginners
* What you can do with GUIs is limited to how they were designed

When we want more fine-tuned control over how we interact with our computer's files, we can turn to a **command line interface (CLI)**, often called the **Terminal**. The Terminal is a program for interacting with a computer's files by executing typed-in commands:

![The Terminal program](img/1-cli-terminal.png)

In the screenshot above, you can see this user entering commands:

```sh
pwd
ls
mkdir unit-5
ls
ls unit-0
cd unit-0
touch bye.txt hey.txt
ls
echo "hello world"
echo "hello world" >> output.txt
ls && cat output.txt
rm hey.txt && ls
mv bye.txt goodbye.txt && ls
```

Note that some commands like `pwd` can be entered on their own. Other commands like `mkdir` may use inputs called **arguments**. Some commands like `ls` can be used on their own or with arguments.

<details>

<summary><strong>Q: Why use The Terminal?? It would be wayyy faster to do this in the Finder</strong></summary>

For this particular task it might be faster to use a GUI file manager like Finder, however there are many tasks where a CLI like the Terminal can outpace a GUI like Finder.

For example, try this command below to create 8 folders at once!

```sh
mkdir unit_{0..7}
```

In addition, there are some things that Finder simply can't do, like execute files with code.

</details>

### Using the Terminal in VS Code

While you can use the Terminal application that comes with your laptop, it is often just as convenient to use the one that comes built into your VS Code code editor.

To open up the Terminal panel, go to **File** > **Terminal** and it should show up at the bottom:

![Use the keyboard shortcut Control+\` to open/close the Terminal](img/vscode-terminal.png)

## Essential Commands

Let's go through some of the most important and commonly used commands.

### Looking at the Working Directory with `pwd` and `ls`

In the terminal, you can only interact with one directory at a time, the **working directory**.

Think of it as the "you are here" icon in a map.

![The working directory is your current location in your file tree.](./img/you-are-here.png)

The `pwd` command prints the full file path to the working directory while the `ls` command prints the contents of the working directory:

![](img/ls.png)

{% hint style="info" %}
**Note:** In computing, all actions that interact with data fall into one of the four categories called CRUD: **c**reating, **r**eading, **u**pdating, or **d**eleting data. 

Which of these actions do you think `pwd` and `ls` are?
{% endhint %}

### Navigating Between Directories with `cd`

The `cd [directory]` command allows you to move to another directory in the file system. However, unlike the previous commands, it requires an **argument**.

An **argument** is an additional piece of information that changes that behavior of a given command. For the `cd` command, we have to also provide a destination.

{% hint style="info" %}
Use the Tab key to autocomplete commands and filenames! Just start typing and hit Tab to autocomplete.
{% endhint %}

For example, suppose we were located in the `/Users` directory inside the following file system:

![The file tree](img/file-structure.png)

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

<details>

<summary><strong>Suppose I were located in the <code>/Users/smith/Documents</code> directory, how could I navigate to the <code>/Users/jones/Desktop</code> directory in one <code>cd</code> command?</strong></summary>

```
cd ../../jones/Desktop
```

</details>

#### Be Careful when using the `cd` command!

Using the `cd` command on its own will send you to the root of your entire file system (`~/`). This is the equivalent of using the command:

```sh
cd ~
```

### Making Files and Directories with `mkdir` and `touch`

`mkdir [dir_name]` creates a new directory in the working directory

`touch [file_name]` creates a new file in the working directory. Make sure to include the file extension!

You can also create multiple files/directories at once by listing multiple file/directory names:

```sh
touch file1.txt file2.txt
mkdir dir1 dir2 dir3
```

### Executing JavaScript files with `node`

A JavaScript program is any file with a `.js` extension, like `hello.js`

The code can be as simple as `console.log("Hello World")`

To run the program, use the command `node hello.js`

#### Terminating a Program with `Control+C`

Many programs will end ("terminate") on their own when each statement has been executed.

Other programs can run forever, requiring us to stop them ourselves. For example, when we use the `node` command on its own, it will start the **Node REPL (Read, Evaluate, Print Loop)** program which just waits for Javascript input, executes it, and then prints the result:

![The Node REPL is useful for testing out expressions.](img/1-node-repl-expressions.png)

To terminate the program, use the keyboard shortcut `Control+C` (you may need to cancel twice).

## Additional Commands

### Unfinished Double Quotes, `echo`, and `>>`

Another common occurrence is an unfinished string. You can test this with the `echo` command which will print a given string straight to the terminal.

![An unfinished double quote will be produce dquote> in the Terminal, waiting for you to finish the string.](img/1-cli-echo-unfinished-dquote.png)

You can also use the output from an `echo` command and send the output into another file using the `>>` append operator.

For example, this command will take the text `"hello world"` and append it to the file `output.txt` (it will create `output.txt` if it doesn't exist)

```sh
echo "hello world" >> output.txt
```

### The `cat` Command and Combining Commands with `&&`

The `cat` command is used to read the contents of a given file.

You can combine any two commands with the `&&` operator.

```sh
ls && cat output.txt
```

For example, the command above lists the contents of the current working directory and print the contents of `output.txt`

### Removing, Renaming, Moving, and Copying

`rm [file_name]` removes a file from the working directory

`rm -r [directory_name]` removes a directory and all of its contents from the working directory. `-r` stands for "recursive".

`mv [file_name] [new_file_name]` renames a file in the working directory or allows you to move the file to another directory.

`cp [file_name] [dir_name]` copies a file into a directory.

## Challenges

Check out these [awesome challenges](https://github.com/mssalvatore/command-line-challenges) for additional practice. A lot of these challenges go way beyond what we've learned in this lesson and will push you to research and learn on your own!

We're using Linux commands so whenever you are researching a topic, add "Linux" to the end of your search so that you get appropriate results! For example, for the first challenge you may search **"how to unzip .tar.gz file linux"**.

Challenges 1-19 are the basics of what you'll want to know for this course.

Challenges 20 you should skip. It involves executing a file that we can't see the contents of.

Challenges 21-50 are more advanced.
