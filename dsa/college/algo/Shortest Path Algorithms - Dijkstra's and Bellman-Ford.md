Today, we will delve into the essential topic of finding optimal paths in graphs. This lecture will cover two fundamental algorithms:

- **Dijkstra's Algorithm**
    
- **The Bellman-Ford Algorithm**

We will explore their mechanics, use cases, limitations, and time complexity. 

---

## 1. Why Do We Need to Find the Shortest Path?

Finding the "shortest path" is not just an abstract academic problem; it's the backbone of many technologies we use daily. 

- **Digital Mapping Services:** When you ask Google Maps for directions, it's calculating the shortest (or fastest) path from your location to your destination. 
    
- **Social Networks:** They can find the "shortest path" of connections between you and another person. 
    
- **IP Routing (Internet):** The internet is a giant graph of connected devices. Protocols like OSPF (Open Shortest Path First) determine the most efficient route to send data packets across this network. 
    
- **Telephone Networks:** Routing calls efficiently through the network. 
    
- **Robotics:** A robot navigating a room needs to find the shortest path to its goal while avoiding obstacles. 
    
- **Other Applications:** Include planning flight agendas and designating optimal file server locations in a network. 

---

## 2. Defining the Problem: From Graphs to Weighted Graphs

Let's start with a simple map represented as a graph. Each location is a **node** (or vertex), and the connections between them are **edges**.

![[unweighted-graph-example.png]]

_(This is the graph from page 5 of your PDF)_

Now, what if some paths are longer, slower, or more "costly" than others? We represent this cost using **weights**. This turns our simple graph into a **weighted graph**.

> **Weighted Graph:** A graph where each edge is assigned a numerical weight. This weight can represent distance, time, cost, or any other metric.

![[weighted-graph-example-with-costs.png]]

_(This is the graph from page 6 of your PDF, now with weights)_

In a weighted graph, the

**cost of a path** is simply the sum of the weights of all the edges along that path.

> The **shortest path** between two nodes is the path with the minimum total cost. 

For now, we will assume all edge weights are **non-negative**. 

![[shortest-path-comparison.png]]

_(This image from page 8 illustrates two different paths from 's' to 't'. The top path has a cost of 25, while the bottom path has a cost of 5, making it the shortest path.)_ 

### The Single-Source Shortest Path Problem

A common version of this problem is the **single-source shortest-path problem**. This is where we want to find the shortest path from a single starting node (the "source") to _all other nodes_ in the graph. 

For example, starting from "Gates," the shortest paths to all other destinations are: 

|Destination|Cost|Path|
|---|---|---|
|Packard|1|Packard|
|CS161|2|Packard-CS161|
|Hospital|10|Hospital|
|Caltrain|17|Caltrain|
|Union|6|Packard-CS161-Union|
|Stadium|10|Stadium|
|Dish|23|Packard-Dish|

---

## 3. Dijkstra's Algorithm

Conceived by Edsger W. Dijkstra in 1956, this is a classic and highly efficient algorithm for finding the shortest paths in a weighted graph, as long as there are no negative edge weights. 

### The Core Intuition

Dijkstra's algorithm works by being "greedy." It builds a tree of shortest paths by iteratively selecting the "closest" unknown node and finalizing its distance.

Imagine all the nodes are in a tangled mess. The algorithm works like this: 

1. **YOINK!** Pick up the starting node ("Gates"). 
    
2. Look at all its immediate neighbors and pull the closest one up ("Packard").
    
3. From this new set of "known" nodes (Gates and Packard), find the next closest unknown node and pull it up ("CS161").
    
4. Continue this process, always pulling the closest unvisited node into your "known" set. 

This process eventually creates a shortest path tree from the source node. The lengths along this tree represent the shortest paths to every other node.

![[dijkstra-intuition-tree.png]]

_(This image from page 22 shows the final shortest path tree created by the algorithm)_ 

### The Algorithm: Step-by-Step

Let's formalize this. The algorithm maintains two sets of vertices:

- **Sure:** Vertices for which we have found the absolute shortest path from the source.
    
- **Not-Sure:** All other vertices.

It also keeps track of

`d[v]`, which is our best-known _estimate_ of the shortest path distance from the source to vertex `v`. 34

**The Process:**

1. **Initialization:** 35
    
    - Set the distance to the starting node to 0 (

        `d[source] = 0`). 36

    - Set the distances to all other nodes to infinity (

        `d[v] = ∞`). 37

    - All nodes start in the "Not-Sure" set. 38
        
2. **Iteration:** While there are still "Not-Sure" nodes: 39
    
    - **Select:** Pick the "Not-Sure" node, `u`, that has the smallest distance estimate `d[u]`. 404040404040404040 This is the greedy choice.
        
    - Update Neighbors: For each neighbor v of u, check if we've found a shorter path to it through u. We update its distance with the formula:

        `d[v] = min(d[v], d[u] + edgeWeight(u,v))` 41414141

    - **Finalize:** Move `u` to the "Sure" set. We are now certain that

        `d[u]` is the shortest possible distance to `u`. 42424242

3. **Termination:** Once all nodes are in the "Sure" set, the algorithm is done. The final

    `d[v]` values are the shortest path distances. 43434343

![[dijkstra-example-steps.gif]]

(This represents the series of graphs from pages 25-39 showing the step-by-step execution of the algorithm. You can create a GIF or add the static images sequentially.)

### Pseudocode

Plaintext

```
Dijkstra(Graph G, Source s):
  // Initialization
  For each vertex v in G:
    d[v] = ∞
  d[s] = 0
  
  // Set of sure vertices is initially empty
  // Set of not-sure vertices is all vertices

  While there are not-sure nodes:
    // Select the closest not-sure node
    u = a not-sure node with the smallest d[u] [cite: 982]
	
    // Mark it as sure (move from not-sure to sure)
    Mark u as sure. [cite: 984]
    
    // Update its neighbors
    For each neighbor v of u: [cite: 982]
      d[v] = min(d[v], d[u] + edgeWeight(u,v)) [cite: 983]
      
  Return d
```

### Running Time and Data Structures

The efficiency of Dijkstra's algorithm depends heavily on how we store the "Not-Sure" nodes and find the one with the minimum distance. 44

The total running time is given by:

ntimes(textT(findMin)+textT(removeMin))+mtimestextT(updateKey) 45

where n is the number of vertices and m is the number of edges.

1. **Using an Array:**
    
    - `findMin`: $O(n)$ (must scan the whole array) 46
        
    - `updateKey`: $O(1)$ 47
        
    - **Total Time:** $O(n^2)$ 48
        
2. **Using a Red-Black Tree (or other balanced binary search tree):**
    
    - `findMin`, `removeMin`, `updateKey`: All take $O(logn)$ time. 49494949
        
    - **Total Time:** $O((n+m)logn)$ 50 
        
    - This is better than the array for
        
        **sparse graphs**, where `m` is much smaller than n2. 51
        
3. **Using a Fibonacci Heap (Advanced):**
    
    - `findMin`: $O(1)$ 52 (amortized)
        
    - `removeMin`: $O(logn)$ 53 (amortized)
        
    - `updateKey`: $O(1)$ 54 (amortized)
        
    - **Total Time:** $O(nlogn+m)$ 55
        
    - This is the fastest known implementation for Dijkstra's algorithm.

![[heap-data-structure.png]]

_(This is the heap example from page 47)_ 56

---

## 4. The Bellman-Ford Algorithm

Dijkstra's algorithm is great, but it has a major drawback: it

**cannot handle negative edge weights**. 57 This is where the Bellman-Ford algorithm comes in.

### Why a Different Algorithm?

- **Pro:** Bellman-Ford can handle negative edge weights. 58 This is useful in scenarios where a path can give you a "rebate" or "benefit" instead of just having a cost.
    
- **Pro:** It is more "decentralized." It works by having each node simply update its distance based on information from its immediate neighbors, making it suitable for distributed systems. 59
    
- **Con:** It is slower than Dijkstra's algorithm. 60

### The Bellman-Ford Algorithm: How it Works

Instead of greedily picking the "closest" node, Bellman-Ford takes a more methodical approach. It simply **relaxes every single edge in the graph** over and over again.

**The Process:**

1. **Initialization:** Same as Dijkstra.

    `d[s] = 0` and `d[v] = ∞` for all other vertices. 61616161

2. **Iteration:** This is the key part. The algorithm loops `n-1` times (where `n` is the number of vertices). In each loop: 62
    
    - It iterates through every edge (u, v) in the graph and performs the update step:

        `d[v] = min(d[v], d[u] + edgeWeight(u,v))` 63

The intuition is that after `k` rounds of updates, the algorithm will have found all shortest paths that are at most `k` edges long. Since any simple path (without cycles) can have at most `n-1` edges, running this loop `n-1` times guarantees finding all shortest paths.

### Pseudocode

Plaintext

```
Bellman-Ford(Graph G, Source s):
  // Initialization
  For each vertex v in G:
    d[v] = ∞
  d[s] = 0 [cite: 1180]
  
  // Relax edges repeatedly
  For i from 1 to n-1: 
    For each edge (u, v) in G:
      d[v] = min(d[v], d[u] + edgeWeight(u,v)) 
      
  // (Optional) Check for negative-weight cycles
  
  Return d
```

### Running Time

The running time is straightforward to analyze:

- The outer loop runs

    `n` times. 64

- The inner loop iterates through all

    `m` edges. 65

- **Total Time:** O(ntimesm) 66666666

![[bellman-ford-example-table.png]]

(This represents the table being filled out from pages 58-62, showing the distance estimates after each full iteration.)

### The Problem of Negative Cycles

What happens if there's a cycle of edges whose weights sum to a negative number?

![[negative-cycle-example.png]]

_(This is the graph from page 67 showing a negative cycle)_ 6767

If a path can reach a negative cycle, it can loop around it infinitely, decreasing its cost each time. 6868This means the "shortest path" is not well-defined; its cost is negative infinity. 69696969

**Bellman-Ford can detect this!**

- After the main

    `n-1` iterations are complete, if you run one more iteration and any `d[v]` value _still_ decreases, it means there is a negative cycle reachable from the source. 70

---

## 5. Recap: BFS vs. Dijkstra vs. Bellman-Ford

| Algorithm        | Graph Type    | Time Complexity | Key Feature/Limitation                                             |
| ---------------- | ------------- | --------------- | ------------------------------------------------------------------ |
| **BFS**          | Unweighted 71 | O(n+m) 72       | Fastest, but only for unweighted graphs.                           |
| **Dijkstra**     | Weighted 73   | O(nlogn+m) 74   | Fast for weighted graphs, but fails with negative weights. 75      |
| **Bellman-Ford** | Weighted 76   | O(nm) 77        | Slower, but can handle negative weights and can be distributed. 78 |
