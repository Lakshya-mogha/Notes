
---

## 📌 Introduction

This lecture focuses on **Queues**, a fundamental linear data structure in computer science. We will explore:

- What queues are and how they work
- Various types of queues
- Implementations using arrays and linked lists
- Advanced structures like circular, double-ended, monotonic, and priority queues
- Applications and real-world use cases

---

## 📚 Learning Objectives
- Understand the behaviour and operations of queues
- Implement queues using arrays and linked lists
- Learn and apply advanced variants of queues
- Realise stacks using queues and vice versa
- Explore applications in operating systems and networks

---

## 🔹 1. What is a Queue?
- A **queue** is a linear, homogeneous data structure.
- It follows **FIFO (First-In First-Out)** or **LILO (Last-In Last-Out)** principle.
- **Enqueue**: Adds an item to the **rear/tail**.
- **Dequeue**: Removes an item from the **front/head**.

---

## 🔹 2. Common Queue Operations

| Operation      | Description                                |
|---------------|--------------------------------------------|
| Enqueue       | Insert element at the rear                 |
| Dequeue       | Remove element from the front              |
| isEmpty       | Check if queue is empty                    |
| isFull        | Check if queue is full                     |
| headElement   | Return the front element                   |
| tailElement   | Return the rear element                    |

---

## 🔹 3. Queue Implementations
### A. Using Arrays (Fixed Size)
- `storage[MAX]` stores elements.
- `Head` and `Tail` initialized to -1.
- Enqueue: `Tail++`, Dequeue: `Head++`
- Queue is full when `Tail == MAX - 1`
- Queue is empty when `Head == Tail`

### 🔄 Scenario: Enqueue/Dequeue Flow
```text
Initial: Head = -1, Tail = -1
Enqueue(5) → Tail = 0
Enqueue(10) → Tail = 1
Dequeue() → Head = 0
```

### B. Scenario 2 – Always shift elements to start
- Dequeue shifts all elements forward (costly).
- Enqueue complexity: `O(1)`
- Dequeue complexity: `O(n)`

---

## 🔹 4. Circular Queue
- Uses modulo: `(Tail + 1) % MAX` and `(Head + 1) % MAX`
- Queue is empty: `Head == Tail`
- Queue is full: `(Tail + 1) % MAX == Head`
- Avoids unused space

### ✅ Time Complexity
- Enqueue: `O(1)`
- Dequeue: `O(1)`

---

## 🔹 5. Queue Using Linked Lists
### Types
- Singly Linked List
- Doubly Linked List
- Circular Linked List

### Operations
- Enqueue: Add at tail (last node)
- Dequeue: Remove from head (first node)

---

## 🔹 6. Types of Queues
### A. Double Ended Queue (Deque)
- **Push_Front()**: Insert at head
- **Push_Back()**: Insert at tail
- **Pop_Front()**: Remove from head
- **Pop_Back()**: Remove from tail

#### Types
- Input-restricted deque: Insertion only at one end
- Output-restricted deque: Deletion only at one end

### B. Monotonic Queue
- Elements maintained in sorted order (increasing/decreasing)

### C. Priority Queue
- Elements removed based on their priority, not order

---

## 🔹 7. Applications of Queues
1. Process Scheduling (OS)
2. Memory Management
3. Queue systems (counters, service lines)
4. Job scheduling (datacenters)
5. Packet routing (networking)

---

## 🔹 8. Stack Using Two Queues
### Concept
- Use two queues `Q1` and `Q2`
- Always maintain last pushed item at front of `Q1`
- Push: Transfer `Q1` to `Q2`, enqueue new element to `Q2`, move back to `Q1`
- Pop: Dequeue from `Q1`

---

## 🔹 9. Queue Using Two Stacks
### Concept
- Use two stacks `S1` and `S2`
- Enqueue: Move all from `S1` to `S2`, push new element to `S2`, move all back to `S1`
- Dequeue: Pop from `S1`

---

## ✅ Summary Table

| Queue Type           | Key Feature                          |
|----------------------|--------------------------------------|
| Linear Queue         | FIFO behavior                        |
| Circular Queue       | Wrap-around using modulo             |
| Double Ended Queue   | Insert/remove from both ends         |
| Monotonic Queue      | Maintains sorted order               |
| Priority Queue       | Removes based on priority            |
| Linked List Queue    | Dynamic memory, flexible             |
| Queue via Stacks     | Emulates queue using stack logic     |
| Stack via Queues     | Emulates stack using queue logic     |

---

## ✍️ Practice Ideas
- Implement all queue variants in C or Python
- Convert stack operations using queues and vice versa
- Use queues to solve problems like **sliding window maximum**, **task scheduling**, etc.

Let me know if you’d like code examples or MCQs to revise these topics! 🚀
