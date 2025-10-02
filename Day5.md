
#🏗️ Topological Sort & DAG Problems

When dealing with **Directed Acyclic Graphs (DAGs)**, ordering of vertices becomes a powerful tool. This is where **Topological Sort** comes into play.

It’s the backbone of problems like **course scheduling, dependency resolution, and task scheduling**.

---

## 🔑 **What is Topological Sort?**

* A **linear ordering of vertices** such that for every directed edge `u → v`, vertex `u` comes before `v`.
* Only valid in **DAGs** (Directed Acyclic Graphs).
* Multiple valid orders may exist.

**Example:**
Graph:

```
1 → 2 → 3  
|         
↓         
4 → 5
```

Possible topological order: `1, 4, 5, 2, 3`.

---

## ⚡ **Approaches**

### 1. **DFS-Based Topological Sort**

* Perform DFS.
* After visiting all neighbors of a node, **push it onto a stack**.
* At the end, pop elements from the stack = topological order.

**Java Snippet (DFS-based):**

```java
import java.util.*;

public class TopoSortDFS {
    private static void dfs(int node, boolean[] visited, Stack<Integer> stack, List<List<Integer>> graph) {
        visited[node] = true;
        for (int nei : graph.get(node)) {
            if (!visited[nei]) {
                dfs(nei, visited, stack, graph);
            }
        }
        stack.push(node);
    }

    public static List<Integer> topoSort(int n, List<List<Integer>> graph) {
        boolean[] visited = new boolean[n];
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(i, visited, stack, graph);
            }
        }
        
        List<Integer> result = new ArrayList<>();
        while (!stack.isEmpty()) {
            result.add(stack.pop());
        }
        return result;
    }

    public static void main(String[] args) {
        int n = 6;
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        
        graph.get(5).add(2);
        graph.get(5).add(0);
        graph.get(4).add(0);
        graph.get(4).add(1);
        graph.get(2).add(3);
        graph.get(3).add(1);

        System.out.println("Topological Sort (DFS): " + topoSort(n, graph));
    }
}
```

✅ Output Example:

```
[5, 4, 2, 3, 1, 0]
```

---

### 2. **Kahn’s Algorithm (BFS-based)**

* Compute **indegree** (number of incoming edges) for each vertex.
* Put vertices with **indegree = 0** in a queue.
* Remove them one by one, decrease indegree of neighbors.
* Continue until queue is empty.

**Java Snippet (Kahn’s Algorithm):**

```java
import java.util.*;

public class TopoSortBFS {
    public static List<Integer> topoSort(int n, List<List<Integer>> graph) {
        int[] indegree = new int[n];
        for (int i = 0; i < n; i++) {
            for (int nei : graph.get(i)) {
                indegree[nei]++;
            }
        }

        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) q.add(i);
        }

        List<Integer> result = new ArrayList<>();
        while (!q.isEmpty()) {
            int node = q.poll();
            result.add(node);
            for (int nei : graph.get(node)) {
                indegree[nei]--;
                if (indegree[nei] == 0) q.add(nei);
            }
        }

        return result.size() == n ? result : new ArrayList<>(); // empty if cycle
    }

    public static void main(String[] args) {
        int n = 6;
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        
        graph.get(5).add(2);
        graph.get(5).add(0);
        graph.get(4).add(0);
        graph.get(4).add(1);
        graph.get(2).add(3);
        graph.get(3).add(1);

        System.out.println("Topological Sort (BFS/Kahn): " + topoSort(n, graph));
    }
}
```

---

## 🎯 **Common Problems**

1. **Course Schedule (LeetCode 207)** → detect cycle in DAG using BFS/DFS.
2. **Course Schedule II (LeetCode 210)** → return valid topological order.
3. **Alien Dictionary** → infer letter order using topological sort.
4. **Task Scheduling with Dependencies** → check if valid execution order exists.

---

## ⚡ **Key Insights**

* **DFS is recursive & stack-based** → good for implementation, but harder for cycle detection.
* **Kahn’s algorithm (BFS)** → directly checks cycles (if some nodes never get indegree=0).
* Works only in **DAGs** → cycle detection is often integrated.

---

📌 With this, you’ve mastered **ordering in DAGs** — a must-have skill for scheduling and dependency problems.

---

