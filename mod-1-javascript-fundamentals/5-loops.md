# Loops

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/1-1-0-loops)!
{% endhint %}

**Table of Contents**:
- [Intro to Iteration](#intro-to-iteration)
- [The `for` loop](#the-for-loop)
  - [For Loop Challenge:](#for-loop-challenge)
- [While Loops and Infinite Loops](#while-loops-and-infinite-loops)
  - [While Loop Challenge](#while-loop-challenge)
- [Nested Loops](#nested-loops)

## Intro to Iteration

**Iteration** is the repetition of a process, getting closer to some result each time.

You are assigned the task of flipping a coin 100 times and documenting the result of each. To do this you will:
  1. Gather your materials (pen, paper, a coin)
  2. Until you've flipped 100 times you will:
      1. flip a coin
      2. record the result by writing: `"flip number [flip number] was [heads|tails]"`
  3. Make sure to increase the `flip number` after each flip.

Doing this by hand certainly would take a while. We can write code to likely do it faster:

```js
const flipCoin = () => {
  const random = Math.random();
  return random > 0.5 ? "heads" : "tails";
}

console.log(`Flip number 1 was ${flipCoin()}`);
console.log(`Flip number 2 was ${flipCoin()}`);
console.log(`Flip number 3 was ${flipCoin()}`);
console.log(`Flip number 4 was ${flipCoin()}`);
console.log(`Flip number 5 was ${flipCoin()}`);
// and so on until you reach 100! 🫠
```

But even so, we have to manually update each number. What a pain! If only there was some way to do this more efficiently.

## The `for` loop

`for` loops are best used to repeat a process a known number of times. The syntax looks like this:

```js
for (initialization; whileTrue; update) {
  // do something
}
```

Armed with this syntax, we can easily loop 100 times!

```js
for (let i = 0; i < 100; i++) {
  const result = flipCoin();
  console.log(`Flip number ${i} was ${result}`);
}
```

Try using the debugger and you will see the order of operations
  1. Initialize (`let i = 0`)
  2. While the condition is true (while `i < size` returns `true`)
  3. Execute the code block
  4. Update the counter (`i++`)
  5. **Go to 2**

{% hint style="info" %}
Even in the most basic programming languages, the concept of the **GOTO** statement has existed. Programmers used to have to label a specific line with a name like `"StartLoop"` and then write a statement to `Go To StartLoop` if they wanted to repeat a portion of code. In high-level programming languages like JavaScript, that is abstracted away for us by the `for` loop.
{% endhint %}

### For Loop Challenge:

Write a program that does the following:
1. Asks the user to enter a number. 
2. Flips a coin that many times, keeping count of how many times heads was flipped. 
3. Prints back the final number of heads to the user like this: 

```
You flipped [heads flipped] heads out of [total] flips! That is [percent]%!
```

Here is some starter code:

```js
const prompt = require('prompt-sync')();

const flips = prompt('How many times do you want to flip the coin? ');

// a guard clause that makes sure they entered a number
if (isNaN(Number(flips))) {
  console.log(`Sorry, ${flips} is not a number`);
  return;
}

// add your code here
```

**<details><summary>Check out the solution!</summary>**

```js
const prompt = require('prompt-sync')();

const flipCoin = () => {
  const random = Math.random();
  return random > 0.5 ? "heads" : "tails";
}

// ask the user for flips
const flips = prompt('How many times do you want to flip the coin? ');

// make sure they entered a number
if (isNaN(Number(flips))) {
  console.log(`Sorry, ${flips} is not a number`);
  return;
}

// We want to use this variable after the loop is done, so we declare it outside the loop
let heads = 0;
for (let i = 0; i < flips; i++) {
  const result = flipCoin();
  heads += result === 'heads' ? 1 : 0;
}
console.log(`You flipped ${heads} heads out of ${flips}. Thats ${(heads / flips) * 100}%!`);
```

</details>

## While Loops and Infinite Loops

An **infinite loop** is one in which the condition is ALWAYS `true`. This will cause a program to run forever, either depleting resources or just causing the computer to stall while it waits for the program to end. 

Infinite loops are most often created using `while` loop which are best used to repeat a process an _unknown_ number of times.

To ensure that a loop does not go on infinitely, we use these two statements:
- `break;` prematurely breaks out of a loop
- `continue;` prematurely goes to the iteration step of the loop

```js
const prompt = require("prompt-sync")({ sigint: true }); // for later

while (true) {
  const input = prompt("Enter a number or q to quit: ");
  if (input === "q") {
    console.log("Bye!");
    break; // <--- how is this different from return??
  }
  if (Number.isNaN(Number(input))) {
    console.log("please enter a number");
    continue;
  }
  console.log(`${input}? That's a great number!`);
}

console.log("See you next time!");
```

{% hint style="info" %}
**Q: How is `break` different from `return`?** A `break` statement will exit the current loop and continue executing code that follows the loop. A `return` statement inside of a loop will exit the current loop AND the current function or program being executed.
{% endhint %}

### While Loop Challenge

Write a program that does the following:
1. Generates a random number from 1-10.
2. Asks the user to guess the number.
3. If the user is correct, print a message congratulating them and end the program.
4. If they are incorrect, ask them again.

**Bonus Features**
1. Keep track of their guesses and when they guess correctly, tell them how many guesses it took for them to get it right.
2. Limit their guesses to 5 guesses. If they guess 5 times incorrectly, they lose!

**<details><summary>Check out the solution!</summary>**

```js
const prompt = require("prompt-sync")({ sigint: true }); // for later

const randomNum = Math.ceil(Math.random() * 10);
console.log("I'm thinking of a random number. Guess what it is!");

// We're going to pull out this `input` value so we can check it on every loop 
let input;

// We're also going to keep track of remaining guesses
let guessesRemaining = 5

// As long as the input doesn't match the random number above AND
while (input !== randomNum) {
  // Get the user input and do some input checking
  input = prompt("Enter a number or q to quit: ");
  if (input === "q") {
    console.log("Bye!");
    break;
  }
  if (Number.isNaN(Number(input))) {
    console.log("please enter a number");
    continue;
  }

  // Now that we know we've got a number input, we can decrement the guesses
  guessesRemaining -= 1;

  // If the guess matches, congratulate the user and exit the loop
  if (Number(input) === randomNum) {
    console.log(`You got it!! And with ${guessesRemaining} guesses to spare!!!`);
    break;
  }

  // Assuming we didn't exit the loop, break them the bad news
  console.log(`${input}? That's a great number! But not mine!`);

  // And do a final check to see if they will keep going!
  if (guessesRemaining === 0) {
    console.log(`Sorry, you've ran out of luck :(`);
    break;
  }
}

console.log("Thanks for playing!");
```

</details>

## Nested Loops

A **nested loop** is a loop written inside the body of another loop. For each iteration of the outer loop, the inner loop will complete ALL of its iterations.

```js
// The outer loops runs 3 times, for i = 0 through i = 2
for (let i = 0; i < 3; i++) {

  // This statement is executed 3 times, once per outer loop.
  console.log(`Outer loop ${i}:`)

  // The inner loop runs 5 times, for j = 0 through j = 4
  for (let j = 0; j < 5; j++) {
    // This statement is executed a total of 15 times, 5 times per outer loop
    console.log(`  ${i} - ${j}`);
  }
}

/*
Outer loop 0:
  0 - 0
  0 - 1
  0 - 2
  0 - 3
  0 - 4
Outer loop 1:
  1 - 0
  1 - 1
  1 - 2
  1 - 3
  1 - 4
Outer loop 2:
  2 - 0
  2 - 1
  2 - 2
  2 - 3
  2 - 4
*/
```
