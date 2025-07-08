# 📘 Lecture 4 Notes: Median, Randomized Algorithms & Hashing

### 🧠 Instructor: Dr. Chiranjib Sur

---

## 🔹 Overview

In this lecture, we explore advanced algorithmic concepts, including:

- Revisiting **Median and Selection** problems
- Understanding **Randomized Algorithms** through Quick Sort
- Applying **Genetic Algorithms** to NP-Hard problems like 0/1 Knapsack
- Diving deep into **Hashing Techniques** (Static & Dynamic)

---

## 📌 1. What is Randomization?

Randomization involves the use of **random numbers** or choices during algorithm execution. It’s useful in:

- Improving **average performance**
- Avoiding **worst-case input patterns**
- Designing robust and scalable algorithms

✅ **Randomized Algorithm**: Uses random decisions to solve a problem efficiently.

![Randomized Algorithm Illustration](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6a/Randomized_quicksort_example.svg/640px-Randomized_quicksort_example.svg.png)

---

## 📌 2. Quick Sort as a Randomized Algorithm

Quick Sort can be randomized by choosing the pivot randomly:

- **Best Case**: Pivot splits array evenly: \( O(n \log n) \)
- **Worst Case**: Highly unbalanced splits: \( O(n^2) \)
- **Randomization helps** avoid the worst case on average.

---

## 📌 3. 0/1 Knapsack Problem
**Problem**:
- Given `N` items each with `weight[i]` and `profit[i]`
- A knapsack of capacity `W`
- Objective: Maximize profit such that total weight ≤ W

**Example**:
```text
N = 3, W = 4
Profit = [1, 2, 3], Weight = [4, 5, 1]
Best: Pick item with weight 1 → Profit = 3
```

🧠 **0/1 Knapsack is NP-Hard**: Solutions grow as \( 2^N \).

---

## 📌 4. Genetic Algorithm (GA)

A **heuristic optimization algorithm** inspired by natural evolution.

![Genetic Algorithm Cycle](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0c/Genetic_algorithm_diagram.svg/500px-Genetic_algorithm_diagram.svg.png)

### Steps in GA
1. **Selection** – Choose chromosomes (solutions)
2. **Crossover** – Combine parts of two chromosomes
3. **Mutation** – Randomly alter genes
4. **Fitness Evaluation** – Score based on problem objective

### 🧬 Solving Knapsack with GA
- Chromosomes: Binary strings (e.g. `{0,1,1,0,0}`)
- Fitness: Total profit of selected items
- Constraints: Total weight ≤ capacity `W`

```text
Input: N = 5, W = 4
Profit = [1, 2, 3, 1, 1], Weight = [4, 5, 1, 8, 2]
Example Best: {1,1,0,1,0} → Profit = 4
```

### ✅ Why Use GA?
- General-purpose
- Works on complex, large-scale problems
- Not limited to a specific domain

---

## 📌 5. Hashing
### 🔑 Hash Function

Maps keys to indices in a table for efficient access.

### ✅ Properties
- **Deterministic or Non-deterministic**
- **Static or Dynamic**

### 📂 Static Hashing Methods

#### A. Linear Probing (Open Addressing)
- On collision, search for next empty slot linearly.
- Pros: Simple
- Cons: Clustering can cause performance drop

![Linear Probing](https://upload.wikimedia.org/wikipedia/commons/thumb/2/24/Hash_table_5_0_1_1_1_1_1_SP.svg/500px-Hash_table_5_0_1_1_1_1_1_SP.svg.png)

#### B. Cuckoo Hashing
- Uses multiple hash functions
- If all possible positions are full, evict and reinsert
- Ensures **constant lookup time**

![Cuckoo Hashing](https://upload.wikimedia.org/wikipedia/commons/thumb/6/62/Cuckoo_hashing_example.svg/500px-Cuckoo_hashing_example.svg.png)

### 📂 Dynamic Hashing Methods

#### A. Chained Hashing
- Each table slot holds a linked list of entries
- Handles collisions gracefully

![Chained Hashing](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4c/Hash_table_linked_list.svg/500px-Hash_table_linked_list.svg.png)

#### B. Extendible Hashing
- Buckets grow as needed, pointers may be redirected
- Splits only affected buckets → Localized updates

#### C. Linear Hashing
- Hash table grows incrementally
- Uses a **split pointer** to determine next bucket to split
- Avoids complete rehashing of the table

---

## ✅ Summary

| Concept | Description |
|--------|-------------|
| **Randomization** | Random choices in algorithm execution to improve performance |
| **Quick Sort** | Classic example of a randomized divide-and-conquer algorithm |
| **0/1 Knapsack** | NP-Hard optimization problem |
| **Genetic Algorithm** | Evolution-inspired method for solving optimization problems |
| **Hashing** | Efficient technique for data retrieval using hash functions |

---

## 🧩 Tips to Ace This Topic
- Practice implementing **Quick Sort** with randomized pivoting
- Understand how **binary strings** represent solutions in Genetic Algorithms
- Implement a simple **hash table** with both linear probing and chaining
- Solve **Knapsack** using both brute force and Genetic Algorithm approaches

Let me know if you'd like detailed code examples or a practice sheet for any of these topics! 🚀
