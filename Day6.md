# 🚦 Shortest Path Algorithms in Graphs

Finding the **shortest path** between nodes is one of the most important graph problems. It powers **navigation systems, network routing, dependency analysis, AI pathfinding (A\*)**, and more.

This blog will cover **core shortest path algorithms**, categorized by graph type.

---

## 🧩 **Types of Shortest Path Problems**

1. **Unweighted Graphs** → BFS gives shortest path.
2. **Weighted Graphs (no negative weights)** → Dijkstra’s algorithm.
3. **Graphs with negative weights (but no negative cycles)** → Bellman-Ford.
4. **All-Pairs Shortest Path** → Floyd-Warshall (or repeated Dijkstra).
5. **Heuristic Shortest Path** → A\* Search.

---

## 1️⃣ **BFS – Shortest Path in Unweighted Graphs**

📌 Works because every edge has equal weight (1).

* Time Complexity: **O(V + E)**
* Space: **O(V)**

**Java Snippet:**

```java
import java.util.*;

public class BFSShortestPath {
    public static int shortestPath(int n, List<List<Integer>> graph, int src, int dest) {
        boolean[] visited = new boolean[n];
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        
        Queue<Integer> q = new LinkedList<>();
        q.add(src);
        dist[src] = 0;
        visited[src] = true;

        while (!q.isEmpty()) {
            int node = q.poll();
            if (node == dest) return dist[node];
            for (int nei : graph.get(node)) {
                if (!visited[nei]) {
                    visited[nei] = true;
                    dist[nei] = dist[node] + 1;
                    q.add(nei);
                }
            }
        }
        return -1; // no path
    }

    public static void main(String[] args) {
        int n = 6;
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());

        graph.get(0).add(1);
        graph.get(0).add(2);
        graph.get(1).add(3);
        graph.get(2).add(3);
        graph.get(3).add(4);
        graph.get(4).add(5);

        System.out.println("Shortest Path: " + shortestPath(n, graph, 0, 5));
    }
}
```

✅ Output: `Shortest Path: 4`

---

## 2️⃣ **Dijkstra’s Algorithm – Weighted Graphs (No Negatives)**

📌 Uses a **priority queue (min-heap)** to always expand the shortest tentative distance first.

* Time Complexity: **O((V + E) log V)** with PQ
* Handles large weighted graphs efficiently.

**Java Snippet:**

```java
import java.util.*;

public class Dijkstra {
    static class Pair {
        int node, dist;
        Pair(int n, int d) { node = n; dist = d; }
    }

    public static int[] dijkstra(int n, List<List<Pair>> graph, int src) {
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        PriorityQueue<Pair> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a.dist));
        pq.add(new Pair(src, 0));

        while (!pq.isEmpty()) {
            Pair curr = pq.poll();
            if (curr.dist > dist[curr.node]) continue;

            for (Pair nei : graph.get(curr.node)) {
                int newDist = dist[curr.node] + nei.dist;
                if (newDist < dist[nei.node]) {
                    dist[nei.node] = newDist;
                    pq.add(new Pair(nei.node, newDist));
                }
            }
        }
        return dist;
    }

    public static void main(String[] args) {
        int n = 5;
        List<List<Pair>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());

        graph.get(0).add(new Pair(1, 2));
        graph.get(0).add(new Pair(2, 4));
        graph.get(1).add(new Pair(2, 1));
        graph.get(1).add(new Pair(3, 7));
        graph.get(2).add(new Pair(4, 3));
        graph.get(3).add(new Pair(4, 1));

        int[] dist = dijkstra(n, graph, 0);
        System.out.println("Distances: " + Arrays.toString(dist));
    }
}
```

✅ Output Example:

```
Distances: [0, 2, 3, 9, 6]
```

---

## 3️⃣ **Bellman-Ford – Handles Negative Weights**

📌 Relaxes all edges **V-1 times**.

* Detects negative weight cycles.
* Time Complexity: **O(V \* E)**

**Java Snippet:**

```java
import java.util.*;

public class BellmanFord {
    static class Edge {
        int u, v, w;
        Edge(int u, int v, int w) { this.u = u; this.v = v; this.w = w; }
    }

    public static int[] bellmanFord(int n, List<Edge> edges, int src) {
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        for (int i = 0; i < n - 1; i++) {
            for (Edge e : edges) {
                if (dist[e.u] != Integer.MAX_VALUE && dist[e.u] + e.w < dist[e.v]) {
                    dist[e.v] = dist[e.u] + e.w;
                }
            }
        }

        // Check negative cycle
        for (Edge e : edges) {
            if (dist[e.u] != Integer.MAX_VALUE && dist[e.u] + e.w < dist[e.v]) {
                throw new RuntimeException("Negative Weight Cycle Detected!");
            }
        }

        return dist;
    }

    public static void main(String[] args) {
        int n = 5;
        List<Edge> edges = Arrays.asList(
            new Edge(0, 1, -1),
            new Edge(0, 2, 4),
            new Edge(1, 2, 3),
            new Edge(1, 3, 2),
            new Edge(1, 4, 2),
            new Edge(3, 2, 5),
            new Edge(3, 1, 1),
            new Edge(4, 3, -3)
        );

        System.out.println("Distances: " + Arrays.toString(bellmanFord(n, edges, 0)));
    }
}
```

✅ Output Example:

```
Distances: [0, -1, 2, -2, 1]
```

---

## 4️⃣ **Floyd-Warshall – All-Pairs Shortest Path**

📌 Dynamic Programming on adjacency matrix.

* Time Complexity: **O(V³)**
* Works with negatives (no cycles).

**Pseudo Steps:**

```
for k in 1..V
  for i in 1..V
    for j in 1..V
      dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

✅ Best when **graph is dense** and we need **all-pairs shortest paths**.

---

## 5️⃣ **A\* Algorithm – Heuristic Shortest Path**

📌 Used in **AI (pathfinding in games, maps)**.

* Uses **f(n) = g(n) + h(n)**

  * `g(n)` → cost so far
  * `h(n)` → heuristic (like Euclidean/Manhattan distance)

✅ More efficient than Dijkstra when heuristic is good.

---

## 🎯 **Problem Applications**

* **Network Routing (OSPF uses Dijkstra, RIP uses Bellman-Ford)**
* **Google Maps shortest route** → Dijkstra + A\*.
* **Game AI Pathfinding** → A\*.
* **Course Prerequisite Scheduling** → Topological + Shortest Path in DAG.

---

## ⚡ **Key Takeaways**

* **Unweighted Graph → BFS**
* **Positive Weights → Dijkstra**
* **Negative Weights → Bellman-Ford**
* **All-Pairs → Floyd-Warshall**
* **Heuristic Pathfinding → A\***

