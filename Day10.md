# ⚡ Blog 9: Advanced Graph Patterns (Flow, Matching, Bipartite)

This blog covers three **powerful categories** of problems:

1. **Flow Networks (Max Flow / Min Cut)**
2. **Graph Matching (Maximum Matching, Bipartite Matching)**
3. **Bipartite Graph Applications (Coloring, Assignment, Scheduling)**

---

## 1️⃣ Flow Networks (Max Flow / Min Cut)

A **flow network** is a directed graph where each edge has a **capacity**, and we want to push as much “flow” from a **source (s)** to a **sink (t)** as possible.

* **Max Flow** = Maximum possible flow from `s` → `t`.
* **Min Cut** = Smallest set of edges that, if removed, disconnect `s` from `t`.
* **Ford-Fulkerson / Edmonds-Karp Algorithm**: Repeatedly send augmenting paths until no more flow can be added.

### Java Implementation (Edmonds-Karp using BFS)

```java
import java.util.*;

class MaxFlow {
    private int n;
    private int[][] capacity;
    private List<Integer>[] adj;

    public MaxFlow(int n) {
        this.n = n;
        capacity = new int[n][n];
        adj = new List[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();
    }

    public void addEdge(int u, int v, int cap) {
        capacity[u][v] += cap; // Handle multiple edges
        adj[u].add(v);
        adj[v].add(u); // Residual graph
    }

    private int bfs(int s, int t, int[] parent) {
        Arrays.fill(parent, -1);
        parent[s] = s;
        Queue<int[]> q = new LinkedList<>();
        q.add(new int[]{s, Integer.MAX_VALUE});

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            int node = cur[0], flow = cur[1];

            for (int next : adj[node]) {
                if (parent[next] == -1 && capacity[node][next] > 0) {
                    parent[next] = node;
                    int newFlow = Math.min(flow, capacity[node][next]);
                    if (next == t) return newFlow;
                    q.add(new int[]{next, newFlow});
                }
            }
        }
        return 0;
    }

    public int maxFlow(int s, int t) {
        int flow = 0;
        int[] parent = new int[n];
        int newFlow;

        while ((newFlow = bfs(s, t, parent)) > 0) {
            flow += newFlow;
            int cur = t;
            while (cur != s) {
                int prev = parent[cur];
                capacity[prev][cur] -= newFlow;
                capacity[cur][prev] += newFlow;
                cur = prev;
            }
        }
        return flow;
    }
}
```

📌 Example Problems:

* **Maximum Bipartite Matching (via Flow)**
* **Network Bandwidth Optimization**
* **Airline Traffic Scheduling**
* **Project Assignment**

---

## 2️⃣ Graph Matching

Graph Matching = selecting edges such that **no two edges share a vertex**.

* **Maximum Matching** = maximum number of matched pairs.
* **Bipartite Matching** = matching only across two sets (e.g., jobs ↔ workers).
* Can be solved via:

  * **DFS Augmenting Path (Hungarian Algorithm)**
  * **Max Flow (Reduction of Matching → Flow)**

### Example: Bipartite Matching with DFS

```java
class BipartiteMatching {
    private int n, m;
    private List<Integer>[] graph;
    private int[] matchR;
    private boolean[] seen;

    public BipartiteMatching(int n, int m) {
        this.n = n; // left partition
        this.m = m; // right partition
        graph = new List[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
    }

    public void addEdge(int u, int v) {
        graph[u].add(v); // Edge from left u → right v
    }

    private boolean bpm(int u) {
        for (int v : graph[u]) {
            if (seen[v]) continue;
            seen[v] = true;

            if (matchR[v] == -1 || bpm(matchR[v])) {
                matchR[v] = u;
                return true;
            }
        }
        return false;
    }

    public int maxMatching() {
        matchR = new int[m];
        Arrays.fill(matchR, -1);

        int result = 0;
        for (int u = 0; u < n; u++) {
            seen = new boolean[m];
            if (bpm(u)) result++;
        }
        return result;
    }
}
```

📌 Example Problems:

* **Job Assignment** (workers → jobs).
* **Stable Marriage Problem**.
* **Course Scheduling** (students → projects).

---

## 3️⃣ Bipartite Graphs

A graph is **bipartite** if nodes can be divided into **two disjoint sets** such that every edge connects a node in set A to set B.

### Checking if a Graph is Bipartite (using BFS Coloring)

```java
public boolean isBipartite(int[][] graph) {
    int n = graph.length;
    int[] color = new int[n];
    Arrays.fill(color, -1);

    for (int i = 0; i < n; i++) {
        if (color[i] == -1) {
            Queue<Integer> q = new LinkedList<>();
            q.add(i);
            color[i] = 0;

            while (!q.isEmpty()) {
                int u = q.poll();
                for (int v : graph[u]) {
                    if (color[v] == -1) {
                        color[v] = 1 - color[u];
                        q.add(v);
                    } else if (color[v] == color[u]) {
                        return false;
                    }
                }
            }
        }
    }
    return true;
}
```

📌 Example Problems:

* **Graph Coloring**
* **Team Formation**
* **Odd-Cycle Detection**

---

## 🧠 Key Takeaways

* **Flow Networks** → Max flow, min cut, traffic optimization.
* **Matching** → Job assignment, stable marriage, bipartite matching.
* **Bipartite Graphs** → Graph coloring, odd cycle detection, DFS/BFS-based verification.

These patterns **power advanced graph algorithms** that solve real-world optimization and scheduling problems.

---

