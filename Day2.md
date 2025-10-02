# 📖 Blog 1: Graph Fundamentals & Representations 🕸️

Graphs are everywhere — from **social networks (friends as nodes, relationships as edges)** to **maps (cities as nodes, roads as edges)** to **compilers (dependencies as graphs)**. Mastering them means unlocking the ability to solve problems in connectivity, shortest paths, optimization, and more.

This blog will **lay the groundwork**: what graphs are, types, and how to represent them in code (Java).

---

## 🔹 1. What is a Graph?

A **graph** is a collection of:

* **Vertices (Nodes)** – entities (cities, users, servers, etc.)
* **Edges** – connections/relationships between nodes.

Formally: **G = (V, E)**

---

## 🔹 2. Types of Graphs

### ➡️ **By Direction**

* **Undirected Graph**: edges have no direction.

  * Example: Friendship (A ↔ B).
* **Directed Graph (Digraph)**: edges have direction.

  * Example: Twitter follow (A → B).

### ➡️ **By Weight**

* **Unweighted Graph**: all edges are equal.
* **Weighted Graph**: edges carry weights (cost, time, distance).

### ➡️ **Special Types**

* **Cyclic vs Acyclic** (DAGs used in scheduling).
* **Connected vs Disconnected**.
* **Dense vs Sparse** (few edges vs many).

---

## 🔹 3. Representing Graphs

Efficient representation is crucial — it affects time & space complexity.

### ✅ **1. Edge List**

A list of all edges.

```java
// Graph: 4 nodes, 3 edges
// 1 -- 2, 2 -- 3, 3 -- 4
int[][] edges = {
    {1, 2},
    {2, 3},
    {3, 4}
};
```

* **Pros**: Simple.
* **Cons**: Slow for queries (neighbors lookup = O(E)).

---

### ✅ **2. Adjacency Matrix**

2D matrix where `matrix[u][v] = 1` (or weight).

```java
int V = 4;
int[][] adjMatrix = new int[V + 1][V + 1]; // 1-indexed
adjMatrix[1][2] = 1;
adjMatrix[2][1] = 1;
adjMatrix[2][3] = 1;
adjMatrix[3][2] = 1;
adjMatrix[3][4] = 1;
adjMatrix[4][3] = 1;
```

* **Pros**:

  * O(1) edge existence check.
  * Great for dense graphs.
* **Cons**: O(V²) space, even if sparse.

---

### ✅ **3. Adjacency List (Most Used)**

Each node stores its neighbors in a list.

```java
import java.util.*;

public class GraphAdjList {
    private Map<Integer, List<Integer>> adjList;

    public GraphAdjList(int vertices) {
        adjList = new HashMap<>();
        for (int i = 1; i <= vertices; i++) {
            adjList.put(i, new ArrayList<>());
        }
    }

    public void addEdge(int u, int v, boolean directed) {
        adjList.get(u).add(v);
        if (!directed) {
            adjList.get(v).add(u); // for undirected
        }
    }

    public void printGraph() {
        for (int node : adjList.keySet()) {
            System.out.println(node + " -> " + adjList.get(node));
        }
    }

    public static void main(String[] args) {
        GraphAdjList graph = new GraphAdjList(4);
        graph.addEdge(1, 2, false);
        graph.addEdge(2, 3, false);
        graph.addEdge(3, 4, false);

        graph.printGraph();
    }
}
```

Output:

```
1 -> [2]
2 -> [1, 3]
3 -> [2, 4]
4 -> [3]
```

* **Pros**: Space efficient (O(V+E)).
* **Cons**: Checking edge existence O(deg(V)).

---

### ✅ **4. Weighted Graph Representation**

Using **Pair (neighbor, weight)** in adjacency list.

```java
import java.util.*;

class WeightedGraph {
    private Map<Integer, List<int[]>> adjList;

    public WeightedGraph(int vertices) {
        adjList = new HashMap<>();
        for (int i = 1; i <= vertices; i++) {
            adjList.put(i, new ArrayList<>());
        }
    }

    public void addEdge(int u, int v, int weight, boolean directed) {
        adjList.get(u).add(new int[]{v, weight});
        if (!directed) {
            adjList.get(v).add(new int[]{u, weight});
        }
    }

    public void printGraph() {
        for (int node : adjList.keySet()) {
            System.out.print(node + " -> ");
            for (int[] edge : adjList.get(node)) {
                System.out.print("(" + edge[0] + ", w=" + edge[1] + ") ");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        WeightedGraph graph = new WeightedGraph(4);
        graph.addEdge(1, 2, 5, false);
        graph.addEdge(2, 3, 2, false);
        graph.addEdge(3, 4, 7, false);

        graph.printGraph();
    }
}
```

Output:

```
1 -> (2, w=5) 
2 -> (1, w=5) (3, w=2) 
3 -> (2, w=2) (4, w=7) 
4 -> (3, w=7)
```

---

## 🔹 4. Complexity Summary

| Representation   | Space  | Edge Check | Neighbor Traversal |
| ---------------- | ------ | ---------- | ------------------ |
| Edge List        | O(E)   | O(E)       | O(E)               |
| Adjacency Matrix | O(V²)  | O(1)       | O(V)               |
| Adjacency List   | O(V+E) | O(deg(V))  | O(deg(V))          |

👉 Rule of thumb:

* **Dense graphs** → Adjacency Matrix.
* **Sparse graphs** → Adjacency List (preferred in interviews).

---

## 🔹 5. Real-World Applications

* **Adjacency List** → social networks (efficient for sparse connections).
* **Adjacency Matrix** → AI state transitions (quick edge lookup).
* **Edge List** → Kruskal’s MST algorithm.

---

