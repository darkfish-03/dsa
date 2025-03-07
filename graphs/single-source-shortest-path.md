# Single Source Shortest Path

## BFS

1. Can only solve the “shortest path” problem in “unweighted graphs”.

```
void bfs(int src, vector<vector<int>>& adj, vector<int>& vis, vector<int>& dis) {
    queue<int> q;
    vis[src] = 1;
    dis[src] = 0;
    
    q.push(src);
    
    while(!q.empty()) {
        int curr = q.front();
        q.pop();
        
        for (int adjNode : adj[curr]) {
            if (!vis[adjNode]) {
                vis[adjNode] = 1;
                dis[adjNode] = dis[curr] + 1;
                q.push(adjNode);
            }
        }
    }
}

```

Problems : 

1. https://www.spoj.com/problems/PPATH/ -- > https://leetcode.com/playground/JKXDQFwN

DFS can be used for SSSP on tree only. 

## Dijkastra Algorithm

1. Can only be used to solve the “single source shortest path” problem in a graph with non-negative weights for directed/ undirected graphs.
2. “Dijkstra's Algorithm” uses a “greedy approach”.
3. Each step selects the “minimum weight” from the currently reached vertices to find the “shortest path” to other vertices.
4. Edge relaxation means checking if going through a specific edge provides a shorter path to a node. If it does, we update the shortest distance to that node. For example: You know the shortest distance to a city A. You check if taking the road (edge) from A to city B gives a shorter distance to B than what you already know. If yes, update the shortest distance to B. This is called relaxing the edge.
5. Intuition : https://leetcode.com/discuss/general-discussion/1059477/A-noob%27s-guide-to-Djikstra%27s-Algorithm
6. With the visited array, we ensure that a node is processed exactly once, but it increases memory usage. Without the visited array, it's possible that the same node could be stored in the priority queue, but if the same node with a smaller distance already exists, it will be processed first.
7. Code References : https://leetcode.com/discuss/interview-question/6131652/Dijkstra%27s-Algorithm-or-All-3-Implementations-or-Readable-C%2B%2B-Code
8. If there is an edge with negative weight then dijkastra alg will keep oscillating between the nodes.
9. Set can also be used as it provides same properties (ascending order, fast retrieval and insertions) as that of priority queue. With set we can erase middle entries which is not possible with priority queue. 

```
   class Solution {
    public : 
    vector<int> dijkastra(int n, vector<vector<int>> adj[], int src) {
        
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>> minHeap;
        vector<int> minDistance(V, 1e9);
        
        minDistance[src] = 0;
        minHeap.push({0, src});
        
        while(!minHeap.empty()) {
            int dis = minHeap.top().first;
            int node = minHeap.top().second;
            minHeap.pop();
            if (dis > minDistance[node]) continue;
            
            for (auto it : adj[node]) {
                int adjNode = it[0];
                int edgeWt = it[1];
                int newDistance = dis + edgeWt;
                if (newDistance < minDistance[adjNode]) {
                    minDistance[adjNode] = newDistance;
                    minHeap.push({newDistance, adjNode});
                }
            }
        }
        return minDistance;
    }
}

Time Complexity :
>> O((V+E)log(V))

<Detailed analysis to be added>

Time Complexity :
>> O(log(V))

```

## Bellman-Ford Algorithm

1. Can solve the “single-source shortest path” in a weighted directed graph with any weights, including, of course, negative weights.
2. Can be used to detect negative cycles as well.
3. Edges can be processed in any order.
4. Relax all the edges n-1 times sequentially.
5. Why n-1 iterations ? Consider 1 --> 2 --> 3 --> 4 --> 5 and let say we process edges from (4,5), (4,3), (3,2), (2,1), then we will need n-1 edges.
6. If on nth iteration the min distance for any node gets relaxed, then it implies that graph contains negative cycle.
7. After the execution of  $i_{th}$  phase, the Bellman-Ford algorithm correctly finds all shortest paths whose number of edges does not exceed  $i$
8. Any shortest path cannot have more than  $n - 1$  edges.

```
class Solution {
    public :
    vector<int> bellmanford(int v, vector<vector<int>>& edges, int src) {
        vector<int> minDistance(V, 1e9);
        minDistance[src] = 0;
        
        for (int i = 0;i<V-1;i++) {
            for (auto& it : edges) {
                int u = it[0]; int v = it[1]; int wt = it[2];
                if (minDistance[u] != 1e9 && minDistance[u] + wt < minDistance[v]) {
                    minDistance[v] = minDistance[u] + wt;
                }
            }
        }
        
        for (auto& it : edges) {
                int u = it[0]; int v = it[1]; int wt = it[2];
                if (minDistance[u] != 1e9 && minDistance[u] + wt < minDistance[v]) {
                    return {-1};
                }
            }
        return minDistance;
    }
}

Time Complexity :
>> O(V*E)

```

## Floyd-Warshall Algorithm

1. Multi Source Shortest Path Algorithm.
2. Can be used to detect negative cycles as well.
3. Try path via every vertex for given (u, v). min(d[u][k] + d[k][v])
4. Its a dynamic programming solution.
5. Solved using adjacency matrix.
6. Before  $k$ -th phase ( $k = 1 \dots n$ ),  $d[i][j]$  for any vertices  $i$  and  $j$  stores the length of the shortest path between the vertex  $i$  and vertex  $j$ , which contains only the vertices  $\{1, 2, ..., k-1\}$  as internal vertices in the path.

```
class Solution {  
    public :
    vector<vector<int>> floydwarshall(vector<vector<int>>& matrix) {
        int n = matrix.size();
        
        for (int i = 0;i<n;i++) {
            for (int j = 0;j<n;j++) {
                if (matrix[i][j] == -1) {
                    matrix[i][j] = 1e9;
                }
                if (i == j) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        for (int k = 0;k<n;k++) {
            for (int i = 0;i<n;i++) {
                for (int j = 0;j<n;j++) {
                    matrix[i][j] = min(matrix[i][j], matrix[i][k]+matrix[k][j]);
                }
            }
        }
        
        for (int i = 0;i<n;i++) {
            if (matrix[i][i] < 0) {
                // negative cycle
            }
        }
        
        for (int i = 0;i<n;i++) {
            for (int j = 0;j<n;j++) {
                if (matrix[i][j] == 1e9) {
                    matrix[i][j] = -1;
                }
            }
        }
        return matrix;
    }
}

Time Complexity :
>> O(V^3)

```
