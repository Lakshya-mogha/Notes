This lecture covers several key sorting algorithms, from the classic comparison-based Merge Sort to linear-time non-comparison sorts like Counting Sort, Radix Sort, and Bucket Sort. 1

## 1. Merge Sort

Merge Sort is a quintessential sorting algorithm based on the**divide and conquer** paradigm. It is also known as an **external memory algorithm**, which means it can process data in parts, making it suitable for datasets too large to fit into memory at once. 

### How it Works: The Divide and Conquer Strategy

The algorithm follows a simple, recursive three-step process: 

1. **Divide**: The main array is split into two roughly equal halves. 5 This process is repeated recursively on the subarrays until we are left with arrays containing only a single element (which are inherently sorted).
    
2. **Conquer**: The smaller subarrays are sorted. 6 In Merge Sort, this step is trivial because the base case of the recursion is an array of size one.
    
3. **Merge**: The two sorted subarrays are combined back into a single, larger sorted array. 7 This merging step is the core of the algorithm's logic.

#### Visualizing the Process

The "Divide" Phase:

The initial array is recursively split until each element is in its own subarray.

![[merge-sort-divide-steps.png]]

(This represents the recursive splitting shown on pages 8-10 of your PDF)

The "Merge" Phase:

The single-element subarrays are progressively merged back together, sorting them in the process.

![[merge-sort-merge-steps.png]]

(This represents the merging process shown on pages 14-16 of your PDF)

### Time Complexity Analysis

The efficiency of Merge Sort comes from the balance between the dividing and merging steps.

- **Dividing**: The array is divided `log₂n` times.
    
- **Merging**: At each level of the recursion, every element is looked at once to be merged. This means the merging work at each level takes linear time, or

    theta(n). 888888888

Since there are `log₂n` levels and each level takes theta(n) work, the total time complexity is:

> **Total Time Complexity:** theta(nlog_2n) 9

The recurrence relation for Merge Sort is:

T(n)=2T(fracn2)+n 10

### Pseudocode

The algorithm consists of two main functions: a recursive `mergeSort` function and a helper `mergeSortedArrays` function.

**`mergeSort` function:**

C

```
void mergeSort(int A[], int first, int last) {
    if (first < last) {
        int mid = (first + last) / 2;
        mergeSort(A, first, mid);      // Recursively sort the first half
        mergeSort(A, mid + 1, last);   // Recursively sort the second half
        mergeSortedArrays(A, first, mid, last); // Merge the two sorted halves
    }
}
```

_(Based on the pseudocode from page 24)_

mergeSortedArrays function:

This function takes two sorted subarrays (represented by L[] and R[]) and merges them into the main array A[].

![[merge-sort-pseudocode.png]]

(This is a representation of the detailed pseudocode on page 25)

---

## 2. The Lower Bound for Comparison-Based Sorting

Is theta(nlogn) the best we can do for sorting? For a certain _class_ of algorithms, the answer is yes.

> **Lower Bound Theory**: This theory defines the minimum possible time complexity that _any_ algorithm of a specific class requires to solve a problem for an arbitrary input. 11

For the problem of

**sorting** using **comparison-based algorithms** (like Merge Sort, Quick Sort, Heap Sort, etc.), there is a proven lower bound. 12

### The Decision Tree Model

- Any comparison-based sort can be modeled as a
    
    **decision tree**. 13
    
- For an input of

    `n` elements, there are `n!` possible permutations (arrangements). 14

- The sorting algorithm must determine which one of these

    `n!` permutations is the sorted one. 15

- In the decision tree:
    
    - **Internal nodes** represent comparisons between two elements (e.g., `a <= b`). 16
        
    - **Leaf nodes** represent one of the `n!` possible permutations. 17
        
    - The execution of the algorithm for a given input corresponds to traversing a path from the root to a leaf node. 18

![[decision-tree-sorting.png]]

(This is the decision tree for sorting 3 elements from page 36)

### The Proof

1. A sorting algorithm must be able to produce any of the

    `n!` permutations as output, so its decision tree must have at least `n!` leaf nodes. 19

2. **Theorem**: A binary tree with `L` leaf nodes must have a height of at least lceillog_2Lrceil. 20
    
3. Therefore, the height of the decision tree for sorting must be at least log_2(n).
    
4. The height of the tree represents the number of comparisons in the worst case. So, the minimum number of comparisons is Omega(log_2(n)).
    
5. Using mathematical properties, we can show that

    log_2(n)=Omega(nlogn). 21

> **Conclusion**: Any comparison-based sorting algorithm will take at least Omega(nlog_2n) time in the worst case. 22 This makes Merge Sort an asymptotically optimal comparison-based sorting algorithm.

---

## 3. Non-Comparison Based Sorting

To break the

Omega(nlogn) barrier, we must use algorithms that don't rely on comparing elements to each other. 23 These algorithms often work by using the actual values of the elements to determine their sorted positions.

### A. Counting Sort

Counting Sort is a non-comparison algorithm that sorts elements in linear time. 24242424It is effective when the elements are integers within a specific range. 25

#### How it Works

The core idea is to determine, for each element `x`, the number of elements less than `x`. This information can be used to place `x` directly into its correct position in the output array.

The Stable Algorithm:

A simple approach of just counting frequencies and overwriting the array is not "stable" (it doesn't preserve the relative order of equal elements). The stable version is more robust:

1. **Create Count Array**: Create an auxiliary `Count` array to store the frequency of each element in the input array `A`. 26 The size of this array is determined by the range of the numbers (e.g.,

    `max_value - min_value + 1`).

2. **Create Cumulative Sum**: Modify the `Count` array so that each element at index `i` stores the sum of the previous counts. 27 After this step,

    `Count[i]` stores the number of elements less than or equal to `i`. This tells us the correct final position for the _last_ occurrence of element `i`.

3. **Build Output Array**:
    
    - Iterate through the input array `A` **from right to left**.
        
    - For each element `A[i]`, place it into a new output array `B` at the position given by `Count[A[i]] - 1`.
        
    - Decrement the value in

        `Count[A[i]]` by one. 28

4. **Copy Back**: Copy the sorted elements from `B` back to `A`. 29

![[counting-sort-stable-example.gif]]

(This represents the detailed step-by-step process shown on pages 88-101 of your PDF)

#### Time Complexity

- The time complexity is

    theta(n+k), where `n` is the number of elements in the input array and `k` is the range of the elements (the size of the `Count` array). 30

- Counting Sort is highly efficient if the range `k` is not significantly larger than the number of elements `n`.

### B. Radix Sort

Radix Sort is another non-comparison algorithm that cleverly sorts numbers by processing their individual digits. 31

#### How it Works

It sorts the data elements digit by digit, starting from the

**least significant digit** and moving to the **most significant digit**. 32 The key requirement is that the sorting algorithm used for each digit must be

**stable**. Counting Sort is an excellent choice for this.

Example:

To sort [312, 20, 87, 881, 402, 7, 100, 243, 68, 524]:

1. **Sort by the last digit (ones place)**: This would group all numbers ending in 0, then 1, then 2, etc.
    
2. **Sort by the middle digit (tens place)**: Using the output from step 1, sort the numbers by their tens digit. The stability ensures that `402` comes before `312`.
    
3. **Sort by the first digit (hundreds place)**: Using the output from step 2, sort by the hundreds digit to get the final sorted list.

![[radix-sort-steps.png]]

(This represents the digit-by-digit sorting process from pages 108-110)

#### Time Complexity

- The time complexity is

    O(dcdotn), where `n` is the number of elements and `d` is the number of digits in the largest element. 33

- This is because we run a linear-time sort (like Counting Sort)

    `d` times. 34

### C. Bucket Sort

Bucket Sort works by distributing the elements into a number of "buckets." 35

#### How it Works

1. **Create Buckets**: Set up an array of empty buckets. The buckets are ordered and can hold data within a defined range. 36
    
2. **Distribute**: Go through the input array and place each element into its corresponding bucket. 37
    
3. **Sort Buckets**: Sort each non-empty bucket individually using another sorting algorithm (like Insertion Sort). 38
    
4. **Concatenate**: Visit the buckets in order and put all the elements back into the original array. 39

![[bucket-sort-example.png]]

(This is the visual example of Bucket Sort from page 116)

#### Time Complexity

- The performance of Bucket Sort depends heavily on the distribution of the elements. 40
    
- If the elements are
    
    **uniformly distributed**, the average time complexity can be linear, or theta(n). 41
    
- In the worst case (e.g., all elements fall into a single bucket), the performance is dominated by the sorting algorithm used within the buckets.