# 6-0-1-linked-list-continued

## Singly and Doubly Linked list

### Singly Linked list

A singly Linked list only contains a link (pointer) to the next node, which means that it can only be traversed in one direction from head to tail. This takes up less space in memory.

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

### Doubly Linked list

A doubly Linked list contains both a link to the next node and a link to the previous node. This means that it can be traversed in both directions, but takes up more space in memory.

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

## tradeoffs

* Doubly linked lists require more memory (they must store the data value, the next pointer, and the previous pointer)

* Doubly linked lists can be traversed in reverse (singly linked lists can't)

* Inserting into the middle of a doubly linked list can be done in constant time (assuming you have a pointer to where it should be inserted).

* Singly linked lists should be used when memory is limited. Doubly linked lists should be used when memory is not limited and searching is required.

## Cycles

A cycle in a Linked list is a situation where the last node of the list points to one of the previous nodes, forming a loop. This means that there is no end to the list and traversing the list will continue indefinitely.

```js
const list = new LinkedList();
list.append(1);
list.append(2);
list.append(3);
list.append(4);

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
