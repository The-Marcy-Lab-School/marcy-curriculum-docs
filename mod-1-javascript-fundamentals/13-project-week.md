# CLI Projects

- [Overview](#overview)
- [Project Options](#project-options)
- [Tips for Success](#tips-for-success)
- [Setup Instructions](#setup-instructions)
- [🎮 Project 1: Rock–Paper–Scissors CLI Game](#-project-1-rockpaperscissors-cli-game)
  - [Key Features to Implement:](#key-features-to-implement)
  - [Data Structure](#data-structure)
  - [Stretch Features to Consider:](#stretch-features-to-consider)
- [🛒 Project 2: Shopping List CLI App](#-project-2-shopping-list-cli-app)
  - [Key Features to Implement:](#key-features-to-implement-1)
  - [Data Structure](#data-structure-1)
  - [Stretch Features to Consider:](#stretch-features-to-consider-1)
- [🧠 Project 3: Quiz Game CLI](#-project-3-quiz-game-cli)
  - [Key Features to Implement:](#key-features-to-implement-2)
  - [Data Structure](#data-structure-2)
  - [Stretch Features to Consider:](#stretch-features-to-consider-2)
- [Project Grading](#project-grading)
  - [Technical Details (15 points)](#technical-details-15-points)
  - [Reflection (2 points)](#reflection-2-points)
  - [Professionalism \& Craftsmanship (5 points)](#professionalism--craftsmanship-5-points)

## Overview

Your task is to create an interactive command-line application using Node.js. This project will demonstrate your understanding of fundamental JavaScript concepts and your ability to organize code into a well-structured application.

Your project should be something you can complete in just a few days while showcasing the core programming concepts we've covered.

## Project Options

Choose **one** of the following three projects to complete. They are ordered by difficulty, so pick the one that matches your comfort level and goals:

**🎮 Project 1: Rock–Paper–Scissors** (Simplest, but still a challenge!)
Build a classic game where users play against the computer with score tracking across multiple rounds.

**🛒 Project 2: Shopping List Manager** (Similar to the task manager but with more complexity)
Create a CRUD application where users can add, remove, and view items in their shopping list through a menu system.

**🧠 Project 3: Quiz Game** (The most challenging and new!)
Develop a multiple-choice quiz application with questions, scoring, and replay functionality.

## Tips for Success

- **Leverage The Case Study**: Make use of the [Task Manager Case Study](https://github.com/The-Marcy-Lab-School/swe-casestudy-1-cli-task-manager) as a guide for building your project.
- **Refer to the Requirements**: Ensure that your project meets all of the requirements listed at the bottom of this page before submitting.
- **Start Simple**: Get basic functionality working first, then add features
- **Test Frequently**: Run your code often to catch issues early
- **Plan Your Functions**: Think about what each function should do before coding
- **Handle Edge Cases**: What happens if a user enters invalid input?
- **Be Creative**: Add your own questions, features, or improvements!
- **Use Meaningful Names**: Your variable and function names should explain their purpose

*Remember: The goal is to demonstrate your understanding of JavaScript fundamentals while building something functional and fun!*

## Setup Instructions

_Complete these steps after you have chosen your project!_

1. Create a new folder with a descriptive name for your project.
2. Run `npm init -y`.
3. Install `prompt-sync` with `npm install prompt-sync`.
4. Add, commit, and push all files with the commit message `"init"`
5. Create a feature branch called `development` where you will create new features: `git checkout -b development`
6. Begin Coding!

When you have completed a feature, have tested it in your branch, and are ready to merge it into the main branch, use the following commands:

```sh
# switch to the main branch
git checkout main

# merge the feature branch into main
git merge development

# add, commit, and push with a descriptive message
git add -A
git commit -m "merging [feature description]"
git push

# return to the feature branch
git checkout development
```

## 🎮 Project 1: Rock–Paper–Scissors CLI Game

**Objective:**
Build a simple Rock–Paper–Scissors game where the user plays against the computer. The program should keep score across multiple rounds until the player decides to quit.

### Key Features to Implement:

* When a user starts the application, they are greeted with a nice message and are presented with a menu of options:
  ```
  Menu:
  1. Play Round
  2. View Stats  
  3. Exit

  Choose an Action (Enter 1-3): [user enters their choice here]
  ```
* The user can enter a number, 1 through 3, to select their next action.
* When playing, the user can select their choice by typing "rock", "paper", or "scissors" (case-insensitive).
  ```
  Choose a move (rock, paper, or scissors): [user enters their choice here]
  ```
* The computer responds by randomly selecting "rock", "paper", or "scissors".
* The game compares the choices and determines the winner based on standard Rock-Paper-Scissors rules (rock beats scissors, scissors beats paper, paper beats rock).
  * After each round, the game displays the choices and the result:
    ```
    You chose: rock
    Computer chose: scissors
    Rock beats scissors! You win!
    ```
* When viewing stats, the user can see games won, games lost, games tied, total games played, and win rate as a rounded percentage.
  ```
  Current Statistics:
  Games Won: 2
  Games Lost: 1
  Games Tied: 0
  Total Games: 3
  Win Rate: 67%
  ```
* The game continues until the user chooses to quit, then displays final statistics.


### Data Structure

Use an **object** to store game data. The object should look like this:

```js
{
  wins: 2,
  losses: 3,
  ties: 1
}
```

### Stretch Features to Consider:

* Add a "best of X rounds" mode where the game continues until someone wins a certain number of rounds
* Add sound effects or ASCII art for different choices (rock, paper, scissors)
* Track and display win streaks and longest winning/losing streaks
* Allow saving/loading game statistics to/from a JSON file using the `fs` module

## 🛒 Project 2: Shopping List CLI App

**Objective:**
Create a CLI shopping list manager where users can add, remove, and view items through a menu system.

### Key Features to Implement:

* When a user starts the application, they are greeted with a nice message and are presented with a menu of options:
  ```
  Menu:
  1. Add Item
  2. Remove Item  
  3. View List
  4. Exit

  Choose an Action (Enter 1-4): [user enters their choice here]
  ```
* The user can enter a number, 1 through 4, to select their next action.
* When adding an item to the list, the user can specify the `name` of the item, the `quantity`, and the `price`. The user should then see a message summarizing the items they have added.
  ```
  Enter item name: apples
  Enter quantity: 5
  Enter price per item: 0.50
  Added 5 apples at $0.50 each to your shopping list!
  ```
* When removing an item from the list, the user can specify the `name` of the item (case insensitive) and the `quantity` to remove. For example, if a user wants to remove two apples, the user interaction might look like this:
  ```
  Enter item name to remove: apples
  Enter quantity to remove: 2
  Removed 2 apples from your shopping list!
  ```
* When viewing the list, the user can see the total quantity of items in the list and the total price.
  ```
  Your Shopping List:
  - 3 apples: $1.50
  - 2 bread: $4.00
  - 1 milk: $3.50
  
  Total Items: 6
  Total Price: $9.00
  ```
* After selecting an action, the application displays a message to confirm the completion of their action.
* After selecting an action, the menu is shown again to the user and they are prompted to choose their next action.

### Data Structure

Use an **array of objects** to store shopping items. Each item should be an object with a name, quantity, and price, this: 

```js
[
  {
    name: "apples",
    quantity: 3,
    price: 1.5
  },
  {
    name: "carton of 12 eggs",
    quantity: 1,
    price: 7.99
  },
]
```

### Stretch Features to Consider:

* Add an option to sort. Let the user choose between `price`, `quantity`, and `name`
* Add a `category` to each item and add it as an option for sorting
* Add a budget feature that warns when total cart value exceeds a set amount
* Allow saving/loading shopping lists to/from a JSON file using the `fs` module
* Add a "shopping mode" that lets users check off items as they shop
* Add support for multiple shopping lists (e.g., groceries, hardware, gifts)

## 🧠 Project 3: Quiz Game CLI

**Objective:**
Build a multiple-choice quiz game where users answer questions, get feedback, and see their score at the end.

### Key Features to Implement:

* When a user starts the application, they are greeted with a nice message and presented with a menu of options:
  ```
  Menu:
  1. Start Quiz
  2. View High Scores
  3. Exit

  Choose an Action (Enter 1-3): [user enters their choice here]
  ```
* The user can enter a number, 1 through 3, to select their next action.
* When starting a quiz, the application presents questions one at a time with multiple choice options. Each question displays the question text and numbered choices for the user to select from. For example:
  
    ```
    Question 1: What is 2 + 2?
    1) 3
    2) 4
    3) 5
    4) 6
    
    Enter your answer (1-4): [user enters their answer here]
    ```

* After answering each question, the user receives immediate feedback on whether their answer was correct.
  ```
  Correct! Well done!
  Current Score: 2/3 (67%)
  ```
* At the end of the quiz, the user sees their final score and percentage (rounded) of correct answers.
  ```
  Quiz Complete!
  Final Score: 4/5 (80%)
  Thanks for playing!
  ```
* If the user's score is in the top 5, they are prompted again to enter their name to add their score to the high score leaderboard.
  ```
  Congratulations! You scored a high score!
  Enter your name: [user enters their name here]
  ```

* When viewing high scores, users can see the top 5 scores and a `Date` timestamp when the quiz was completed:

  ```
  Highscores:
  1. 100 (ben) — 10/1/25
  2. 90 (motun) — 10/2/25
  3. 80 (carmen) — 10/10/25
  4. 80 (gonzalo) — 10/29/25
  5. 70 (reuben) — 10/19/25
  ```

* After completing an action, the menu is shown again and the user is prompted to choose their next action.

### Data Structure

Use an **array of objects** to store quiz questions. Each question should be an object with a `question` string, a `choices` array, and an `answerIndex` indicating the index of the answer in `choices`, like this: 

```js
[
  {
    question: "What is 2+2",
    choices: ["4", "22", "0", "40"],
    answerIndex: 0
  },
  {
    question: "What is 5*6",
    choices: ["3", "56", "30", "11"],
    answerIndex: 2
  },
]
```

In addition, use a separate **array of objects** to store high score history. Each score should be an object with a `name`, a `score`, and a `date`, like this:

```js
[
  { name: 'ben', score: 100, date: "10/1/25" },
  { name: 'motun', score: 90, date: "10/2/25" },
  { name: 'carmen', score: 75, date: "10/10/25" },
]
```

### Stretch Features to Consider:

* Add different categories of questions (math, science, history, etc.) and let users choose their preferred category
* Implement a difficulty system with easy, medium, and hard questions
* Add a timer for each question to create a more challenging experience
* Allow saving/loading quiz results and high scores to/from a JSON file using the `fs` module

## Project Grading

We care deeply about the quality of the code that is produced at Marcy. Even if your application works as described, was it created following best practices? Did you effectively use all of the skills from the module? Were you able to learn and grow from this project-building process?

To ensure that your project meets Marcy's standards of excellence, your project will be assessed on the following criteria (22 points total):
* Technical Details (15 points)
  * Core JavaScript Concepts (7 points)
  * Code Organization (4 points)
  * User Interaction (4 points)
* Reflection (2 points)
* Professionalism & Craftsmanship (5 points)

### Technical Details (15 points)

Technical details are broken up further into three groups:

**Core JavaScript Concepts (7 points)**
1. Variables: Uses clear, descriptive names that explain what data they hold
2. Functions: Breaks code into multiple functions that each handle specific tasks
3. Conditionals: Includes at least one `if/else` statement that changes behavior based on user input
4. Loops: Uses at least one `while` loop to repeatedly prompt the user
5. String Methods: Uses string methods (like `.toLowerCase()`, `.trim()`, etc.) to validate user input
6. Higher Order Array Methods: Uses at least one array higher-order method (like `.forEach()`, `.filter()`, `.reduce()`, etc...)
7. Objects: Uses at least one object to organize related data together

**Code Organization (4 points)**
1. Split your code into **at least 3 separate modules** (files)
2. Use `require()` and `module.exports` to connect your modules
3. Follows a logical file structure:
  - `index.js` — the entry point of the application that displays a menu to the user.
  - `menu.js` — handles the main menu loop and handles user input.
  - A data layer (choose a name!) — handles the logic related to managing the data for your application.
4. A `package.json` file exists and includes dependencies for the project

**User Interaction (4 points)**
1. Creates an interactive experience that responds to user choices using the `prompt-sync` package
2. Provides warning messages when invalid input is provided and allows the user to try again.
3. Provides confirmation messages when user actions are completed.
4. All messages printed to the console have consistent spacing and are free of spelling and grammar mistakes.

### Reflection (2 points)
In `REFLECTIONS.md`, respond to the prompts below. Your responses should each contain about 150-250 words and demonstrate thoughtful reflection.

  1. Which project requirement did you find most challenging and how did you overcome this challenge? Be specific in your response.
  2. Share one technical concept that you gained a deeper understanding of through building this project. Explain that concept in simple terms and explain how it is used in your project.

### Professionalism & Craftsmanship (5 points)
1. Code is functional and free of bugs. Does not throw errors when used under expected circumstances.
2. Code is well-organized, spacing is consistent, variable/function names are descriptive, comments are concise and purposeful, and no unused code remains.
3. Commits are small, frequent, and have meaningful commit messages.
4. A branch is used for development while the `main` branch is always functional
5. The `README.md` includes setup instructions, usage examples, and clear explanation of functionality. All writing in the `README.md` file and the `REFLECTION.md` file is polished and free of typos and grammatical errors.