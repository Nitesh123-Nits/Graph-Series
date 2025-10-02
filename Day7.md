# 📘 Blog 6: Minimum Spanning Tree (MST) 🌉

## 🔹 What is MST?

A **Spanning Tree** of a graph connects all vertices with the minimum number of edges (N-1 for N nodes), with **no cycles**.
A **Minimum Spanning Tree (MST)** is the spanning tree where the **sum of edge weights is minimized**.

✅ Graph must be:

* **Connected**
* **Undirected**
* **Weighted**

---

## 🔹 Why is MST Important?

* **Network Design:** Build road, cable, or data network with minimum cost.
* **Clustering Algorithms (ML):** Partitioning data.
* **Approximation Algorithms:** TSP, Steiner Tree.

---

## 🔹 MST Algorithms

We mainly use **Kruskal’s** and **Prim’s**.

---

### **1️⃣ Kruskal’s Algorithm (Union-Find / Greedy Approach)**

**Steps:**

1. Sort all edges by weight.
2. Pick the smallest edge that doesn’t form a cycle.
3. Use **Union-Find (Disjoint Set Union - DSU)** to check cycle.
4. Repeat until you connect all vertices (N-1 edges).

**Complexity:**

* Sorting edges: **O(E log E)**
* Union-Find operations: \~**O(E α(N))** (α(N) is inverse Ackermann, nearly constant).

👉 Best when graph is **sparse** (E \~ V).

**Java Code (Kruskal’s MST):**

```java
import java.util.*;

class KruskalMST {
    static class Edge {
        int u, v, weight;
        Edge(int u, int v, int weight) {
            this.u = u;
            this.v = v;
            this.weight = weight;
        }
    }

    static class DSU {
        int[] parent, rank;
        DSU(int n) {
            parent = new int[n];
            rank = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        boolean union(int x, int y) {
            int px = find(x), py = find(y);
            if (px == py) return false;
            if (rank[px] < rank[py]) parent[px] = py;
            else if (rank[px] > rank[py]) parent[py] = px;
            else {
                parent[py] = px;
                rank[px]++;
            }
            return true;
        }
    }

    public static int kruskalMST(int n, List<Edge> edges) {
        Collections.sort(edges, Comparator.comparingInt(e -> e.weight));
        DSU dsu = new DSU(n);
        int mstCost = 0, count = 0;
        for (Edge edge : edges) {
            if (dsu.union(edge.u, edge.v)) {
                mstCost += edge.weight;
                count++;
                if (count == n - 1) break;
            }
        }
        return mstCost;
    }

    public static void main(String[] args) {
        List<Edge> edges = Arrays.asList(
            new Edge(0, 1, 10),
            new Edge(0, 2, 6),
            new Edge(0, 3, 5),
            new Edge(1, 3, 15),
            new Edge(2, 3, 4)
        );
        System.out.println("MST Cost = " + kruskalMST(4, edges));
    }
}
```

✅ Output: `MST Cost = 19`

---

### **2️⃣ Prim’s Algorithm (Priority Queue / Greedy Approach)**

**Steps:**

1. Start from any node.
2. Use a **Min Heap** to always pick the smallest edge connecting MST to a new node.
3. Expand MST until all nodes are covered.

**Complexity:**

* With Min Heap (priority queue): **O(E log V)**.

👉 Best when graph is **dense** (E \~ V²).

**Java Code (Prim’s MST):**

```java
import java.util.*;

class PrimsMST {
    static class Pair {
        int node, weight;
        Pair(int node, int weight) {
            this.node = node;
            this.weight = weight;
        }
    }

    public static int primMST(int n, List<List<Pair>> graph) {
        boolean[] visited = new boolean[n];
        PriorityQueue<Pair> pq = new PriorityQueue<>(Comparator.comparingInt(p -> p.weight));
        pq.offer(new Pair(0, 0)); // Start from node 0
        int mstCost = 0;

        while (!pq.isEmpty()) {
            Pair cur = pq.poll();
            if (visited[cur.node]) continue;
            visited[cur.node] = true;
            mstCost += cur.weight;

            for (Pair nei : graph.get(cur.node)) {
                if (!visited[nei.node]) pq.offer(nei);
            }
        }
        return mstCost;
    }

    public static void main(String[] args) {
        int n = 4;
        List<List<Pair>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());

        graph.get(0).add(new Pair(1, 10));
        graph.get(0).add(new Pair(2, 6));
        graph.get(0).add(new Pair(3, 5));
        graph.get(1).add(new Pair(3, 15));
        graph.get(2).add(new Pair(3, 4));

        System.out.println("MST Cost = " + primMST(n, graph));
    }
}
```

✅ Output: `MST Cost = 19`

---

## 🔹 Kruskal vs Prim – When to Use?

| Algorithm     | Best for                  | Time Complexity | Data Structures           |
| ------------- | ------------------------- | --------------- | ------------------------- |
| **Kruskal’s** | Sparse Graphs (few edges) | O(E log E)      | Sorting + Union-Find      |
| **Prim’s**    | Dense Graphs (many edges) | O(E log V)      | Min Heap + Adjacency List |

---

## 🔹 Common MST Problems

* ✅ [LeetCode 1135: Connecting Cities with Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)
* ✅ [LeetCode 1584: Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)
* ✅ [HackerRank: Kruskal (MST)](https://www.hackerrank.com/challenges/kruskalmstrsub/problem)

---

## 🔹 Interview Takeaways

* MST = Greedy choice (pick min edge).
* Kruskal → edges sorted + DSU.
* Prim → priority queue on vertices.
* Both produce **same MST cost**.

---
