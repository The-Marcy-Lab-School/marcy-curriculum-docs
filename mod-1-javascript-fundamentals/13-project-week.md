# CLI Projects

- [Overview](#overview)
  - [Project Options](#project-options)
  - [Tips / Common Mistakes to Avoid](#tips--common-mistakes-to-avoid)
- [Getting Started](#getting-started)
  - [Setup Instructions](#setup-instructions)
  - [Git Workflow](#git-workflow)
- [Project Grading](#project-grading)
  - [Technical Details (15 points)](#technical-details-15-points)
  - [Reflection (2 points)](#reflection-2-points)
  - [Professionalism \& Craftsmanship (5 points)](#professionalism--craftsmanship-5-points)
- [How to Submit](#how-to-submit)
- [🎮 Project 1: Rock–Paper–Scissors CLI Game](#-project-1-rockpaperscissors-cli-game)
- [🛒 Project 2: Shopping List CLI App](#-project-2-shopping-list-cli-app)
- [🧠 Project 3: Quiz Game CLI](#-project-3-quiz-game-cli)
- [Stuck Getting Started?](#stuck-getting-started)
  - [Phase 1: Get a Menu Working (Start Here!)](#phase-1-get-a-menu-working-start-here)
  - [Phase 2: Create Your Data Structure](#phase-2-create-your-data-structure)
  - [Phase 3: Implement ONE Feature at a Time](#phase-3-implement-one-feature-at-a-time)
    - [Pick your FIRST feature (the easiest one):](#pick-your-first-feature-the-easiest-one)
    - [Now pick your SECOND feature (the main action):](#now-pick-your-second-feature-the-main-action)
    - [Continue until all features work!](#continue-until-all-features-work)
  - [Phase 4: Polish and Validate](#phase-4-polish-and-validate)
  - [Submission Checklist](#submission-checklist)

## Overview

In class, we studied the [Task Manager Case Study application](https://github.com/The-Marcy-Lab-School/swe-casestudy-1-cli-task-manager). For this project, you will use this task manager application as a guide for building something new!

Your task is to create an interactive command-line application using Node.js to demonstrate your understanding of JavaScript fundamentals, your systems-level thinking skills, and your ability to organize code into a well-structured application.

### Project Options

Choose **one** of the following three projects to complete. They are ordered by difficulty, so pick the one that matches your comfort level and goals:

**🎮 Project 1: Rock–Paper–Scissors** (Simplest, but still a challenge!)
Build a classic game where users play against the computer with score tracking across multiple rounds.

**🛒 Project 2: Shopping List Manager** (Similar to the task manager but with more complexity)
Create a CRUD application where users can add, remove, and view items in their shopping list through a menu system.

**🧠 Project 3: Quiz Game** (The most challenging and new!)
Develop a multiple-choice quiz application with questions, scoring, and replay functionality.

Once you have chosen your project, read through the sections below and then look for your project-specific requirements in their appropriate section of this document.

### Tips / Common Mistakes to Avoid

For many of you, this will be the first "real" project that you build. This is an exciting opportunity! However, it can also become a frustrating one if you take the wrong approach. Here are some common mistakes to avoid and what you can do instead.

| Mistake | What to Do Instead |
|---------|---------------------|
| Trying to build everything at once | Build ONE feature, test it, commit, then move on |
| Writing lots of code before testing | Test after every 5-10 lines of code |
| Trying to write everything from scratch | Use the [Task Manager Case Study](https://github.com/The-Marcy-Lab-School/swe-casestudy-1-cli-task-manager) as a guide for building your project! Take what you need, make what you need! |
| Copying the example without understanding it | Type the code yourself and make changes to match YOUR project |
| Forgetting to export/import functions | Check that every function you call is exported from its file and imported where you use it |
| Not committing often enough | Commit every time something works! You can always go back. |

## Getting Started

### Setup Instructions

Once you have chosen your project, complete the following steps to get started:

1. Go to the [swe-project-1-cli-app template repository](https://github.com/The-Marcy-Lab-School/swe-project-1-cli-app) and click "Use this template" to create a new repository. Set yourself as the owner and give your project a descriptive name!
2. Clone your repo down to your computer.
3. Run `npm init -y`.
4. Install `prompt-sync` with `npm install prompt-sync`.
5. Add, commit, and push all files with the commit message `"initializing project"`

### Git Workflow

To ensure that your `main` branch contains the most up-to-date working version of your application, you should use this workflow:

```sh
# 1. First time only: create the branch
git checkout -b development

  # After that: switch to existing branch  
  git checkout development

# 2. Once you've completed a feature and the application is working nicely: add, commit, and push with a descriptive message
git add -A
git commit -m "merging [feature description]"
git push

# 3. Merge the feature into the main branch:
git checkout main
git merge development

# 4. return to the development branch to work on the next feature
git checkout development
```

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
5. The `README.md` includes setup instructions, usage examples, and clear explanation of functionality. All writing in the `README.md` file and the `REFLECTIONS.md` file is polished and free of typos and grammatical errors.

## How to Submit

When you have finished your application, merge everything into the `main` branch and push! Then, submit the URL to your GitHub repository on Canvas. No need to make a pull-request.

## 🎮 Project 1: Rock–Paper–Scissors CLI Game

**Objective:**
Build a simple Rock–Paper–Scissors game where the user plays against the computer. The program should keep score across multiple rounds until the player decides to quit.

<!-- omit in toc -->
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

<!-- omit in toc -->
### Data Structure

Use an **object** to store game data. The object should look like this:

```js
{
  wins: 2,
  losses: 3,
  ties: 1
}
```

<!-- omit in toc -->
### Stretch Features to Consider:

* Add a "best of X rounds" mode where the game continues until someone wins a certain number of rounds
* Add sound effects or ASCII art for different choices (rock, paper, scissors)
* Track and display win streaks and longest winning/losing streaks
* Allow saving/loading game statistics to/from a JSON file using the `fs` module

## 🛒 Project 2: Shopping List CLI App

**Objective:**
Create a CLI shopping list manager where users can add, remove, and view items through a menu system.

<!-- omit in toc -->
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

<!-- omit in toc -->
### Data Structure

Use an **array of objects** to store shopping items. Each item should be an object with a name, quantity, and price, like this: 

```js
[
  {
    name: "apples",
    quantity: 3,
    price: .5
  },
  {
    name: "carton of 12 eggs",
    quantity: 1,
    price: 7.99
  },
]
```

<!-- omit in toc -->
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

<!-- omit in toc -->
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
  1. 100% (ben) — 10/1/25
  2. 90% (motun) — 10/2/25
  3. 80% (gonzalo) — 10/29/25
  4. 75% (carmen) — 10/10/25
  5. 70% (reuben) — 10/19/25
  ```

* After completing an action, the menu is shown again and the user is prompted to choose their next action.

<!-- omit in toc -->
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

In addition, use a separate **array of objects** to store high score history. Each score should be an object with a `name`, a `score` (representing percentage), and a `date`, like this:

```js
[
  { name: 'ben', score: 100, date: "10/1/25" },
  { name: 'motun', score: 90, date: "10/2/25" },
  { name: 'carmen', score: 75, date: "10/10/25" },
]
```

<!-- omit in toc -->
### Stretch Features to Consider:

* Add different categories of questions (math, science, history, etc.) and let users choose their preferred category
* Implement a difficulty system with easy, medium, and hard questions
* Add a timer for each question to create a more challenging experience
* Allow saving/loading quiz results and high scores to/from a JSON file using the `fs` module

## Stuck Getting Started?

If you're feeling stuck and don't know where to start, follow these steps. Each phase builds on the previous one, so don't skip ahead!

### Phase 1: Get a Menu Working (Start Here!)

**Goal:** Display a menu and respond to user input. Nothing else.

1. **Create `index.js`** — This is your entry point. For now, it should just call a `showMenu()` function from the `menu` file. You can also print out some welcome/goodbye messages:
   ```js

   const { showMenu } = require('./menu.js');

    // This is the main entry point for the application.
    const startApp = () => {
      console.clear();
      console.log("Welcome to [Your App Name]!");
      showMenu();
      console.log("\nGoodbye!");
    }

    startApp();
   ```

2. **Create `menu.js`** — This file handles user interaction. Start with just a menu that prints and exits:
   ```js
   const prompt = require('prompt-sync')();

   const showMenu = () => {
     console.log("Menu:");
     console.log("1. [First Option]");
     console.log("2. [Second Option]");
     console.log("3. Exit");

     const choice = prompt("Choose an option: ");
     console.log(`You chose: ${choice}`);
   };

   module.exports = { showMenu };
   ```

3. **Run it!** Type `node index.js` in your terminal. Does it print the menu and capture your input? Great! Commit your progress.

4. **Add a loop** — Make the menu repeat until the user chooses "Exit":
   ```js
   const showMenu = () => {     
     let isRunning = true;
     while (isRunning) {
       console.log("\nMenu:");
       console.log("1. [First Option]");
       console.log("2. [Second Option]");
       console.log("3. Exit");

       const choice = prompt("Choose an option: ");

       if (choice === "1") {
         console.log("You chose option 1");
       } else if (choice === "2") {
         console.log("You chose option 2");
       } else if (choice === "3") {
         console.log("Goodbye!");
         isRunning = false;
       } else {
         console.log("Invalid choice. Please try again.");
       }
     }
   };
   ```

5. **Run and test it again!** Can you select options and exit? Commit your progress.

---

### Phase 2: Create Your Data Structure

**Goal:** Set up the data your app will manage.

1. **Create a third file for your data** — Name it based on your project:
   - Rock-Paper-Scissors → `gameData.js`
   - Shopping List → `shoppingList.js`
   - Quiz Game → `quizData.js`

2. **Define your starting data** — Look at the "Data Structure" section for your project. Create the initial data and export it:
   ```js
   // Example for Rock-Paper-Scissors (gameData.js)
   const gameStats = {
     wins: 0,
     losses: 0,
     ties: 0
   };

   module.exports = { gameStats };
   ```

   ```js
   // Example for Shopping List (shoppingList.js)
   const shoppingList = [];

   module.exports = { shoppingList };
   ```

   ```js
   // Example for Quiz Game (quizData.js)
   const questions = [
     {
       question: "What is 2 + 2?",
       choices: ["3", "4", "5", "6"],
       answerIndex: 1
     },
     // Add more questions later!
   ];

   const highScores = [];

   module.exports = { questions, highScores };
   ```

3. **Import your data into `menu.js`** and print it to verify it works:
   ```js
   const { gameStats } = require('./gameData.js');
   console.log(gameStats); // Just to test - remove later!
   ```

4. **Run and verify**, then commit your progress.

---

### Phase 3: Implement ONE Feature at a Time

**Goal:** Build each menu option one at a time. Don't move on until the current feature works!

#### Pick your FIRST feature (the easiest one):
- **Rock-Paper-Scissors:** View Stats (just print the data)
- **Shopping List:** View List (just print the data)
- **Quiz Game:** View High Scores (just print the data)

1. **Create a function for this feature** in your data file:
   ```js
   // Example: gameData.js (Rock Paper Scissors)
   const gameStats = { wins: 0, losses: 0, ties: 0 };

   const viewStats = () => {
     console.log("\nCurrent Statistics:");
     console.log(`Games Won: ${gameStats.wins}`);
     console.log(`Games Lost: ${gameStats.losses}`);
     console.log(`Games Tied: ${gameStats.ties}`);
     const total = gameStats.wins + gameStats.losses + gameStats.ties;
     console.log(`Total Games: ${total}`);
   };

   module.exports = { gameStats, viewStats };
   ```

2. **Call that function from your menu:**
   ```js
   const { viewStats } = require('./gameData.js');

   // Inside your menu loop:
   if (choice === "2") {
     viewStats();
   }
   ```

3. **Run and test it!** Then commit.

#### Now pick your SECOND feature (the main action):
- **Rock-Paper-Scissors:** Play Round
- **Shopping List:** Add Item
- **Quiz Game:** Start Quiz

4. **Write a function for this feature.** Break it into smaller steps:
   - What input do you need from the user?
   - What logic needs to happen?
   - What message should you show the user?

5. **Test after EVERY small change.** Don't write 50 lines and then test.

6. **Commit after each feature works.**

#### Continue until all features work!

---

### Phase 4: Polish and Validate

**Goal:** Handle edge cases and make the experience smooth.

1. **Add input validation** — What if the user types something unexpected?
   ```js
   // Example: validate menu choice by removing excess whitespace
   const choice = prompt("Choose an option: ").trim();
   
   // Example: validate yes/no questions
   const answer = prompt("Play again? (yes/no): ").toLowerCase().trim();
   ```

2. **Test edge cases:**
   - What if the user enters nothing (just presses Enter)?
   - What if they enter letters when you expect numbers?
   - What if they try to remove an item that doesn't exist?

3. **Add helpful error messages:**
   ```js
   if (isNaN(quantity) || quantity <= 0) {
     console.log("Please enter a valid positive number.");
   }
   ```

4. **Clean up your code:**
   - Remove any `console.log()` statements you used for testing
   - Make sure variable names are descriptive
   - Add comments where the logic is complex

5. **Final test** — Run through your entire app like a user would. Does everything work smoothly?

### Submission Checklist

- [ ] My app runs without errors (`node index.js`)
- [ ] All menu options work correctly
- [ ] Invalid input shows a helpful message (doesn't crash)
- [ ] My code is split into at least 3 files
- [ ] I've committed my work regularly with descriptive messages
- [ ] My README explains how to run the app
- [ ] My REFLECTIONS.md is complete
