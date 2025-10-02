#📘 Blog 3: DFS Pattern (Path Exploration, Connectivity, Components) 🌐


## 🔹 Why DFS Matters?

**Depth-First Search (DFS)** explores a graph/tree as far as possible along one branch before backtracking.
Think of **a maze explorer**: keep going forward until you hit a wall, then backtrack and try a different path.

DFS is used when:

* You want to **explore all possible paths** (e.g., backtracking problems).
* You need **connectivity detection** (components, cycles).
* Graph is **huge**, and recursion helps break it naturally.

---

## 🔹 DFS Core Idea

1. Start from a source node.
2. Recursively (or using a stack) visit an unvisited neighbor.
3. Backtrack when no unvisited neighbors remain.

---

## 🔹 DFS Template in Java

### Recursive DFS

```java
import java.util.*;

public class DFSExample {
    public static void dfs(int node, boolean[] visited, List<List<Integer>> graph) {
        visited[node] = true;
        System.out.print(node + " ");
        
        for (int neighbor : graph.get(node)) {
            if (!visited[neighbor]) {
                dfs(neighbor, visited, graph);
            }
        }
    }
    
    public static void main(String[] args) {
        int n = 5; 
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        
        // Undirected edges
        graph.get(0).add(1); graph.get(1).add(0);
        graph.get(0).add(2); graph.get(2).add(0);
        graph.get(1).add(3); graph.get(3).add(1);
        graph.get(2).add(4); graph.get(4).add(2);
        
        boolean[] visited = new boolean[n];
        dfs(0, visited, graph); // Output: 0 1 3 2 4
    }
}
```

### Iterative DFS (using stack)

```java
public void dfsIterative(int start, List<List<Integer>> graph) {
    boolean[] visited = new boolean[graph.size()];
    Stack<Integer> stack = new Stack<>();
    stack.push(start);
    
    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (!visited[node]) {
            visited[node] = true;
            System.out.print(node + " ");
            
            for (int neighbor : graph.get(node)) {
                if (!visited[neighbor]) {
                    stack.push(neighbor);
                }
            }
        }
    }
}
```

---

## 🔹 DFS Use Cases (Patterns)

### **1. Path Exploration**

DFS is the go-to for exploring **all possible paths**.

👉 Example: **Path from source to destination in DAG**

```java
public boolean hasPath(List<List<Integer>> graph, int src, int dest, boolean[] visited) {
    if (src == dest) return true;
    visited[src] = true;
    for (int neighbor : graph.get(src)) {
        if (!visited[neighbor] && hasPath(graph, neighbor, dest, visited)) {
            return true;
        }
    }
    return false;
}
```

👉 Related Problems:

* Word Search (LeetCode 79)
* N-Queens
* All Paths from Source to Target (LeetCode 797)

---

### **2. Connected Components**

DFS helps find how many **disconnected parts** exist in a graph.

👉 Example: **Count connected components in undirected graph**

```java
public int countComponents(int n, int[][] edges) {
    List<List<Integer>> graph = new ArrayList<>();
    for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
    for (int[] e : edges) {
        graph.get(e[0]).add(e[1]);
        graph.get(e[1]).add(e[0]);
    }
    
    boolean[] visited = new boolean[n];
    int count = 0;
    
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            dfs(i, visited, graph);
            count++;
        }
    }
    return count;
}
```

👉 Related Problems:

* Number of Islands (LeetCode 200)
* Connected Components in Graph (LeetCode 323)

---

### **3. Cycle Detection**

DFS helps detect cycles in both directed and undirected graphs.

👉 **Cycle detection in undirected graph**

```java
public boolean hasCycleUndirected(int node, int parent, boolean[] visited, List<List<Integer>> graph) {
    visited[node] = true;
    for (int neighbor : graph.get(node)) {
        if (!visited[neighbor]) {
            if (hasCycleUndirected(neighbor, node, visited, graph)) return true;
        } else if (neighbor != parent) {
            return true;
        }
    }
    return false;
}
```

👉 **Cycle detection in directed graph (using recursion stack)**

```java
public boolean hasCycleDirected(int node, boolean[] visited, boolean[] recStack, List<List<Integer>> graph) {
    if (recStack[node]) return true;
    if (visited[node]) return false;
    
    visited[node] = true;
    recStack[node] = true;
    
    for (int neighbor : graph.get(node)) {
        if (hasCycleDirected(neighbor, visited, recStack, graph)) return true;
    }
    
    recStack[node] = false;
    return false;
}
```

👉 Related Problems:

* Course Schedule (LeetCode 207)
* Detect Cycle in Graph

---

### **4. Topological Sorting (DFS-based)**

DFS can be used to generate topological order in DAGs.

```java
public void topoSortDFS(int node, boolean[] visited, Stack<Integer> stack, List<List<Integer>> graph) {
    visited[node] = true;
    for (int neighbor : graph.get(node)) {
        if (!visited[neighbor]) {
            topoSortDFS(neighbor, visited, stack, graph);
        }
    }
    stack.push(node);
}
```

👉 Related Problems:

* Course Schedule II (LeetCode 210)

---

### **5. Backtracking (DFS with undo step)**

DFS naturally extends to **backtracking problems**.

👉 Example: **Word Search (LeetCode 79)**

```java
public boolean exist(char[][] board, String word) {
    int m = board.length, n = board[0].length;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (dfs(board, word, 0, i, j)) return true;
        }
    }
    return false;
}

private boolean dfs(char[][] board, String word, int idx, int i, int j) {
    if (idx == word.length()) return true;
    if (i<0 || j<0 || i>=board.length || j>=board[0].length || board[i][j] != word.charAt(idx)) 
        return false;
    
    char temp = board[i][j];
    board[i][j] = '#'; // mark visited
    
    boolean found = dfs(board, word, idx+1, i+1, j) || dfs(board, word, idx+1, i-1, j) ||
                    dfs(board, word, idx+1, i, j+1) || dfs(board, word, idx+1, i, j-1);
    
    board[i][j] = temp; // undo (backtrack)
    return found;
}
```

---

## 🔹 DFS Interview Problem Map

* **Path Exploration:** Word Search, N-Queens, All Paths.
* **Connectivity:** Number of Islands, Connected Components.
* **Cycle Detection:** Course Schedule, Detect Cycle in Graph.
* **Topological Sort:** Course Schedule II.
* **Backtracking:** Sudoku Solver, Word Search, Combinations/Permutations.

---

## 🔹 DFS Complexity

* **Time:** `O(V + E)` (Vertices + Edges).
* **Space:**

  * Recursive stack: `O(V)` in worst case.
  * Iterative DFS: `O(V)` for stack + visited.

---

✅ **Key Takeaway:**
DFS is the **Swiss Army knife for exploration problems**:

* Useful when you need to explore **all paths**, **detect cycles**, or **analyze connectivity**.
* Natural fit for **backtracking problems** (try → explore → undo).

---

