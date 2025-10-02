# 📘 Blog 2: BFS Pattern (Level Order, Shortest Path in Unweighted Graphs) 🌐

## 🔹 Why BFS Matters?

**Breadth-First Search (BFS)** explores nodes **level by level**, like ripples spreading from a stone thrown in water.

* **Tree case:** BFS = Level-order traversal.
* **Graph case:** BFS = Find shortest path in unweighted graphs.
* BFS guarantees:

  * First time you visit a node = shortest path (in terms of edges).
  * Works perfectly for problems involving *minimum steps, spreading processes, or layered exploration*.

---

## 🔹 BFS Core Idea

1. Start from a source node.
2. Use a **queue** to explore neighbors before moving deeper.
3. Keep a **visited set** to prevent revisiting.

---

## 🔹 BFS Template in Java

```java
import java.util.*;

public class BFSExample {
    public static void bfs(int n, List<List<Integer>> graph, int start) {
        boolean[] visited = new boolean[n];
        Queue<Integer> queue = new LinkedList<>();
        
        visited[start] = true;
        queue.offer(start);
        
        while (!queue.isEmpty()) {
            int node = queue.poll();
            System.out.print(node + " ");
            
            for (int neighbor : graph.get(node)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }
    }
    
    public static void main(String[] args) {
        int n = 5; // number of nodes
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        
        // Undirected edges
        graph.get(0).add(1); graph.get(1).add(0);
        graph.get(0).add(2); graph.get(2).add(0);
        graph.get(1).add(3); graph.get(3).add(1);
        graph.get(2).add(4); graph.get(4).add(2);
        
        bfs(n, graph, 0); // Output: 0 1 2 3 4
    }
}
```

---

## 🔹 BFS Use Cases (Patterns)

### **1. Level Order Traversal (Tree/Graph)**

* Traverse level by level.
* Problem: **Binary Tree Level Order Traversal (LeetCode 102)**.

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        result.add(level);
    }
    return result;
}
```

---

### **2. Shortest Path in Unweighted Graph**

* BFS ensures the shortest path in terms of **number of edges**.
* Problem: **Shortest Path in Binary Matrix (LeetCode 1091)**, **Word Ladder**.

```java
public int shortestPath(int n, List<List<Integer>> graph, int src, int dest) {
    int[] dist = new int[n];
    Arrays.fill(dist, -1);
    
    Queue<Integer> queue = new LinkedList<>();
    dist[src] = 0;
    queue.offer(src);
    
    while (!queue.isEmpty()) {
        int node = queue.poll();
        for (int neighbor : graph.get(node)) {
            if (dist[neighbor] == -1) {
                dist[neighbor] = dist[node] + 1;
                queue.offer(neighbor);
            }
        }
    }
    return dist[dest];
}
```

---

### **3. Multi-source BFS (Flood Fill / Spread Problems)**

* BFS starting from **multiple sources simultaneously**.
* Problems: **Rotting Oranges (LeetCode 994)**, **Zombie in Matrix**.

```java
public int orangesRotting(int[][] grid) {
    int m = grid.length, n = grid[0].length;
    Queue<int[]> queue = new LinkedList<>();
    int fresh = 0, time = 0;
    
    // Push all rotten oranges first
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 2) queue.offer(new int[]{i, j});
            if (grid[i][j] == 1) fresh++;
        }
    }
    
    int[][] dirs = {{1,0},{-1,0},{0,1},{0,-1}};
    while (!queue.isEmpty() && fresh > 0) {
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            int[] cell = queue.poll();
            for (int[] d : dirs) {
                int x = cell[0] + d[0], y = cell[1] + d[1];
                if (x>=0 && x<m && y>=0 && y<n && grid[x][y]==1) {
                    grid[x][y] = 2;
                    fresh--;
                    queue.offer(new int[]{x, y});
                }
            }
        }
        time++;
    }
    return fresh == 0 ? time : -1;
}
```

---

### **4. BFS on Grids (Matrix as Graph)**

* Treat 2D grid as graph.
* Problems: **Number of Islands (variant)**, **Maze shortest path**.

---

## 🔹 BFS Interview Problem Map

* **Traversal:** Binary Tree Level Order, Clone Graph.
* **Shortest Path (Unweighted):** Word Ladder, Minimum Knight Moves, Binary Matrix Path.
* **Multi-source BFS:** Rotting Oranges, Fire Spread, Walls and Gates.
* **Connectivity:** Number of Islands, BFS Components Count.

---

## 🔹 BFS Complexity

* **Time:** `O(V + E)` (Vertices + Edges).
* **Space:** `O(V)` for queue + visited.

---

✅ **Key Takeaway:**
BFS is the **Swiss Army knife** for *shortest paths, spreading processes, and level exploration*.
It is the **first go-to pattern** when:

* Graph is unweighted.
* You need minimum steps.
* Problem mentions "in how many moves/steps/levels."


