## 📌 Introduction

In this lecture, we study **Tree Data Structures**, focusing on:

- Concepts of general trees and binary trees
- Properties and terminology used with trees
- Various tree representations
- Binary tree traversal methods
- Reconstructing binary trees using traversal information

---

## 📚 Learning Objectives
- Understand what a **tree** is
- Learn about **binary trees** and their structure
- Implement trees in memory (pointers and arrays)
- Perform traversals: inorder, preorder, postorder
- Understand tree properties and reconstruct trees

---

## 🌲 1. What is a Tree?
- A **tree** is a **hierarchical** data structure.
- It consists of **nodes** and **edges**.
- The **top node** is called the **root**.
- Each node can have **child nodes**.
- A **node with no children** is a **leaf node**.

### 🔁 Recursive Definition of Tree
1. An empty structure is an empty tree.
2. If `T1`, `T2`, …, `Tk` are disjoint trees, a new tree `T` can be formed with a root whose children are the roots of `T1...Tk`. These are subtrees of `T`.

---

## 🔸 2. Tree Terminology

| Term                | Meaning |
|---------------------|--------|
| Degree (of node)    | Number of children |
| Degree (of tree)    | Maximum node degree |
| Path                | Sequence of connected nodes |
| Length (of path)    | Number of edges in the path |
| Height (of node)    | Longest path from node to a leaf |
| Height (of tree)    | Height of the root node |
| Depth (of node)     | Path length from root to that node |

---

## 📏 3. Properties of Trees
### Theorem 1

A tree with `n` nodes has exactly `n - 1` edges.

### Theorem 2

If the **sum of degrees** of all nodes = `d`, then total number of nodes = `d + 1`

### Theorem 3

A tree with degree `k` and height `h` can have **max nodes**:

$\text{Max Nodes} = \frac{k^{h+1} - 1}{k - 1}$

### Theorem 4

A tree with `n` nodes and degree `k` has **minimum height**:

$\log_k(n)$

---

## 🧱 4. Tree Representations
### A. Using Struct with Fixed Children
```c
struct node {
  int info;
  struct node* child_1;
  struct node* child_2;
  // ... up to child_k
};
```

### B. Level-wise Representation (Child-Sibling)
```c
struct node {
  int info;
  struct node* firstChild;
  struct node* sibling;
};
```

---

## 🌳 5. Binary Tree
- A **binary tree** is a tree with **at most two children** per node.
- These are called the **left** and **right** child.

### Recursive Definition of Binary Tree
1. An empty structure is a binary tree.
2. If `T1` and `T2` are binary trees, then a tree `T` with root having `T1` as left child and `T2` as right child is also a binary tree.

---

## 💾 6. Binary Tree Representations
### A. Using Pointers
```c
struct node {
  char info;
  struct node* lChild;
  struct node* rChild;
};
```

### B. Using Array
- Root at index `0`
- Left child at `2i + 1`
- Right child at `2i + 2`

---

## 🔍 7. Binary Tree Properties

| Theorem | Statement                                            |
| ------- | ---------------------------------------------------- |
| T5      | Max nodes at level `l` = $2^l$                       |
| T6      | Max nodes of height `h` = $2^{h+1} - 1$              |
| T7      | Min height for `n` nodes = $\log_2 n$                |
| T8      | Max height = `n - 1`                                 |
| T9      | In full binary tree: leaf nodes = internal nodes + 1 |
| T10     | Number of edges = nodes - 1                          |

---

## 🔁 8. Binary Tree Traversal
### A. Inorder
1. Traverse left
2. Process root
3. Traverse right
```c
void inorder(struct node* node) {
  if (node != NULL) {
    inorder(node->lChild);
    printf("%c ", node->info);
    inorder(node->rChild);
  }
}
```

### B. Preorder
1. Process root
2. Traverse left
3. Traverse right
```c
void preorder(struct node* node) {
  if (node != NULL) {
    printf("%c ", node->info);
    preorder(node->lChild);
    preorder(node->rChild);
  }
}
```

### C. Postorder
1. Traverse left
2. Traverse right
3. Process root
```c
void postorder(struct node* node) {
  if (node != NULL) {
    postorder(node->lChild);
    postorder(node->rChild);
    printf("%c ", node->info);
  }
}
```

### Time Complexity: All three are \( O(n) \)

---

## 🏗️ 9. Reconstructing Binary Tree

To reconstruct a **unique binary tree**, you need:

- **Inorder** + **Preorder** OR
- **Inorder** + **Postorder**

### Example

If:

- Inorder = D B E A C F
- Preorder = A B D E C F

You can uniquely reconstruct the tree.

---

## ✅ Summary

| Topic | Key Concept |
|-------|-------------|
| Tree | Hierarchical structure, root and children |
| Binary Tree | Max 2 children, left and right |
| Properties | Nodes, edges, height, depth |
| Representation | Structs, arrays |
| Traversals | Inorder, Preorder, Postorder (\( O(n) \)) |
| Reconstruction | Use preorder/postorder with inorder |

---

Would you like to see visual diagrams for tree types and traversals or practice problems to reinforce this? 🌿
