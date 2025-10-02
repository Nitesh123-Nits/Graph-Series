# 🔗 Blog 8: Strongly Connected Components (SCCs) & Bridges / Articulation Points

Graphs aren’t just about paths and trees — sometimes you need to understand the **structure of connectivity itself**. That’s where **SCCs and critical edges/nodes** come into play.

These concepts help in problems like:

* Finding strongly connected “subsystems” in a directed graph.
* Identifying critical links in a network (where removing one breaks connectivity).
* Optimizing network design and fault-tolerance.

---

## 🌐 Part 1: Strongly Connected Components (SCCs)

### 🔎 What are SCCs?

* In a **directed graph**, an SCC is a set of nodes where every node is reachable from every other node.
* Example: In a social network, an SCC could represent a **tight group of friends** where everyone can reach everyone else.

### 📌 Why Important?

* Break down directed graphs into **condensed DAGs**.
* Basis for advanced problems like **2-SAT, deadlock detection, compiler optimization**.

---

### ⚙️ Algorithms for SCCs

#### 1. **Kosaraju’s Algorithm** (Two DFS Passes)

Steps:

1. Do a DFS and store nodes in **finish-time stack**.
2. Reverse the graph.
3. Pop nodes from stack, run DFS in reversed graph → each DFS = one SCC.

```java
class KosarajuSCC {
    private int V;
    private List<Integer>[] graph, revGraph;
    private boolean[] visited;
    private Stack<Integer> stack;

    KosarajuSCC(int V) {
        this.V = V;
        graph = new ArrayList[V];
        revGraph = new ArrayList[V];
        for (int i = 0; i < V; i++) {
            graph[i] = new ArrayList<>();
            revGraph[i] = new ArrayList<>();
        }
        visited = new boolean[V];
        stack = new Stack<>();
    }

    void addEdge(int u, int v) {
        graph[u].add(v);
        revGraph[v].add(u); // reverse edge
    }

    void dfs1(int u) {
        visited[u] = true;
        for (int v : graph[u]) {
            if (!visited[v]) dfs1(v);
        }
        stack.push(u);
    }

    void dfs2(int u, List<Integer> comp) {
        visited[u] = true;
        comp.add(u);
        for (int v : revGraph[u]) {
            if (!visited[v]) dfs2(v, comp);
        }
    }

    List<List<Integer>> getSCCs() {
        // Step 1: Order by finish time
        for (int i = 0; i < V; i++) {
            if (!visited[i]) dfs1(i);
        }

        // Step 2: Process in reverse graph
        Arrays.fill(visited, false);
        List<List<Integer>> sccs = new ArrayList<>();

        while (!stack.isEmpty()) {
            int u = stack.pop();
            if (!visited[u]) {
                List<Integer> comp = new ArrayList<>();
                dfs2(u, comp);
                sccs.add(comp);
            }
        }
        return sccs;
    }
}
```

#### 2. **Tarjan’s Algorithm** (Single DFS, O(V+E))

* Uses **low-link values** to identify SCCs directly in one DFS.
* More efficient in practice than Kosaraju.

```java
class TarjanSCC {
    private List<Integer>[] graph;
    private int[] ids, low;
    private boolean[] onStack;
    private Stack<Integer> stack;
    private int id, sccCount;

    TarjanSCC(int n) {
        graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        ids = new int[n];
        low = new int[n];
        Arrays.fill(ids, -1);
        onStack = new boolean[n];
        stack = new Stack<>();
        id = 0;
        sccCount = 0;
    }

    void addEdge(int u, int v) { graph[u].add(v); }

    void dfs(int at) {
        stack.push(at);
        onStack[at] = true;
        ids[at] = low[at] = id++;

        for (int to : graph[at]) {
            if (ids[to] == -1) dfs(to);
            if (onStack[to]) low[at] = Math.min(low[at], low[to]);
        }

        // Start of SCC
        if (ids[at] == low[at]) {
            while (true) {
                int node = stack.pop();
                onStack[node] = false;
                low[node] = ids[at];
                if (node == at) break;
            }
            sccCount++;
        }
    }

    int findSCCs() {
        for (int i = 0; i < graph.length; i++) {
            if (ids[i] == -1) dfs(i);
        }
        return sccCount;
    }
}
```

---

## 🌉 Part 2: Bridges & Articulation Points

### 🔎 What are they?

* **Bridge (critical edge):** Removing it increases number of connected components.
* **Articulation Point (cut vertex):** Removing it disconnects the graph.

These help analyze **network vulnerability**.

---

### ⚙️ Tarjan’s Algorithm for Bridges

```java
class Bridges {
    private List<Integer>[] graph;
    private int[] ids, low;
    private int id;
    private List<int[]> bridges;

    Bridges(int n) {
        graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        ids = new int[n];
        low = new int[n];
        Arrays.fill(ids, -1);
        id = 0;
        bridges = new ArrayList<>();
    }

    void addEdge(int u, int v) {
        graph[u].add(v);
        graph[v].add(u);
    }

    void dfs(int at, int parent) {
        ids[at] = low[at] = id++;
        for (int to : graph[at]) {
            if (to == parent) continue;
            if (ids[to] == -1) {
                dfs(to, at);
                low[at] = Math.min(low[at], low[to]);
                if (ids[at] < low[to]) bridges.add(new int[]{at, to});
            } else {
                low[at] = Math.min(low[at], ids[to]);
            }
        }
    }

    List<int[]> findBridges() {
        for (int i = 0; i < graph.length; i++) {
            if (ids[i] == -1) dfs(i, -1);
        }
        return bridges;
    }
}
```

---

### ⚙️ Tarjan’s Algorithm for Articulation Points

```java
class ArticulationPoints {
    private List<Integer>[] graph;
    private int[] ids, low;
    private boolean[] visited, isArticulation;
    private int id, rootChildren, root;

    ArticulationPoints(int n) {
        graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        ids = new int[n];
        low = new int[n];
        visited = new boolean[n];
        isArticulation = new boolean[n];
        id = 0;
    }

    void addEdge(int u, int v) {
        graph[u].add(v);
        graph[v].add(u);
    }

    void dfs(int at, int parent) {
        if (parent == root) rootChildren++;
        visited[at] = true;
        ids[at] = low[at] = id++;

        for (int to : graph[at]) {
            if (to == parent) continue;
            if (!visited[to]) {
                dfs(to, at);
                low[at] = Math.min(low[at], low[to]);
                if (ids[at] <= low[to]) isArticulation[at] = true;
            } else {
                low[at] = Math.min(low[at], ids[to]);
            }
        }
    }

    List<Integer> findAPs() {
        for (int i = 0; i < graph.length; i++) {
            if (!visited[i]) {
                root = i;
                rootChildren = 0;
                dfs(i, -1);
                isArticulation[i] = (rootChildren > 1);
            }
        }
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < graph.length; i++) if (isArticulation[i]) res.add(i);
        return res;
    }
}
```

---

## 🚀 Time Complexity

* SCC (Tarjan/Kosaraju): **O(V + E)**
* Bridges & Articulation Points: **O(V + E)**

---

## 📝 Key Takeaways

* **SCCs:** break directed graphs into strongly connected chunks.
* **Bridges & APs:** identify critical edges/nodes for connectivity.
* **Tarjan’s algorithm (DFS + low-link values)** unifies SCCs, bridges, and articulation points into a single powerful framework.


