# Single Source Shortest Path

## BFS/ DFS

1. Can only solve the “shortest path” problem in “unweighted graphs”. In real scenario a shortest distance path might be more time consuming. 

## Dijkastra Algorithm

1. Can only be used to solve the “single source shortest path” problem in a graph with non-negative weights for directed/ undirected graphs.
2. “Dijkstra's Algorithm” uses a “greedy approach”.
3. Each step selects the “minimum weight” from the currently reached vertices to find the “shortest path” to other vertices.
4. Edge relaxation means checking if going through a specific edge provides a shorter path to a node. If it does, we update the shortest distance to that node. For example: You know the shortest distance to a city A. You check if taking the road (edge) from A to city B gives a shorter distance to B than what you already know. If yes, update the shortest distance to B. This is called relaxing the edge.
5. Intuition : https://leetcode.com/discuss/general-discussion/1059477/A-noob%27s-guide-to-Djikstra%27s-Algorithm
6. With the visited array, we ensure that a node is processed exactly once, but it increases memory usage. Without the visited array, it's possible that the same node could be stored in the priority queue, but if the same node with a smaller distance already exists, it will be processed first.
7. Code References : https://leetcode.com/discuss/interview-question/6131652/Dijkstra%27s-Algorithm-or-All-3-Implementations-or-Readable-C%2B%2B-Code
8. If there is an edge with negative weight then dijkastra alg will keep oscillating between the nodes. 

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
        return dist;
    }
}

Time Complexity Analysis :

O(V*(pop vertex from minHeap + no. of edges on each vertex*push into minHeap))
O(V*(log(minHeapSize) + ne(log(minHeapSize)))
O(V*(log(minHeapSize))(ne + 1))
O(V*(log(minHeapSize))*V)
O(V^2*(log(minHeapSize)))
O(E*(log(V)))
```

## Bellman-Ford Algorithm

1. Can solve the “single-source shortest path” in a weighted directed graph with any weights, including, of course, negative weights.

## Floyd-Warshall Algorithm
