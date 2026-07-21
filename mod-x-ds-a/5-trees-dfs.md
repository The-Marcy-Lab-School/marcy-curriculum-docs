# Depth-First Search & BFS vs. DFS

- [Essential Questions](#essential-questions)
- [Key Concepts](#key-concepts)
- [Problem: Exploring Full Paths Before Backtracking](#problem-exploring-full-paths-before-backtracking)
- [DFS Implementation](#dfs-implementation)
- [Traversal Orderings: Pre-, In-, and Post-Order](#traversal-orderings-pre--in--and-post-order)
- [BFS vs. DFS](#bfs-vs-dfs)
- [Traversal on a Grid](#traversal-on-a-grid)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. How does Depth-First Search use a Stack to explore a tree fully before backtracking?
2. What is DFS's relationship to the call stack, and to the Stack ADT from earlier in this module?
3. What distinguishes pre-order, in-order, and post-order traversal, and when is each useful?
4. Given a problem, how do you decide whether BFS or DFS is the better fit?
5. How do BFS and DFS extend to traversing a 2D grid?

## Key Concepts

* **Depth-First Search (DFS)** - a traversal algorithm that explores as far down one branch as possible before backtracking, using a Stack to keep track of which nodes to return to.
* **Pre-order** - a DFS ordering that visits a node *before* its children.
* **In-order** - a DFS ordering (binary trees only) that visits the left subtree, then the node, then the right subtree.
* **Post-order** - a DFS ordering that visits a node's children *before* the node itself.
* **Backtrack** - to return to a previously visited node in order to explore a different branch.

## Problem: Exploring Full Paths Before Backtracking

BFS answers "what's here at this level, before moving deeper?" But plenty of problems ask the opposite question: **explore one path as far as it goes, and only back up when you hit a dead end.** Think of:

* Solving a maze — you commit to a corridor and follow it until it dead-ends, then backtrack to the last fork and try a different direction.
* Checking whether *any* full path through a decision tree leads to a valid outcome — you need to walk all the way to a leaf before you know.
* Aggregating a value that depends on a node's entire subtree (e.g. "total size of everything inside this folder") — you can't answer for a folder until you've fully finished exploring everything nested inside it.

<details>

<summary><strong>Q: BFS uses a Queue because it needs FIFO order — the node discovered first gets visited first. What ordering guarantee does "go deep before backtracking" actually need?</strong></summary>

LIFO — **last in, first out**. When you reach a fork with multiple unexplored children, you want to dive into the *most recently discovered* branch immediately, not wait until every other pending branch has been explored first (which is what a Queue/FIFO would do). That's exactly the ordering guarantee a **Stack** provides — which is exactly why this traversal is named after it: Depth-**First** Search, using a Stack.

</details>

## DFS Implementation

Just like BFS reused the `Queue` class from earlier in this module, DFS reuses the `Stack` class:

```js
function dfs(root) {
  if (!root) return [];

  const visited = [];
  const stack = new Stack();
  stack.push(root);

  while (stack.peek()) {
    const node = stack.pop();
    visited.push(node.value);

    // push right first so left is popped (and explored) first
    if (node.right) stack.push(node.right);
    if (node.left) stack.push(node.left);
  }

  return visited;
}
```

<details>

<summary><strong>Q: Why does this push `node.right` before `node.left`, when BFS enqueued `node.left` before `node.right`?</strong></summary>

Because a Stack pops whatever was pushed *most recently*. If `left` should be explored first, it needs to be the last one pushed — so `right` has to go on the Stack first. This is a common trip-up: with a Stack, the order you push in is the *reverse* of the order you'll pop in, which is the opposite of how a Queue behaves.

</details>

Compare this to the BFS implementation from the last lesson — the two algorithms are nearly identical, differing only in which ADT manages "what to visit next":

```
        8
       / \
      3   10
     / \    \
    1   6    14
```

* BFS visits: `8, 3, 10, 1, 6, 14` (level by level)
* DFS visits: `8, 3, 1, 6, 10, 14` (as far left as possible, then backtrack)

## Traversal Orderings: Pre-, In-, and Post-Order

The DFS implementation above visits a node the moment it's popped — *before* looking at its children. That's called **pre-order** traversal. But the position of "visit the node" relative to exploring its children can change, producing three distinct orderings (most naturally expressed recursively, which you'll revisit in the next lesson):

| Ordering       | Order             | Use case                                                                                                                |
| -------------- | ----------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Pre-order**  | node, left, right | Copying/serializing a tree (you need the parent before its children to reconstruct it)                                  |
| **In-order**   | left, node, right | On a BST specifically, this visits every value in **sorted order**                                                      |
| **Post-order** | left, right, node | Aggregating a value that depends on a node's children (e.g. deleting a tree safely, or computing a folder's total size) |

<details>

<summary><strong>Q: Why does in-order traversal visit a BST's values in sorted order?</strong></summary>

Recall the BST ordering rule: everything in a node's left subtree is smaller, everything in its right subtree is larger. In-order traversal visits the *entire* left subtree (all smaller values) before the node itself, and the entire right subtree (all larger values) after — so at every node, "everything visited so far" is smaller and "everything left to visit" is larger. Applied recursively at every node in the tree, this produces values in strictly increasing order.

</details>

## BFS vs. DFS

|                    | BFS                                                | DFS                                                                  |
| ------------------ | -------------------------------------------------- | -------------------------------------------------------------------- |
| Underlying ADT     | Queue (FIFO)                                       | Stack (LIFO)                                                         |
| Explores           | Level by level                                     | Fully down one branch, then backtracks                               |
| Best for           | Shortest path (unweighted), level-order processing | Exploring full paths, subtree aggregation, "does any path satisfy X" |
| Space (worst case) | O(width) — can hold an entire level at once        | O(height) — only holds one path at a time                            |

<details>

<summary><strong>Q: For a very "wide" but "shallow" tree (many siblings, few levels), which traversal uses less memory — BFS or DFS?</strong></summary>

DFS. BFS's Queue would have to hold every node at the widest level all at once — potentially very large. DFS's Stack only ever holds nodes along the *current path* from root to wherever it currently is, which is bounded by the tree's height, not its width.

</details>

Given a new problem, decide between them by asking:

* "Do I need the shortest path, or to process level by level?" → **BFS**
* "Do I need to explore a full path before I can conclude anything, or aggregate values bottom-up?" → **DFS**

## Traversal on a Grid

Both BFS and DFS extend directly to a 2D grid (a matrix) — a grid is really just a tree/graph in disguise, where each cell's "children" are its (up to four) neighboring cells.

**Classic problems that are grid traversal wearing a disguise:**

* **"Count islands"** — given a grid of `1`s (land) and `0`s (water), count the number of connected groups of land. Each unvisited land cell is the "root" of a new traversal (BFS or DFS both work) that marks every connected land cell as visited, so it isn't counted again.
* **"Shortest path in a maze"** — given a grid with open and blocked cells, find the shortest path from a start cell to an end cell. Since this asks for *shortest* path, this is a direct application of BFS's shortest-path guarantee, extended from tree nodes to grid cells.

```js
function countIslands(grid) {
  const visited = new Set();
  let islands = 0;

  function exploreIsland(row, col) {
    const key = `${row},${col}`;
    const inBounds = row >= 0 && row < grid.length && col >= 0 && col < grid[0].length;
    if (!inBounds || visited.has(key) || grid[row][col] === 0) return;

    visited.add(key);
    exploreIsland(row + 1, col);
    exploreIsland(row - 1, col);
    exploreIsland(row, col + 1);
    exploreIsland(row, col - 1);
  }

  for (let row = 0; row < grid.length; row++) {
    for (let col = 0; col < grid[0].length; col++) {
      if (grid[row][col] === 1 && !visited.has(`${row},${col}`)) {
        exploreIsland(row, col);
        islands++;
      }
    }
  }

  return islands;
}
```

<details>

<summary><strong>Q: `exploreIsland` above is written recursively, even though this lesson has otherwise stuck to iterative Stack/Queue implementations. What does each recursive call correspond to, in DFS terms?</strong></summary>

Each recursive call is the equivalent of one "visit a node, then push its unvisited neighbors" step from the iterative DFS implementation — except here, the call stack is doing the pushing/popping automatically instead of an explicit `Stack`. This is a preview of the connection the next lesson covers in depth: recursion and DFS are two ways of expressing the exact same traversal.

</details>
