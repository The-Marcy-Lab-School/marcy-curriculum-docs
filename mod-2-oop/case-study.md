# Case Study: Dragon Slayer

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/swe-casestudy-2-dragon-slayer)!
{% endhint %}


**Table of Contents:**

- [Case Study Overview](#case-study-overview)
  - [Learning Objectives](#learning-objectives)
  - [Gameplay Overview](#gameplay-overview)
  - [Setup and Running Instructions](#setup-and-running-instructions)
- [Implementation Details](#implementation-details)
  - [Directory Structure](#directory-structure)
  - [Purpose of Key Files](#purpose-of-key-files)
  - [Character Classes](#character-classes)
  - [Other Classes, Factories, and Utilities](#other-classes-factories-and-utilities)
  - [Key Interactions](#key-interactions)
- [Investigation Questions](#investigation-questions)
  - [Encapsulation with Closures and Factory Functions](#encapsulation-with-closures-and-factory-functions)
  - [Encapsulation with Classes](#encapsulation-with-classes)
  - [Inheritance](#inheritance)
  - [Polymorphism](#polymorphism)
- [Extension / Refactor Opportunities](#extension--refactor-opportunities)

## Case Study Overview

Dragon Slayer is a simple, command-line, object-oriented RPG where players choose a hero and battle their way to defeat a dragon, facing multiple enemies along the way. The project is structured to both demonstrate core OOP concepts (like inheritance, polymorphism, and encapsulation) and how to organize code in a modular Node.js application.

### Learning Objectives

This case study demonstrates the following Mod 2 OOP concepts:
- **Encapsulation** (with closures via factory functions and with classes)
- **Inheritance** (Character base class with Hero/Enemy subclasses)
- **Polymorphism** (shared interfaces with class-specific implementations)
- **Factory Functions** (makeGameHistoryManager using closures)
- **Static vs Instance Methods** (Game and Character class methods)

For detailed investigation questions exploring these concepts, see [INVESTIGATION.md](./INVESTIGATION.md).

### Gameplay Overview

You are on a quest to slay the dragon.

Choose a hero (Mage, Warrior, or Archer) and battle through three levels:
1. **Level 1**: Fight a Goblin (a fairly easy win)
2. **Level 2**: Fight an Orc (you should win most of the time)
3. **Level 3**: Fight the Dragon (a real challenge!)

Each turn, choose your action:
- **Attack**: Deal damage to the enemy.
- **Defend**: Double your defense and counterattack if all damage is blocked.
- **Buff**: Power up your character (increases attack, defense, or health). You will be vulnerable and take double damage if attacked.

Your enemies are powerful, but they choose their actions randomly and announce their choice before you act. If you want to win you must be strategic with your choices! 

**Tip**: Health is restored between levels, but buffs persist.

### Setup and Running Instructions

1. **Install Dependencies**
   - Be sure you have [Node.js](https://nodejs.org/) installed (`v16+` recommended).
   - Run `npm install` if you have dependencies (none strictly required unless you add a dependency to `package.json`). If `prompt-sync` is missing, run:  
     ```sh
     npm install prompt-sync
     ```

2. **Start the Game**
   - From the project root, run:
     ```sh
     node src/index.js
     # or
     npm start
     ```

## Implementation Details

### Directory Structure

```
dragon-slayer/
├── src/
│   ├── index.js                  # Entry point: main menu and game loop
│   ├── Game.js                   # Game logic and central manager class
│   ├── makeGameHistoryManager.js # Factory for managing/saving game history
│   ├── utils/
│   │   └── fileIO.js             # File read/write helpers for JSON files
│   ├── characters/
│   │   ├── Character.js          # Base Character class
│   │   ├── Heroes.js             # Mage, Warrior, Archer hero classes
│   │   └── Enemies.js            # Goblin, Orc, Dragon enemy classes
│   └── data/
│       └── gameHistory.json      # (Generated at runtime) Persists history of games
├── README.md                     # This file
├── INVESTIGATION.md              # Questions to understand OOP concepts implemented in this application
├── package.json                  # Node.js dependencies & scripts (if used)
└── ...                           # (other typical git/node files)
```

---

### Purpose of Key Files

- **src/index.js**:  
  Main "launcher" for the game. Handles the menu, user input, and orchestrates creation and saving of game sessions.

- **src/Game.js**:  
  Defines the `Game` class, which manages an individual play session, including the sequence of battles, player/enemy selection, and win/lose logic. Also contains static helpers to print instructions and character stats.

- **src/makeGameHistoryManager.js**:  
  Exports a factory function that creates a history manager object responsible for tracking, displaying, and persisting completed games.

- **src/utils/fileIO.js**:  
  Utility file for reading from and writing to the `gameHistory.json` file using Node's filesystem.

- **src/characters/Character.js**:  
  Defines the abstract base class for all characters (heroes and enemies) with shared properties and methods.

- **src/characters/Heroes.js**:  
  Defines the Mage, Warrior, and Archer hero classes, which inherit from Character and implement their own abilities.

- **src/characters/Enemies.js**:  
  Implements specific enemy classes: Goblin, Orc, and Dragon, each with unique stats and moves.

- **src/data/gameHistory.json**:  
  Stores the saved history of game sessions, written to/loaded from by the game history manager.

---

### Character Classes

- **Character (Base Class)**  
  All heroes and enemies extend from `Character`, sharing core stats like health, attack, defense, as well as common methods for attacks, buffs, and damage calculation.

- **Heroes (Mage, Warrior, Archer)**  
  Inherit from `Character`. Each implements unique abilities and flavor through constructor params and buff/attack logic.

- **Enemies (Goblin, Orc, Dragon)**  
  Also extend `Character`. They choose actions randomly and scale up in difficulty.

You can see these relationships visually in this Unified Modeling Language (UML) Diagram below:

![The Character superclass is extended by Mage, Archer, Warrior, Goblin, Orc, and Dragon](./img/character-diagram.svg)

Each box represents a single class with the class name listed at the top, properties in the middle, and methods at the bottom. Static properties and methods are underlined.

### Other Classes, Factories, and Utilities
- **Game**  
  Central coordinator. Handles setup, main game loop, battle execution, progression, and printing instructions/stats. Knows the player and current enemy.

- **makeGameHistoryManager**
  A factory that creates a private `gameHistory` manager object, encapsulating methods for:
    - Adding completed games
    - Displaying the history
    - Saving/loading history from filesystem using `fileIO.js`

- **fileIO.js**  
  Has generic utility functions for writing data to / reading from a given file. Used by the game history manager for file persistence for game history so progress is saved between runs.

### Key Interactions

- `index.js` (menu loop) creates a `gameHistoryManager`.
- Each time the player wants to play, a new `Game` instance is created.
- After each session, the new `Game` instance is passed to `gameHistoryManager.addGame`.
- On exit, history is persisted to `src/data/gameHistory.json`.
- The core battle loop is orchestrated by the `Game` class, which uses hero/enemy instances and their overridden attack/buff methods (illustrating polymorphism and encapsulation).

## Investigation Questions

By answering these questions, you will be required to think critically about how the application is designed and understand WHY it is designed in this way. Your aim should be to:
* learn as much as you can from this application so that you can build an application of your own that leverages these same skills
* communicate clearly about the concepts you are using and the decisions you make for how you implement them.

### Encapsulation with Closures and Factory Functions

A **closure** in JavaScript is a feature where an inner function has access to variables defined in its outer (enclosing) function, even after that outer function has finished executing. This allows the inner function to "remember" and interact with the environment in which it was created, letting you preserve private state between function calls.

A **factory function** is a function that returns a new object each time it's called, often using closure to encapsulate private variables and methods that can't be accessed from outside the function. When a factory function defines internal variables and returns an object with methods that reference those variables, those methods form closures—giving the created objects their own encapsulated, persistent state.

**Investigation Questions:** 

Note: "IO" is short for "input / output" and is a common acronym used to describe functionality related to reading data from or writing data to external sources such as files or the command line. In this case, we use it to read from and write to the `gameHistory.json` file.

Look into the `makeGameHistoryManager.js` file and examine how it's used in `index.js`:

1. In `makeGameHistoryManager.js`, the `gameHistory` variable is defined inside the factory function (line 12). Trace through the code: where is `gameHistory` used inside the returned object? Even though `makeGameHistoryManager` finishes executing and returns, how can the `addGame()`, `printGameHistory()`, and `saveGameHistory()` methods still access the `gameHistory` variable? Explain how closure makes this possible.

2. Look at how `makeGameHistoryManager` is used in `index.js` (line 10). The function is called, and the returned object is stored in `gameHistoryManager`. What would happen if you called `makeGameHistoryManager` multiple times? Would each returned object have its own separate `gameHistory` array? Why or why not? Come up with an imagined example with two different game history manager objects to illustrate your answer.

3. The `gameHistory` variable is not directly accessible from outside the returned object. Try to explain: if you created a `gameHistoryManager` object using `makeGameHistoryManager`, could you directly access or modify the `gameHistory` array from outside? Why is this beneficial? How does this demonstrate encapsulation?

4. Compare the factory function pattern in `makeGameHistoryManager.js` to how you might implement this with a class. If you were to convert `makeGameHistoryManager` to a class, what would the constructor look like? What would be the equivalent of the `gameHistory` variable? Why might a factory function be more appropriate here than a class? Consider: does this functionality need to maintain state between method calls?

5. Look at the `addGame()`, `printGameHistory()`, and `saveGameHistory()` methods in the returned object. All of these methods reference the `gameHistory` variable that was captured in the closure. If you were to modify the code to add a new method called `getGameHistory()` that returns the `gameHistory` array, would this break encapsulation? Why or why not?

### Encapsulation with Classes

Encapsulation with classes is an object-oriented programming concept where data (properties) and behavior (methods) are bundled together within a class. It restricts direct access to some of an object's components, typically by using private fields (using `#` in JavaScript) and providing public methods to interact with that data. This ensures that the internal state of an object can only be changed in controlled ways. Encapsulation provides improved code safety as well as easier maintenance and refactoring.

**Investigation Questions:** 

Examine the `Game.js` file:

1. Compare the static methods and properties in the `Game` class to the instance methods and properties. Look at methods like `printCharacterStats()` (static, line 140) versus `runGame()` (instance, line 40). What's the difference between calling `Game.printCharacterStats()` and `game.runGame()`? Why are some methods static (belonging to the class) while others are instance methods (belonging to each game object)? How does this relate to encapsulation—what data does each type of method need to access?

2. Look at the `printCharacterStats()` static method (lines 140-150). Why is this method static rather than an instance method? What would happen if you tried to call `game.printCharacterStats()` on a `Game` instance? Why doesn't this method need access to any instance properties?

3. Examine the instance properties in the `Game` constructor (lines 31-37). Properties like `player`, `enemy`, `playerName`, `level`, and `achievedVictory` are all public. If you wanted to make the `level` property private in the `Game` class, what would you need to change? How would you need to modify methods like `setLevel()` and `runGame()` that access `this.level`? Explain why making `level` private would improve encapsulation and prevent potential bugs (e.g., what if someone accidentally set `game.level = -5`?).

4. Look at the `Character` class in `characters/Character.js`. All the properties defined in the constructor (lines 63-70) are public. If you wanted to make a character's `health` property private in the `Character` class, what would you need to change? How would you need to modify the `restoreHealth()` method? What other methods in the `Character` class directly access `this.health`, and how would they need to change? Explain why making `health` private would improve encapsulation and prevent potential bugs (e.g., what if someone accidentally set `character.health = -100`?).

### Inheritance

Inheritance is an object-oriented programming (OOP) concept where a new class (the **subclass**) automatically acquires the properties and methods of another class (the **superclass**). This promotes code reuse, allows programmers to build upon existing functionality, and enables logical relationships between types. By using inheritance, programmers can write less repetitive code, make their code more modular and maintainable, and easily extend or specialize functionality in future subclasses.

**Investigation Questions:** 
Take a look at the `characters/` directory and look at the `Character.js` class. Then, look at the classes defined in `Heroes.js` and `Enemies.js`. 

1. Examine the constructor of the `Mage` class in `Heroes.js`. What does the `super()` call do, and what arguments are being passed to it? Compare this to the `Character` class constructor. Why is it necessary to call `super()` before any other code in the subclass constructor?

2. In the `Character.js` class, identify all the instance methods and properties that are defined. Now look at the `Warrior` class in `Heroes.js`. Which methods and properties from `Character` can a `Warrior` instance access? Give at least three examples of inherited methods that `Warrior` can use.

3. Examine the `printDescription()` method in the `Archer` class. It calls `super.printDescription()` before adding its own console.log statement. What would happen if you removed the `super.printDescription()` call? What would the output be? Explain why using `super` here is important for maintaining the base class functionality.

### Polymorphism

Polymorphism is an object-oriented programming concept where objects of different classes can be treated as if they are instances of the same superclass because they share a common interface (the same method names). In practice, it means that you can invoke the same method (for example, `.buff()` or `.printDescription()`) on different objects, and each object will respond in its own class-specific way.

**Benefits to programmers:**
- Makes code more flexible and extensible, because you can write general code that works with a wide range of object types.
- Reduces the amount of repetitive or duplicated code, since the same interface can be reused across subclasses.
- Enhances maintainability, since changes to functionality can be made in one place (the superclass or a specific subclass) without altering code that depends on the polymorphic interface.

**Investigation Questions:** 
Take a look at the `characters/` directory and examine the `Game.js` file.

1. Look at the `printCharacterStats()` method in `Game.js` (lines 140-150). This method creates arrays containing different character types (`Mage`, `Warrior`, `Archer`, `Goblin`, `Orc`, `Dragon`) and then calls `printDescription()` on each one. Explain how polymorphism makes this possible. What would happen if each character class didn't have a `printDescription()` method? What would happen if some classes had the method and others didn't?

2. Examine the `resolveActions()` method in `Game.js` (lines 125-137). Notice that it calls `this.player.buff()` and `this.enemy.buff()` without knowing the specific subclass type. Look at the `buff()` method implementations in `Mage`, `Warrior`, `Archer`, `Goblin`, `Orc`, and `Dragon`. How does polymorphism ensure that the correct `buff()` method is called for each character? What would the output be if you called `buff()` on a `Mage` versus a `Dragon`? How is this different from the base `Character.buff()` method?

3. In the `Character.js` class, the `buff()` method (lines 118-121) contains a placeholder implementation that just logs a message. Why do you think the base class has this placeholder method instead of leaving it undefined? How does this design choice relate to polymorphism? What would happen if you tried to call `buff()` on a `Character` instance directly (not a subclass)?

4. Look at the `displayBattleStatus()` method in `Game.js` (lines 105-109). It calls `printStatus()` on both `this.player` and `this.enemy`. However, `printStatus()` is not overridden in any of the subclasses—it's only defined in the `Character` class. Is this still an example of polymorphism? Why or why not? How does this differ from the polymorphic behavior you see with `buff()` or `printDescription()`?

5. Consider the `enemies` array in the `Game` constructor (line 37). It contains instances of `Goblin`, `Orc`, and `Dragon`. Later in the code, `this.enemy` is assigned from this array (line 96). Throughout the `Game` class, methods like `setAction()`, `attack()`, `defend()`, and `buff()` are called on `this.enemy` without checking its specific type. Explain how polymorphism allows this to work. If you wanted to add a new enemy type (e.g., `Troll`), what would you need to do to make it work seamlessly with the existing `Game` code? What would break if `Troll` didn't extend `Character`?

## Extension / Refactor Opportunities

* Refactor the `makeGameHistoryManager` into a `class` declaration. Make sure to keep the `gameHistory` private!
* Add new characters / enemies. Reflect on how polymorphism makes it easy to do this.

Enjoy the adventure, and good luck slaying the dragon!