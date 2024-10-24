# Debugging & Problem Solving

**Table of Contents:**
- [Debugging \& Rubber Ducking](#debugging--rubber-ducking)
  - [How to Rubber Duck](#how-to-rubber-duck)
- [PEDAC Problem Solving](#pedac-problem-solving)
  - [P — State the Problem](#p--state-the-problem)
  - [E — Examples / Test Cases](#e--examples--test-cases)
  - [D — Data Structures](#d--data-structures)
  - [A — Algorithm](#a--algorithm)
  - [C — Code!](#c--code)
  - [Example](#example)


## Debugging & Rubber Ducking

**Debugging** refers to the process of detecting, locating, and fixing problems, or “bugs,” in computer code. 

It is one of the most essential skills to learn and yet, it is incredibly difficult to learn, particularly for new programmers. 

One strategy that is widely used in the industry is called **rubber-ducking** which is essentially explaining your problem to a rubber duck.

Yes... a literal rubber duck.

![a rubber duck](img/rubber-duck.png)

How does this help you resolve the problems in your code? The idea of debugging a program by talking to a rubber duck comes from The Pragmatic Programmer (Hunt & Adams, 1999):

> A very simple but particularly useful technique for finding the cause of a problem is simply to explain it to someone else. The other person should look over your shoulder at the screen, and nod his or her head constantly (like a rubber duck bobbing up and down in a bathtub). They do not need to say a word; the simple act of explaining, step by step, what the code is supposed to do often causes the problem to leap off the screen and announce itself. (p. 95)

### How to Rubber Duck

First, grab a rubber duck (or some other inanimate object that you feel comfortable talking to).

Then, have the following conversation with your duck:

* What is your code supposed to do? What should the output be? 
  * Start by telling your duck about the goal or purpose of your program.
* What is your code actually doing, and how is it different from what you want it to be doing? 
  * Describe the error or incorrect output you are encountering. If you have multiple problems, pick one and come back to the others later.
* Where does the code first go wrong? 
  * Describe out loud to your rubber duck what each line of your code is doing, and try to trace the problem back to its source. You may find that a simple typo is causing all of your problems!
* What have you done so far to try to fix your program? 
  * Describe any approaches you have tried and how they worked or did not work.
* What other ideas do you have? 
  * Get creative, and test out other possible solutions, starting with the simplest and easiest approach.

## PEDAC Problem Solving

Imagine that you had the following problem to solve:

> Consider this array.
> ```js
> const users = [
>   { id: 1, name: 'ben', isAdmin: false},
>   { id: 2, name: 'maya', isAdmin: true},
>   { id: 3, name: 'reuben', isAdmin: true},
>   { id: 4, name: 'gonzalo', isAdmin: false},
>   { id: 5, name: 'ana', isAdmin: true},
> ]
> ```
> Write a function `getAdmins` that has one parameter, an array of `users`. The function should return the an array of user objects within `users` where the `isAdmin` property is `true`. If none are found, return an empty array. Do not modify the original array.

You might have an idea of how to solve the problem, but before you do, try planning out your approach first! Doing so has a number of benefits:
* You will have a roadmap for writing a solution that you can return to if you get lost while coding.
* If you don't finish solving the problem, you can leave and come back with most of the planning already done.
* You can improve the skill of problem solving separate from the skill of coding.

At Marcy, we use **PEDAC** which is an acronym that stands for the following problem solving strategy:

### P — State the Problem

Before you can solve a problem, you must fully understand the problem. When you have a problem in front of you, start by listing **inputs** and **outputs**

{% hint style="info" %}
Tip: Write this planning in a comment directly in your code!
{% endhint %}

Example: 

### E — Examples / Test Cases
### D — Data Structures
### A — Algorithm
### C — Code!

### Example
```js
/* 
PEDAC

State the Problem
- inputs: 
  - an array of user objects that have properties id, name, and isAdmin
- output:
  - an array of user objects that are admins

Examples / Test Cases:

getUserById(users, 4) => { id: 4, name: 'ana'}
getUserById(users, 2) => { id: 2, name: 'maya'}
getUserById(users) => undefined
getUserById(users, 100) => undefined

Data Structures & Tools
- we can loop through the array
- we can use if statements to check each value's id

Algorithm
- use users.find() to find the user that matches the given id
Code

*/
```