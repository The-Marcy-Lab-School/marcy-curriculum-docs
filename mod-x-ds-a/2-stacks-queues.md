# 1. Stacks and Queues

**Table of Contents:**
- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Problem: Balanced Brackets](#problem-balanced-brackets)
- [What is an Abstract Data Type?](#what-is-an-abstract-data-type)
  - [So, What is a Stack?](#so-what-is-a-stack)
  - [Classic Algorithm: Balanced Brackets](#classic-algorithm-balanced-brackets)
    - [Implementation Challenge:](#implementation-challenge)
- [Queues: The Counterpart to Stacks](#queues-the-counterpart-to-stacks)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is an _abstract data type_?
2. What is the order in which values in a stack are inserted and retrieved?
3. Where do we see stacks in the real world?
4. What are common use cases for stacks in computer science?
5. How does a Queue differ from a Stack?

## Key Concepts

* **Stack** - an Abstract Data Type that stores a collection of values that you can visualize as a "vertical stack": you can only add values to the "top" of the stack and can only access or remove the value at the "top" of the stack. Example use cases include an "undo" button, a function call stack, depth first search graph traversal.
  * **LIFO ("Last In, First Out")** - the ordering behavior of a stack: the last value "pushed" (added) is the first value "popped" (removed).
* **Queue** - an Abstract Data Type that stores a collection of values that you can visualize as a line: you can only add values to the "back" and can only access or remove the value at the "front." Example use cases include task scheduling and handling requests in the order they arrive.
  * **FIFO ("First In, First Out")** - the ordering behavior of a queue: the first value "enqueued" (added) is the first value "dequeued" (removed).

## Problem: Balanced Brackets

**The Problem**: Given a string containing just the characters `(`, `)`, `{`, `}`, `[` and `]`, determine if the input string is balanced.

* Balanced Examples:
  * `{[()]}`
  * `[()]{}`
  * `[](){}`
* Unbalanced Examples:
  * `[(])` (The `(` was opened after the `[`, so it must be closed before the `[` can close).
  * `}}{{` (The pairs match but the closing brackets come first)

How would you implement this function?

```js
const isBalanced = (str) => {
  // ???
}
```

To help us think about how to implement this, let's look at an **Abstract Data Type** called a **Stack**.

## What is an Abstract Data Type?

An Abstract Data Type (ADT) is not a "real" data structure like an Array or a Hashmap (a.k.a. Objects in JavaScript).

Instead, it is a restricted set of operations you can perform with a collection of data, without any details about how those operations are actually implemented. **We only care about the outcome of those operations.**

For example, in order to operate a car, I just need to know that it has a steering wheel, a gas pedal, and brakes. I don't need to know how everything under the hood works.

Similarly, learning about ADTs like Stacks can help you ignore the implementation details and simplify the way you think about a problem.

### So, What is a Stack?

A Stack is an Abstract Data Type in which you can visualize a collection of data as a pile of pancakes.

<img src="../.gitbook/assets/pancakes.png" alt="A stack of pancakes" width="400">

Stacks are ordered like Arrays, however, you can only work with the "top" value in the stack. A Stack has three basic operations:

* `push(value)` — inserts a new element to the "top" of the stack
* `pop()` — removes the top element of the stack
* `peek()` - look at the top element of the stack without removing it

![](../.gitbook/assets/stack.png)

This is more formally known as **LIFO**: or "last in, first out" (the last pancake you add to the stack is the first one you eat).

Stacks have helped programmers solve a variety of interesting problems such as:

* Building a call stack:
  * Every time a function is invoked, it is pushed to the top of the call stack
  * When the function returns, it is popped from the stack
  * When the program crashes, you print out the stack at that moment and you can see what functions were called and in what order
* Implementing an "Undo" button:
  * Every time a user performs an action, push it to the top of the Stack
  * If the user wants to "Undo" an action, simply pop the most recent action from the Stack
  * You can "Redo" actions by maintaining a second stack where any undone actions are pushed to the second stack until the user takes a completely new action.

### Classic Algorithm: Balanced Brackets

<summary><strong>Q: What makes a Stack a perfect ADT to solve this problem? How does the LIFO access pattern show up in valid strings versus invalid strings?</strong></summary>

**Why a Stack is Perfect**: As you read the string from left to right, every time you see an opening bracket, it becomes the "active" bracket waiting for its partner. Because of the LIFO property, the last opening bracket you see is always the very first one that needs to be closed.

For example, in the string `{[()]}` the `(` (at index `2`) is the last bracket opened so it must be the first one closed, then the `[` and lastly the `{`.

</details>

#### Implementation Challenge:

A Stack can be easily implemented with an Array in JavaScript with minimal work needed.
- Creating a Stack - create an empty Array called `stack`
- `stack.push()` — use the built-in `Array.push` method
- `stack.pop()` — use the built-in `Array.pop` method
- `stack.peek()` — use index notation and grab the last element of the array

**The Algorithm Steps**

1. Initialize an empty stack (an empty array)
2. Loop through each character in the string:
   1. If it's an opening bracket (`(`, `{`, `[`), "push" it onto the stack.
   2. If it's a closing bracket (`)`, `}`, `]`), "peek" at the top of the stack. 
      1. If the top of the stack is its matching opening partner, pop it off. 
      2. If it doesn't match (or the stack is empty), the string is invalid.
3. After checking the whole string, if the stack is completely empty (you peek at the top and nothing is there), the brackets are perfectly balanced. If there are still brackets left in the stack, someone forgot a closing bracket!

{% hint style="info" %}
💡**TIP:** Create an Object that maps each closing bracket (`)`, `}`, `]`) to it's opening bracket (`(`, `{`, `[`). When encountering a closing bracket, this will make it much easier to see if the top bracket in the stack is its pair.
{% endhint %}

Here is a visualization of that algorithm:

{% embed url="https://docs.google.com/presentation/d/1RZ-rg-AuhxZC0XIMrndSVXNfweK__Fca7wA_6eV8-eY/embed?start=false&loop=false&delayms=3000" %}

<details>

Can you implement it?

<summary><strong>Solution</strong></summary>

```js
function isBalanced(str) {
  // An array can easily be used as a Stack since it has push and pop methods
  const stack = [];
  
  // when you encounter a ), ], or }, look up the matching opening bracket
  const bracketPairs = {
    ')': '(',
    '}': '{',
    ']': '['
  };

  for (let i = 0; i < str.length; i++) {
    const char = str[i];

    // 1. If it's an opening bracket, push it onto the stack
    if (char === '(' || char === '{' || char === '[') {
      stack.push(char);
    } 
    // 2. If it's a closing bracket
    else if (char === ')' || char === '}' || char === ']') {
      // Look at the topmost opening bracket in the stack
      const top = stack[stack.length - 1]
      
      // If the current closing bracket is not matched with the top, they aren't balanced
      if (bracketPairs[char] !== top) {
        return false; 
      }
      // If it's a perfect match, pop it off
      stack.pop();
    }
  }

  // 3. If the stack is completely empty, it is balanced
  // Otherwise, an opening bracket remains un-closed
  return stack.length === 0;
}

// Test Cases
console.log(isBalanced("{[()]}")); // true
console.log(isBalanced("[(])"));   // false
console.log(isBalanced("[{()"));   // false
```

</details>

## Queues: The Counterpart to Stacks

A Stack gives you a specific way to think about problems that only care about **one end** of a list: the "top." That's what made it a perfect fit for Balanced Brackets, the call stack, and an "Undo" button — in each case, the only thing that matters is whatever was most recently added. We called this process LIFO: last in, first out.

But plenty of problems care about the opposite order: **first in, first out (FIFO)**. Think of:

* **Handling requests to a server** — requests are handled in the order they came in, not in reverse.
* **Task scheduling in an operating system** — new tasks join the back of the line, and the OS works through them in the order they arrived.

The Abstract Data Structure for these FIFO problems is called a **Queue** which you can visualize the data as a line: the **first** person in line is the **first** person served. 

<img src="../.gitbook/assets/queue-cartoon.png" alt="A line of people waiting, credit: Royston Robertson" width="500">


A Queue has three basic operations:

* `enqueue(value)` — inserts a new element to the "back" of the queue
* `dequeue()` — removes the front element of the queue
* `peek()` — look at the front element of the queue without removing it

![](../.gitbook/assets/queue.png)

<details>

<summary><strong>Q: A Stack and a Queue both restrict what you can do with a list of values. What's the key difference between the two restrictions?</strong></summary>

A Stack restricts you to one end — you `push` and `pop` from the same "top." A Queue restricts you to *both* ends, but assigns each end a different job — you `enqueue` onto the "back" and `dequeue` from the "front." Same idea (an ADT that limits an Array's full flexibility in exchange for a guaranteed order), opposite ordering rule.

</details>

For now, this is just an introduction to Queues as another example of an Abstract Data Type — a different set of guarantees than a Stack offers, useful for a different shape of problem. We'll come back to Queues soon and use them to help solve some interesting problems of their own.

