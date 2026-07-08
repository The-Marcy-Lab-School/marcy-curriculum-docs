# Nodes & Linked Lists

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Nodes](#nodes)
    - [Linked Lists](#linked-lists)
    - [Doubly Linked Lists](#doubly-linked-lists)
    - [Trees](#trees)
- [Making a Node Class for Linked Lists](#making-a-node-class-for-linked-lists)
- [Making a Linked List Class](#making-a-linked-list-class)
- [Algorithm: Prepend to head](#algorithm-prepend-to-head)
- [Algorithm: Append to tail](#algorithm-append-to-tail)
- [Algorithm: isCyclic](#algorithm-iscyclic)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is a graph, and what do all graphs have in common?
2. What is a node? What does the `head` of a linked list refer to? What does the `tail` refer to?
3. What are the tradeoffs between linked lists and arrays?
4. What are the tradeoffs between singly linked lists and doubly linked lists?
5. What are the run times for insertion, deletion, and accessing values in a linked list?

## Key Concepts

* **Graph** - a category of abstract data type used to organize collections of data with *relationships*. All graphs are made up of nodes containing each data point and edges connecting them.
  * **Node** - a single unit of data storage in a graph. Depending on the structure, a node may point to a `next`, `prev` (previous), `parent`, or `children` node.
  * **Edge** - a connection between two nodes.
* **Linked List** - a graph structure made of nodes chained together in a single-file line, where each node points to the `next` node in the sequence. To access any node in the list, you must start at the `head` node of the list and traverse through the `next` pointers of each node.
  * **Singly Linked List** - a linked list where each node *only* points to the `next` node, allowing traversal in one direction only.
  * **Doubly Linked List** - a linked list where each node points to both the `next` and `prev` node, allowing traversal in both directions.
  * **`head`** - the first node in a linked list.
  * **`tail`** - the last node in a linked list.
* **Traverse** - to visit the nodes of a data structure in a particular order.
* **Random Access** - the ability to access any element in a collection directly (e.g. by index), regardless of its position. Arrays support random access.
* **Sequential Access** - the requirement to visit elements of a collection in order, starting from the beginning, to reach a given element. Linked lists only support sequential access.

## Nodes

A **Graph** is a category of abstract data type that is used to organize relationships between data.

The thing that all graphs share is that they are comprised of **nodes** that hold a single piece of data, and **edges** that connect two nodes.

**<details><summary>Q: Consider the abstract data structures below. What do the nodes in each structure point to?</summary>**

* In a **linked list**, each node points to the `next` node in the list.
* In a **doubly linked list**, each node points to both the `next` and `prev` node.
* In a **tree**, each node points to its `children` nodes (and sometimes a `parent` node).

</details>

#### Linked Lists

![](../.gitbook/assets/linked-list.png)

#### Doubly Linked Lists

![](../.gitbook/assets/doubly-linked-list.png)

#### Trees

![](../.gitbook/assets/tree.jpeg)

## Making a Node Class for Linked Lists

Nodes themselves typically do not have any methods.

The simplest kind of node is the one used in a linked list. It holds its own data and a pointer to the `next` node in the list.

![](../.gitbook/assets/linked-list.png)

```js
// depending on how the node is used, it may have a next, prev, parent, or children, property
class Node {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}

const nodeA = new Node("a");
const nodeB = new Node("b");
const nodeC = new Node("c");

nodeA.next = nodeB;
nodeB.next = nodeC;

console.log(nodeA, nodeB, nodeC); // What do you expect to see?
```

**<details><summary>Q: What is the head of a linked list? What is the tail?</summary>**

The `head` is the first node in the linked list — the entry point for traversing the rest of the list. The `tail` is the last node in the list, identifiable because its `next` pointer is `null`.

</details>

## Making a Linked List Class

![](../.gitbook/assets/linked-list.png)

The linked list itself holds only a reference to a `head` node and various methods for modifying or "traversing" the list.

```js
class LinkedList {
    constructor() {
        this.head = null;
    }
    
    appendToTail(data) {}
    prependToHead(data) {}
    removeFromHead() {}
    removeFromTail() {}
    contains() {}
}
```

> "Traversing" is a fancy word for "visiting the nodes in a particular order" in a data structure.

**<details><summary>Q: What are the ways that we can traverse a linked list?</summary>**

A singly linked list can only be traversed in one direction: starting at the `head` and following each node's `next` pointer until reaching the `tail`. A doubly linked list can also be traversed in reverse, starting at the `tail` and following each node's `prev` pointer back to the `head`.

</details>

Some linked lists may also implement:

* adding a new node in the middle
* removing a node from the middle

**Let's visualize: https://visualgo.net/en/list**

## Algorithm: Prepend to head

* Inputs: data to add
* Output: the new `head` of the linked list
* Behavior: the new node should be the new `head` of the linked list and it should point to the previous `head` of the linked list

```js
const list = new LinkedList();
list.prependToHead('a')
list.prependToHead('b')
list.prependToHead('c')
console.log(list.head);
console.log(list.head.next);
console.log(list.head.next.next);
// Node { data: 'c', next: Node }
// Node { data: 'b', next: Node }
// Node { data: 'a', next: null }
```

**<details><summary>Solution</summary>**

```js
class LinkedList {
    constructor() {
        this.head = null;
    }
    prependToHead(data) {
        const newNode = new Node(data);
        newNode.next = this.head;
        this.head = newNode;
    }
}
```

1. The new node is going at the beginning of the list. So it's `next` pointer should point to the existing `head` of the list.
2. Then, the list's `head` pointer should now point at the new node.
3. Test:
   * Adding to a list with multiple nodes
   * Adding to an empty list
   * Adding to a list with one value

</details>

## Algorithm: Append to tail

* Inputs: data to add
* Output: the `head` of the linked list
* Behavior: the previous tail node's `next` property should point to the new node.

```js
const list = new LinkedList();
list.appendToTail('a')
list.appendToTail('b')
list.appendToTail('c')
console.log(list.head);
console.log(list.head.next);
console.log(list.head.next.next);
// Node { data: 'a', next: Node }
// Node { data: 'b', next: Node }
// Node { data: 'c', next: null }
```

**<details><summary>Solution</summary>**

```js
class LinkedList {
    constructor() {
        this.head = null;
    }
    prependToHead(data) { /* ... */ }
    
    appendToTail(data) {
        const newNode = new Node(data);
        if (!this.head) {
            this.head = newNode;
        } 
        else {
            let currNode = this.head;
            while (currNode.next !== null) {
                currNode = currNode.next;
            }
            currNode.next = newNode;
        }
    }
}
```

1. To put the new node at the end of the list, we need to first get to the end of the list, starting at the list's `head`. We'll use a `currNode` variable to keep track of where we are in the list.
2. Using a `while` loop, we iterate as long as the `currNode` has a `next` node to move to.
3. We'll reach the tail node once `currNode` has no `next` node. At this point, we set the `currNode` (which is the tail) to point to the new node.
4. Test:
   * Adding to a list with multiple nodes
   * Adding to an empty list
   * Adding to a list with one node

</details>

## Algorithm: isCyclic

This is not a method of linked lists but a method whose _input_ is the head of a linked list. It should return `true` if the linked list contains a cycle, `false` otherwise.

```js
const list = new LinkedList();

const nodeA = new Node("a");
const nodeB = new Node("b");
const nodeC = new Node("c");

list.head = nodeA;

nodeA.next = nodeB;
nodeB.next = nodeC;

isCyclic(list.head); // false

nodeC.next = nodeA; // a cycle!

isCyclic(list.head); // true
```

**<details><summary>Solution</summary>**

```js
function isCyclic(headNode) {

    let nodesEncountered = []; // track nodes we've seen
    
    let currentNode = headNode; // track the current node in our traversal
    
    while(currentNode) { // eventually it will be null
        
        // if we've encountered it before...
        if (nodesEncountered.includes(currentNode)) {
            return true; // we found a cycle!
        } 
        
        // otherwise...
        nodesEncountered.push(currentNode); // add it to the encountered list
        currentNode = currentNode.next; // traverse to the next node
    }
    
    return false;
}
```

</details>
