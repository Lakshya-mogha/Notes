This guide breaks down all the topics in your `week_9.pdf` notes. Focus on the **"How-To" sections** and the **example** to understand how to solve the problems.

---

## 1. The Simplex Method: The Big Picture

* **What is it?** It's a mathematical algorithm [cite: 14] used to find the **best solution** (like maximum profit or minimum cost) for a linear programming (LP) problem[cite: 14].
* **How does it work?** An LP problem has a function and constraints (inequalities)[cite: 14]. These constraints form a shape called a polyhedron. The best solution is always at one of the corners (vertices) of this shape[cite: 16].
* **The Process:** The Simplex method is a step-by-step process [cite: 17] that starts at one vertex and moves to an adjacent vertex, improving the solution at each step until it finds the best one[cite: 17].
* **Core Skill:** To do this, it relies heavily on solving systems of linear equations using **row operations**[cite: 18, 23].

---

## 2. Core Skill: Solving Linear Equations Using Row Operations

This is the fundamental tool you need. The Simplex method uses this to move from vertex to vertex.

### What are the 3 Elementary Row Operations?

You can manipulate a matrix (or system of equations) using these three moves[cite: 27, 28]:

1.  **Interchange:** Swap any two rows[cite: 34].
2.  **Multiply:** Multiply one entire row by a **non-zero** number[cite: 35].
3.  **Add:** Add a scalar multiple of one row to another row[cite: 36].

> **Key Concept: Elementary Matrices**
> * Doing a row operation is the same as pre-multiplying your matrix `A` by a corresponding "elementary matrix" (`E`)[cite: 37, 98].
> * **Type 1 (Swap):** An identity matrix with two rows swapped[cite: 40].
> * **Type 2 (Scale):** An identity matrix with one row multiplied by a number $\alpha$[cite: 58].
> * **Type 3 (Add):** An identity matrix with a number $\beta$ added to an off-diagonal spot[cite: 74, 75].

### How to Use Row Operations (The "How-To")

You'll use these operations for two main tasks:

#### Task 1: Finding the Inverse of a Matrix ($A^{-1}$)

1.  Create an "augmented matrix" by placing the identity matrix `I` next to your matrix `A`: **`[A, I]`**[cite: 126, 127].
2.  Use the 3 row operations to transform the `A` part on the left into the identity matrix `I`[cite: 129].
3.  As you do this, the `I` part on the right will automatically turn into the inverse, $A^{-1}$[cite: 130].
4.  Your final matrix will look like **`[I, A_inv]`**[cite: 130].

#### Task 2: Solving a System of Equations ($Ax=b$)

1.  Create an augmented matrix **`[A, b]`**[cite: 136].
2.  Use row operations to transform `A` (on the left) into the identity matrix `I`[cite: 137].
3.  The `b` vector (on the right) will transform into a new vector, let's call it $\tilde{b}$[cite: 137].
4.  The final matrix will be **$[I, \tilde{b}]$**.
5.  The solution to your system is simply **$x = \tilde{b}$**[cite: 138].

---

## 3. The Canonical Augmented Matrix

This is the goal of your row operations at each step of the Simplex method.

* **What is it?** A system $Ax=b$ is in **canonical form** if, among all your variables, you have $m$ special variables (where $m$ is the number of equations) that have two properties[cite: 172]:
    1.  Each one appears in **only one** equation[cite: 173].
    2.  Its coefficient in that equation is **1**[cite: 173].
* **What it looks like:** The matrix part will have an identity matrix ($I_m$) embedded in it[cite: 171]. After re-ordering, it looks like **$[I_m, Y, y_0]$**[cite: 171, 178].

### Key Vocabulary

* **Basic Variables:** The $m$ variables that form the identity matrix ($I_m$)[cite: 179]. In the notes, they assume these are the first $m$ variables, $x_1, ..., x_m$[cite: 180].
* **Non-Basic Variables:** All the *other* variables[cite: 179].
* **Basic Solution:** This is the solution you get from a canonical matrix. It's the most important concept.

### How to Find the Basic Solution

1.  Take your canonical augmented matrix $[I_m, Y, y_0]$.
2.  Set all **non-basic variables** (the ones not in your $I_m$) **to 0**[cite: 186, 187].
3.  The **basic variables** are then equal to the corresponding values in the final solution column, $y_0$[cite: 184, 186].
4.  The solution vector $x$ is just $[y_0^T, 0^T]^T$[cite: 188].

### **EXAMPLE (This is the most important part to understand!)**

Your notes give this example:

* **Original Matrix `[A, b]`:**
    $[\begin{matrix}1&1&-1&4&8\\ 1&-2&-1&1&2\end{matrix}]$ [cite: 199]

* **After Row Operations, it becomes the Canonical Matrix:**
    $[\begin{matrix}1&0&-1&3&6\\ 0&1&0&1&2\end{matrix}]$ [cite: 200, 207]

* **How to read this:**
    * The first two columns form an identity matrix $[\begin{matrix}1&0\\0&1\end{matrix}]$.
    * This means **$x_1$ and $x_2$ are the BASIC variables**[cite: 204].
    * Therefore, **$x_3$ and $x_4$ are the NON-BASIC variables**.
    * The solution column $y_0$ is $[\begin{matrix}6\\ 2\end{matrix}]$[cite: 200, 207].

* **Find the Basic Solution:**
    1.  Set non-basic variables to 0: **$x_3 = 0$**, **$x_4 = 0$**[cite: 186, 187].
    2.  Read the solution for the basic variables from the matrix[cite: 184]:
        * Row 1 says: $1x_1 + 0x_2 - 1x_3 + 3x_4 = 6$
        * Substitute non-basics: $x_1 - 1(0) + 3(0) = 6$ => **$x_1 = 6$**
        * Row 2 says: $0x_1 + 1x_2 + 0x_3 + 1x_4 = 2$
        * Substitute non-basics: $x_2 + 0(0) + 1(0) = 2$ => **$x_2 = 2$**
    3.  The final **basic solution** is $x = [6, 2, 0, 0]^T$[cite: 203].

---

## 4. Updating the Augmented Matrix (Pivoting)

This is the *action* of the Simplex method. It's how you move from one basic solution to the next.

* **The Goal:** You want to swap one of your basic variables (let's call it $x_p$) with a non-basic variable (let's call it $x_q$)[cite: 231, 232].
* **Why?** To see if the solution gets better.
* **The "Pivot Element":** You'll choose a specific element in the matrix to "pivot" on. This element is $y_{pq}$, which is in the row $p$ of the variable you're *removing* and the column $q$ of the variable you're *adding*[cite: 261].
    * This only works if the pivot element $y_{pq}$ is **not zero**[cite: 243].

### How to Pivot (The "Pivot Equations")

This looks scary, but it's just a recipe. You are turning the pivot column $q$ into a new identity matrix column (all zeros, with a 1 at the pivot element)[cite: 263].

Let $y_{ij}$ be the *old* matrix entries.

Let $y_{ij}^{\prime}$ be the *new* matrix entries you're calculating[cite: 253].

Let $y_{pq}$ be your chosen pivot element (row $p$, col $q$).

**There are two rules to create the new matrix:**

1.  **For the Pivot Row (row $p$):**
    * Divide every element in the pivot row by the pivot element $y_{pq}$[cite: 254].
    * **Formula:** $y_{pj}^{\prime} = \frac{y_{pj}}{y_{pq}}$ [cite: 254] (This makes the pivot element itself become 1).

2.  **For ALL Other Rows (where $i \ne p$):**
    * Use this formula for every element in the row[cite: 254].
    * **Formula:** $y_{ij}^{\prime} = y_{ij} - \frac{y_{pj}}{y_{pq}} \cdot y_{iq}$ [cite: 254]
    * **In simpler terms:** `New Value = Old Value - (Old Value in Pivot Column * New Pivot Row Value)`

This operation, "pivoting," [cite: 262] creates a new canonical matrix[cite: 258], and you can read a new basic solution from it. The Simplex method repeats this until it finds the best solution.

---

Good luck on your exam. Focus on the **how-to** steps, especially the **example** of reading a basic solution and the **two rules for pivoting**. You can do this.