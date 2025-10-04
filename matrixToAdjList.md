
# 🧠 Convert Adjacency Matrix to Adjacency List in Java — Complete Guide

Graphs are one of the most powerful data structures used in software engineering — from representing networks and maps to modeling dependencies and workflows.
But how you **represent** a graph in memory can significantly affect performance.

In this post, we’ll explore how to **convert an adjacency matrix** into an **adjacency list**, and why you’d want to.

---

## 🚀 1. Understanding Graph Representations

A graph can be represented in two common ways:

### 🧩 1.1 Adjacency Matrix

An adjacency matrix is a 2D array where:

* `matrix[i][j] = 1` → there’s an edge between node `i` and `j`.
* `matrix[i][j] = 0` → no edge between them.

#### Example:

```
   0  1  2
0 [1, 1, 0]
1 [1, 1, 0]
2 [0, 0, 1]
```

Here:

* Node 0 is connected to 1
* Node 1 is connected to 0
* Node 2 is isolated

---

### 🧱 1.2 Adjacency List

An adjacency list represents the same graph as a **list of lists** —
each vertex has a list of its neighbors.

#### Example (converted):

```
0 → [1]
1 → [0]
2 → []
```

This is much **space-efficient** for sparse graphs and easier to traverse.

---

## 🧮 2. Why Convert?

| Representation   | Space    | Ideal For     |
| ---------------- | -------- | ------------- |
| Adjacency Matrix | O(V²)    | Dense Graphs  |
| Adjacency List   | O(V + E) | Sparse Graphs |

When working with large, sparse graphs (like in social networks, or maps),
**adjacency lists save space** and make traversal algorithms (DFS, BFS, Dijkstra) faster.

---

## ⚙️ 3. Java Implementation

Here’s how you can easily convert a matrix into an adjacency list:

```java
import java.util.*;

public class MatrixToList {
    public static void main(String[] args) {
        int[][] isConnected = {
            {1, 1, 0},
            {1, 1, 0},
            {0, 0, 1}
        };

        List<List<Integer>> adj = convertToAdjList(isConnected);

        System.out.println("Adjacency List:");
        for (int i = 0; i < adj.size(); i++) {
            System.out.println(i + " → " + adj.get(i));
        }
    }

    static List<List<Integer>> convertToAdjList(int[][] matrix) {
        int V = matrix.length;
        List<List<Integer>> adj = new ArrayList<>();

        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }

        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                if (matrix[i][j] == 1 && i != j) {
                    adj.get(i).add(j);
                }
            }
        }

        return adj;
    }
}
```

---

## 🧩 4. Output

For the input matrix:

```
[ [1,1,0],
  [1,1,0],
  [0,0,1] ]
```

The output will be:

```
Adjacency List:
0 → [1]
1 → [0]
2 → []
```

---

## 🔍 5. Time and Space Complexity

| Operation                   | Complexity   |
| --------------------------- | ------------ |
| Traversing all matrix cells | **O(V²)**    |
| Space for adjacency list    | **O(V + E)** |

Even though the conversion takes `O(V²)`,
subsequent graph operations (DFS, BFS, etc.) become **faster and memory-efficient**.

---

## 💡 6. Extensions

If the matrix contains **weights** instead of 0/1 (e.g., `matrix[i][j] = w` for edge weight),
you can store pairs instead:

```java
List<List<int[]>> adj = new ArrayList<>();
if (matrix[i][j] > 0 && i != j)
    adj.get(i).add(new int[]{j, matrix[i][j]});
```

This converts it into a **weighted adjacency list**, useful for algorithms like Dijkstra or Prim’s.

---

## 🧠 7. Key Takeaways

* ✅ **Adjacency Matrix** is simple but space-heavy (`O(V²)`).
* ✅ **Adjacency List** is more scalable (`O(V + E)`).
* ✅ Converting between them is straightforward with a double loop.
* ✅ Ideal for real-world applications like route finding, graph traversals, and network simulations.

---

### 🔗 Bonus Tip

If you’re solving problems like:

* **Number of Provinces**
* **Connected Components**
* **Network Connectivity**

Converting your input matrix to an adjacency list first makes your DFS/BFS implementations cleaner and faster.

---

### ✍️  Note

Converting a matrix to a list might look trivial,
but this simple optimization can **dramatically improve performance**
when scaling graph-based systems.



Would you like me to extend this blog with a **section comparing BFS and DFS traversals** after conversion (to show real performance gain)?
