# 🧩 Blog 7: Union-Find / Disjoint Set Pattern (DSU Mastery Guide)

The **Union-Find (Disjoint Set Union, DSU)** is one of the most **powerful yet underrated** tools in graph theory and competitive programming.
It’s the **Swiss army knife** for problems involving:

* **Connectivity** (are two nodes connected?)
* **Grouping/Clustering** (which component does a node belong to?)
* **Cycle detection in undirected graphs**
* **Minimum Spanning Trees (Kruskal’s Algorithm)**
* **Dynamic connectivity queries**
* **Advanced problems** like accounts merging, friend circles, network connectivity, etc.

If you master DSU, you’ll instantly unlock solutions to a wide class of graph problems.

---

## 🏗️ Core Ideas of DSU

1. **Parent Array**

   * Each node points to a parent.
   * If `parent[x] == x`, then `x` is a **root node** (the representative of its set).

2. **Find(x)**

   * Traverses parent pointers until it reaches the root.
   * Optimized with **Path Compression**: we flatten the tree so future lookups are almost O(1).

3. **Union(x, y)**

   * Merges two sets if they are different.
   * Optimized with **Union by Rank/Size**: attach the smaller tree under the bigger one to keep the height minimal.

---

## ⚙️ Template: DSU in Java

```java
class UnionFind {
    private int[] parent;
    private int[] rank;  // or size

    // Constructor
    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;  // each node is its own parent initially
            rank[i] = 1;
        }
    }

    // Find with Path Compression
    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]); // flatten tree
        }
        return parent[x];
    }

    // Union by Rank
    public boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY) return false; // already connected

        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
        return true;
    }

    // Check if connected
    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```

---

## 🧾 Complexity Analysis

* **Union / Find (with Path Compression + Union by Rank):**
  → **O(α(N)) per operation**, where α(N) is the inverse Ackermann function (practically constant).
* **Space Complexity:** O(N).

---

## 📌 Pattern Applications

Now let’s explore the **real power** of Union-Find with extensive use cases:

---

### 🔹 1. Cycle Detection in Undirected Graph

If an edge connects two nodes already in the same set, it forms a **cycle**.

```java
public boolean hasCycle(int n, int[][] edges) {
    UnionFind uf = new UnionFind(n);
    for (int[] edge : edges) {
        if (!uf.union(edge[0], edge[1])) {
            return true; // cycle detected
        }
    }
    return false;
}
```

**Usage:**

* Network redundancy check.
* Detecting illegal extra edges in a tree.

---

### 🔹 2. Kruskal’s Algorithm (Minimum Spanning Tree)

Union-Find is the **backbone of Kruskal’s MST** algorithm.
Steps:

1. Sort edges by weight.
2. Iterate edges; union if they connect two different components.
3. Stop when we have `n-1` edges.

```java
public int kruskalMST(int n, int[][] edges) {
    Arrays.sort(edges, (a, b) -> a[2] - b[2]); // sort by weight
    UnionFind uf = new UnionFind(n);
    int mstWeight = 0, count = 0;

    for (int[] edge : edges) {
        if (uf.union(edge[0], edge[1])) {
            mstWeight += edge[2];
            count++;
            if (count == n - 1) break;
        }
    }
    return mstWeight;
}
```

---

### 🔹 3. Connected Components in Graph

* Start with `n` components.
* Every successful union reduces count by 1.

```java
public int countComponents(int n, int[][] edges) {
    UnionFind uf = new UnionFind(n);
    int components = n;
    for (int[] edge : edges) {
        if (uf.union(edge[0], edge[1])) {
            components--;
        }
    }
    return components;
}
```

---

### 🔹 4. Redundant Connection (LeetCode 684)

* Given a tree with an extra edge, return the edge that forms a cycle.

```java
public int[] findRedundantConnection(int[][] edges) {
    UnionFind uf = new UnionFind(edges.length + 1);
    for (int[] edge : edges) {
        if (!uf.union(edge[0], edge[1])) {
            return edge; // redundant edge
        }
    }
    return new int[0];
}
```

---

### 🔹 5. Accounts Merge (LeetCode 721)

* Emails belonging to the same person must be grouped.
* Use DSU to **union accounts sharing emails**.

Idea:

* Map each email → account.
* Union accounts with shared emails.
* Group results by representative.

---

### 🔹 6. Number of Islands II (Dynamic Island Counting)

* Initially grid = water.
* Add land dynamically.
* Each new land = new component.
* Union with adjacent land cells → reduce count.

This is a **classic dynamic connectivity DSU problem**.

---

### 🔹 7. Advanced Problems

* **Friend Circles / Social Networks** (LeetCode 547).
* **Percolation Systems** in simulations.
* **Clustering problems** (grouping similar entities).
* **Union-Find with Rollback** (used in offline queries in competitive programming).

---

## ⚡ DSU Variants You Should Know

1. **Union by Rank vs Union by Size**

   * Both keep the tree shallow.
2. **Weighted Union-Find**

   * Used for problems involving relations (e.g., equations, ratios).
   * Example: **Evaluate Division** problem (LeetCode 399).
3. **Union-Find with Rollback**

   * Used in dynamic connectivity problems where queries can be undone.

---

## 📝 Key Takeaways

* Union-Find = **lightweight, near O(1), dynamic connectivity checker**.
* Core optimizations: **Path Compression + Union by Rank/Size**.
* Crucial in:

  * Cycle detection.
  * MST (Kruskal’s).
  * Connectivity queries.
  * Dynamic clustering problems.

If BFS/DFS are **exploration tools**, DSU is the **connectivity backbone** — together they cover almost all graph problems.

---

