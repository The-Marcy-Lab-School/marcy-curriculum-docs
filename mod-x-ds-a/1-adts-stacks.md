# Abstract Data Types and Stacks
- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Introduction: Why Learn Data Structures?](#introduction-why-learn-data-structures)
- [Abstract Data Types](#abstract-data-types)
- [What is a Stack?](#what-is-a-stack)
  - [Stack Implementation](#stack-implementation)
  - [Classic Algorithm: Balanced Brackets](#classic-algorithm-balanced-brackets)
    - [Implementation Challenge:](#implementation-challenge)


## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is an _abstract data type_?
2. What is the order in which values in a stack are organized?
3. Where do we see stacks in the real world?
4. What are common use cases for stacks in computer science?
5. What are the run times for insertion, deletion, and accessing values in a stack?

## Key Concepts

* **Abstract Data Type (ADT)** - a high-level description of how a type of data behaves, independent of how it is implemented.
* **Stack** - an ADT that stores a collection of values that you can visualize as a "vertical stack": you can only add values to the "top" of the stack and can only access or remove the value at the "top" of the stack. Example use cases include an "undo" button, a function call stack, depth first search graph traversal.
  * **LIFO ("Last In, First Out")** - the ordering behavior of a stack: the last value "pushed" (added) is the first value "popped" (removed).
  * **`push`** - inserts a new value onto the "top" of the stack.
  * **`pop`** - removes and returns the value on the "top" of the stack.
  * **`peek`** - returns the value on the "top" of the stack

## Introduction: Why Learn Data Structures?

Let's start with a question.

**<details><summary>Q: Why is it important to know different data structures? For example, if Arrays and Objects can both store collections of data, why should I know how to use both of them?</summary>**

Every data structure has advantages and disadvantages. While Arrays and Objects can both store collections of data, but the operations you can perform with them are different. Knowing their differences allows you to choose the right data structure for every situation.

</details>

## Abstract Data Types

An **abstract data type (ADT)** is a high-level ("abstract") description about how a particular type of data behaves _without concern for how it is implemented_.

You can think of Classes in Object-Oriented Programming as a way of designing ADTs: you can design a Class with a set of methods and other parts of your program can use that Class without knowing the implementation. 

For example, to manage a list of Todos, we can define a class with methods for adding, deleting and updating a todo item. We don't need to know how each method is implemented in order for us to build things with this Class. 

```js
class TodoList {
  addTodo(description) {
    // implementation hidden...
  }
  deleteTodo(todoNumber) {
    // implementation hidden...
  }
  markTodoComplete(todoNumber) {
    // implementation hidden...
  }
}
```

If the underlying implementation changes, as long as the Class's method signatures stay the same (the method names and parameters), the programs using the Class aren't affected.

In the world of Computer Science, there are a few "classical" examples that are studied widely and have broad applications: 
* Dictionary/Map
* Stacks
* Queues
* Graphs
  * Linked Lists
  * Doubly Linked Lists
  * Binary Trees

Each of these Abstract Data Types describe different ways to create, read, update, and delete collections of data, each with specific (and highly interesting) algorithms associated with them.

![Thought cloud of cars](../.gitbook/assets/abstract-cars.png)

In the real world, the idea of a car is similar to an abstract data type. All cars have doors, wheels, and an engine and with my driver's license, I can operate pretty much any kind of car. However, each manufacturer has a different approach to creating their cars and, under the hood (literally), they may look very different. You can jump out of a gas-powered sedan and into an electric SUV and drive it immediately. You don't need to know if there's a combustion engine or a lithium-ion battery under the hood: the interface is what matters.

These ADTs give programmers universal "steering wheels" for managing data.

## What is a Stack?

You can visualize the data in a Stack as a pile of pancakes. Stacks follow the "last in, first out" (LIFO) access pattern (the last pancake you add to the stack is the first one you eat).

<img width=400px src="../.gitbook/assets/pancakes.png">

A Stack has three basic operations:

* push — inserts a new element to the "top" of the stack
* pop — removes the top element of the stack
* peek - look at the top element of the stack without removing it

![](./img/stack.png)

Stacks show up in the wild in a few places, such as:
* The "Undo" button: 
  * Every time a user performs an action, push it to the top of the Stack 
  * If the user wants to "Undo" an action, simply pop the most recent action from the Stack
* The call stack:
  * Every time a function is invoked, it is pushed to the top of the call stack
  * When the function returns, it is popped from the stack

### Stack Implementation

If you're thinking — "hey these operations are the same as an Array!" — you're right! In fact, one way we can implement a Stack in JavaScript is with an Array:

```js
class Stack {
  #values = [];

  push(data) {
    this.#values.push(data)
  }

  pop() {
    return this.#values.pop();
  }

  peek() {
    return this.#values[this.#values.length-1];
  }
}

const pancakes = new Stack();
pancakes.push('pancake 1')
pancakes.push('pancake 2')
pancakes.push('pancake 3')
console.log(pancakes.pop());  // pancake 3 (last in, first out)
console.log(pancakes.pop());  // pancake 2
console.log(pancakes.peek()); // pancake 1
```

But remember, the underlying implementation isn't what matters. It is what you can do with those three operations!

### Classic Algorithm: Balanced Brackets

**The Problem**: Given a string containing just the characters `(`, `)`, `{`, `}`, `[` and `]`, determine if the input string is valid.
* Valid Examples: 
  * `{[()]}`
  * `[](){}`
  * `[[[[[[]]]]]]`
* Invalid Examples: 
  * `[(])` (The `(` was opened after the `[`, so it must be closed before the `[` can close).
  * `}}{{`

**<details><summary>Q: What makes a Stack a perfect ADT to solve this problem? How does the LIFO access pattern show up in valid strings versus invalid strings?</summary>**

**Why a Stack is Perfect**: As you read the string from left to right, every time you see an opening bracket, it becomes the "active" bracket waiting for its partner. Because of the LIFO property, the last opening bracket you see is always the very first one that needs to be closed.

For example, in the string `{[()]}` the `(` is the last bracket opened so it must be the first one closed, then the `[` and lastly the `{`.

</details>


#### Implementation Challenge:

**The Algorithm Steps**
1. Initialize an empty stack.
2. Loop through each character in the string:
   1. If it's an opening bracket (`(`, `{`, `[`), push it onto the stack.
   2. If it's a closing bracket (`)`, `}`, `]`), peek at the top of the stack. If the top of the stack is its matching opening partner, pop it off. If it doesn't match (or the stack is empty), the string is invalid.
3. After checking the whole string, if the stack is completely empty (you peek at the top and nothing is there), the brackets are perfectly balanced. If there are still brackets left in the stack, someone forgot a closing bracket!

Here is a visualization of that algorithm: 

{% embed url="https://docs.google.com/presentation/d/1RZ-rg-AuhxZC0XIMrndSVXNfweK__Fca7wA_6eV8-eY/embed?start=false&loop=false&delayms=3000" %}


Use the `Stack` class in your implementation:

```js
class Stack {
  #values = [];

  push(data) {
    this.#values.push(data)
  }

  pop() {
    return this.#values.pop();
  }

  peek() {
    return this.#values[this.#values.length-1];
  }
}

const isBalanced = (str) => {
  // return true if balanced, false otherwise
}
```

**<details><summary>Solution</summary>**

```js
class Stack {
  #values = [];

  push(data) {
    this.#values.push(data);
  }

  pop() {
    return this.#values.pop();
  }

  peek() {
    return this.#values[this.#values.length - 1];
  }
}

function isBalanced(str) {
  // Instantiate your custom Stack class
  const stack = new Stack();
  
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
      // If the stack is empty, peek() returns undefined. 
      // If it's not empty but doesn't match, peek() returns the wrong bracket.
      if (stack.peek() !== bracketPairs[char]) {
        return false; 
      }
      // If it's a perfect match, pop it off
      stack.pop();
    }
  }

  // 3. If peek() is undefined, the stack is completely empty and balanced
  return stack.peek() === undefined;
}

// Test Cases
console.log(isBalanced("{[()]}")); // true
console.log(isBalanced("[(])"));   // false
console.log(isBalanced("[{()"));   // false
```

</details>
