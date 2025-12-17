# 1. Intro to Programming

**Table of Contents**

* [Setup](0-intro-to-programming.md#setup)
* [Key Terms](0-intro-to-programming.md#key-terms)
* [What is a program?](0-intro-to-programming.md#what-is-a-program)
* [Running a file with Node](0-intro-to-programming.md#running-a-file-with-node)
* [The Console](0-intro-to-programming.md#the-console)
  * [Debunking The Console.log Myth](0-intro-to-programming.md#debunking-the-consolelog-myth)
* [Control Flow](0-intro-to-programming.md#control-flow)
  * [Conditional Statements](0-intro-to-programming.md#conditional-statements)
  * [Functions and Function Calls.](0-intro-to-programming.md#functions-and-function-calls)
  * [Inspecting the Control Flow With Node](0-intro-to-programming.md#inspecting-the-control-flow-with-node)
* [Code Style and Readability](0-intro-to-programming.md#code-style-and-readability)

## Setup

* In your `unit-1` folder, create a new folder called `1-intro-to-programming`
* `cd 1-intro-to-programming`
* `touch index.js`
* Open the `index.js` file

## Key Terms

* A **program** is a text file with instructions to accomplish some task.
* **Comments** are ignored and help to explain the code.
* **Expressions** are raw data values or the results of operations. Expressions on their own do nothing.
* **Statements** change the program.
  * Creating a variable increases the memory used by the program.
  * `if` statements change the "control flow" of a program by skipping lines of code.
* **Node** is a program that interprets and executes JavaScript code.
* `console` is a built-in object with functions that print text to the Terminal output. A program can run without any console statements, you just won't see any output in the Terminal.
* **Control Flow** is the order in which lines of code are executed.
* **Code Style** refers to the formatting of the code in a way that makes it easier to read and understand. Refer to the [style guide](https://marcylabschool.gitbook.io/marcy-lab-school-docs/cheatsheets/style-guide) for more details.

## What is a program?

A program is a text file with instructions to accomplish some task. It will be made up of comments, expressions, and statements:

*   **Comments** are ignored and help to explain the code.

    ```js
    // this is a comment
    ```
*   **Expressions** are raw data values or the results of operations. Expressions on their own do nothing.

    ```js
    // index.js
    5 + 5
    'hello'
    true
    ```
*   **Statements** change the program.

    \{% code title="index.js" lineNumbers="true" %\}

    ```js
    // creating a variable increases the memory/storage used by the program
    let instructor = 'ben';
    let mood;

    // if statements change the "control flow" of a program (which line of code comes next)
    if (instructor !== 'ben') {
      mood = 'sad';
    } else {
      mood = 'happy';
    }
    ```

    \{% endcode %\}

**Additional Notes on Expressions:**

* References to variables are also expressions, since they retrieve the raw data held inside of them.
* Expressions can be composed of multiple smaller expressions! For example, the `instructor` and `'ben'` expressions are combined using the `!==` operator to create an expression that returns the value `false`

## Running a file with Node

When we want to run the code in a file, we use a piece of software called **Node**.

> Node is a program for interpreting and executing JavaScript code

Node is installed on your computers and can be activated in the Terminal using the `node [filename]` command:

```sh
# index.js is the name of the file we want to run
node index.js
```

**Question:** Why are we not seeing anything when we run this file?

## The Console

`console.log()` is a built-in function that prints text to the Terminal output.

```js
console.log("hello world!");
```

{% hint style="info" %}
The word "console" can often be used interchangeably with "terminal" and to "log" means to write something down.
{% endhint %}

Every language has a way to print text to the Terminal:

```python
# run a .py file like this: 
# python3 index.py
print('hello')
```

### Debunking The Console.log Myth

{% hint style="danger" %}
Myth: "If our program doesn't print anything to the screen, then it isn't working or it isn't doing anything!"
{% endhint %}

It is common to think that without `console.log()`, the program isn't doing anything. This is not the case!

This program below runs a loop one billion times! Even if you don't see any output, notice that it takes a moment for it to finish running:

```js
let x = 0;

/* A loop with 1 billion iterations will actually take a second to run! Increase that number to 10 billion and it could take a minute or more. */
for (let i = 0; i < 1000000000; i++) {
  x++;
}
```

Your computer IS executing the instructions you give it, but you just can't see the results because there is no `console.log()` statement. In fact, the `console` has even more uses than just printing out a message:

```js
let x = 0;

console.log("start");
console.time("loop timer");

/* A loop with 1 billion iterations will actually take a second to run! Increase that number to 10 billion and it could take a minute or more. */
for (let i = 0; i < 1000000000; i++) {
  x++;
}

console.log("stop");
console.timeEnd("loop timer");
```

As you can see, `console` is actually an object with functions inside (a.k.a "methods") that can each be used to print various pieces of information to the Terminal.

## Control Flow

> **Control Flow** refers to the order in which code is executed.

The default control flow is top to bottom, with every line of code being executed in order.

```js
console.log('1');
console.log('2');
console.log('3');
```

### Conditional Statements

`if` statements change the control flow of a program, causing certain lines to be skipped.

```js
let instructor = 'ben';
let mood;

// if statements change the "control flow" of a program (which line of code comes next)
if (instructor !== 'ben') {
  mood = 'sad'; // this is skipped
} else {
  mood = 'happy'; // this line of code is executed next
}
```

### Functions and Function Calls.

**Functions** are containers for statements.

A **function call** statement also changes the control flow by "activating" the function. Calling a function effectively means: "execute the first line of code in the function next".

{% code title="index.js" lineNumbers="true" %}
```js
// functions are containers for statements. we've created the function, but haven't activated it yet
const say = (speaker, message) => {
  console.log(`${speaker}: ${message}`);
  return; // often, a blank return statement like this is omitted
};
 
// call the functions - activate the function, tell it start
say("ben", "hi Fall 2024")
say("Fall 2024", "hi ben")
```
{% endcode %}

The `return` statement "returns" the control flow to the line of code that called the function. The `return` statement can be omitted if no value is returned but technically its still there (otherwise the program wouldn't know to go back to the function call!).

**Question:** What is the control flow of the code above? (Describe it using line numbers.)

### Inspecting the Control Flow With Node

You can view the exact control flow of a program by using the built in VS Code **Run and Debug panel**.

![The run and debug panel is found on the left side of the screen](../.gitbook/assets/0-debugger-panel.png)

Once you open the panel, open a `.js` file and add a `debugger;` statement wherever you would like to inspect your code. Then, click "Run and Debug" and select the "Node" configuration.

![The debugger will pause execution at the debugger statement.](../.gitbook/assets/0-debugger-running.png)

At the top, you will see the debugger control panel where you can control the execution of the program.

![The debugger provides various buttons to control the execution of the program.](../.gitbook/assets/0-debugger-explanation.svg)

## Code Style and Readability

**Readability** is how easy it is for another engineer to read and understand your code (including future you).

Indentation shows the scope of each line of code. Without it, it is harder to read and understand.

```js
const canVote = (age) => {
if (age >= 18) {
return true;
} else {
return false;
}
}
```

Whenever a block of code is created with curly braces `{}`, we indent the code inside using the Tab key:

```js
const canVote = (age) => {
  if (age >= 18) {
    return true;
  } else {
    return false;
  }
}
```

**Question:** How would you fix the code style of this function?

```js
const sayTheTime = (time) => {
if (time <= 12) {
  console.log("Good morning");
        } else if (time <= 18) {
  console.log("Good afternoon");
} else {
  console.log("Good evening");
}
}
```
