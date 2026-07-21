# Singly and Doubly Linked Lists

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Singly Linked List](#singly-linked-list)
- [Doubly Linked List](#doubly-linked-list)
- [Tradeoffs](#tradeoffs)
- [Cycles](#cycles)
  - [Check to see if a linked list is a cycle](#check-to-see-if-a-linked-list-is-a-cycle)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is the difference between a singly linked list and a doubly linked list?
2. What are the tradeoffs between a singly linked list and a doubly linked list?
3. What is a cycle in a linked list, and how can you detect one?

## Key Concepts

* **Singly Linked List** - a linked list where each node only stores a pointer to the `next` node, allowing traversal in one direction only (head to tail). Uses less memory than a doubly linked list.
* **Doubly Linked List** - a linked list where each node stores pointers to both the `next` and `prev` node, allowing traversal in both directions. Uses more memory than a singly linked list, but supports constant-time insertion at a known position and reverse traversal.
* **Cycle** - a situation where a node's `next` pointer (directly or indirectly) points back to an earlier node in the list, creating a loop with no end. Traversing a cyclic list without protection will continue indefinitely.

## Singly Linked List

A singly linked list only contains a link (pointer) to the next node, which means that it can only be traversed in one direction from head to tail. This takes up less space in memory.

This is pretty much what we covered in the last lecture.

Lets just add one more method and break it down:

```js
removeFromTail() {
    if (!this.head)  return

    this.#length--;

    if (this.head === this.tail) {
        // Only one node in the list
        const data = this.head.data;
        this.head = null;
        this.tail = null;
        return data;
    }

    let currentNode = this.head;
    while (currentNode.next !== this.tail) {
        currentNode = currentNode.next;
    }

    const data = this.tail.data;
    currentNode.next = null;
    this.tail = currentNode;
    return data;
    }
```

Check if the list is empty: If there are no nodes in the list (i.e., head is null), return null because there's nothing to remove.

Decrease the length: Decrement the length of the list by 1, as we're going to remove a node.

Handle a single-node list: If there's only one node in the list (i.e., head is the same as tail), it means this is the only node in the list. Remove it by setting both head and tail to null, and return the data of the removed node.

Traverse the list to find the node before the tail: Start traversing the list from the head node until you reach the node whose next reference is the current tail node. This node will be the one before the tail.

Remove the tail node: Once you've found the node before the tail, set its next reference to null, effectively removing the current tail node from the list.

Update the tail reference: Update the tail reference to point to the node you found before the tail.

Return the data of the removed node: Finally, return the data of the removed node (which was the tail of the list).

## Doubly Linked List

A doubly linked list contains both a link to the next node and a link to the previous node. This means that it can be traversed in both directions, but takes up more space in memory.

```js
class Node {
  constructor(data = null) {
    this.data = data;
    this.next = null;
    this.prev = null; // Reference to the previous node
  }
}

class LinkedList {
  #length = 0;
  
  constructor() {
    this.head = null;
    this.tail = null;
  }

  appendToTail(data) {
    const newNode = new Node(data);
    this.#length++;
    
    if (!this.head && !this.tail) {
      this.head = newNode;
    } else {
      newNode.prev = this.tail;
      this.tail.next = newNode;
    }
    
    this.tail = newNode;
  }

  prependToHead(data) {
    const newNode = new Node(data);
    this.#length++;

    if (!this.head && !this.tail) {
      this.tail = newNode;
    } else {
      newNode.next = this.head;
      this.head.prev = newNode;
    }

    this.head = newNode;
  }

  removeHead() {
    const data = this.head.data;
    this.head = this.head.next;
    if (this.head) {
      this.head.prev = null;
    } else {
      this.tail = null;
    }
    this.#length--;
    return data;
  }

  contains(data) {
    let currentNode = this.head;
    while (currentNode) {
      if (currentNode.data === data) {
        return true;
      }
      currentNode = currentNode.next;
    }
    return false;
  }

  length() {
    return this.#length;
  }
}
```

## Tradeoffs

* Doubly linked lists require more memory (they must store the data value, the next pointer, and the previous pointer)

* Doubly linked lists can be traversed in reverse (singly linked lists can't)

* Inserting into the middle of a doubly linked list can be done in constant time (assuming you have a pointer to where it should be inserted).

* Singly linked lists should be used when memory is limited. Doubly linked lists should be used when memory is not limited and searching is required.

## Cycles

A cycle in a Linked list is a situation where the last node of the list points to one of the previous nodes, forming a loop. This means that there is no end to the list and traversing the list will continue indefinitely.

```js
const list = new LinkedList();
list.appendToTail(1);
list.appendToTail(2);
list.appendToTail(3);
list.appendToTail(4);

// create a cycle by making the last node point to the first node
list.tail.next = list.head;
```

### Check to see if a linked list is a cycle

* Start with a function that takes in a given node as the starting point (headNode) of the linked list.

* Create an empty array (object or map) called nodesEncountered to keep track of nodes we've seen.

* Begin traversing the linked list, starting from the headNode.
* While traversing:
  * Check if the current node has been encountered before by looking it up in the nodesEncountered array.
  * If the current node has been encountered before, it means there is a cycle in the linked list, so return true.
  * If the current node has not been encountered before, add it to the nodesEncountered array and move to the next node in the list.
  * If the traversal completes without finding any cycles (i.e., no repeated nodes are encountered), return false, indicating that the linked list does not contain a cycle.

[Visual examples](https://visualgo.net/en/list)
