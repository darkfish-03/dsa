## Spanning Tree
1. A spanning tree is a set of edges such that any vertex can reach any other by exactly one simple path.
2. The spanning tree with the least weight is called a minimum spanning tree.
3. Any spanning tree will necessarily contain  $n-1$  edges.

### Prim's Algorithm

1. The minimum spanning tree is built gradually by adding edges one at a time. At first the spanning tree consists only of a single vertex (chosen arbitrarily). Then the minimum weight edge outgoing from this vertex is selected and added to the spanning tree. After that the spanning tree already consists of two vertices. Now select and add the edge with the minimum weight that has one end in an already selected vertex (i.e. a vertex that is already part of the spanning tree), and the other end in an unselected vertex. And so on, i.e. every time we select and add the edge with minimal weight that connects one selected vertex with one unselected vertex. The process is repeated until the spanning tree contains all vertices (or equivalently until we have  n - 1  edges).
2.  If the graph was originally not connected, then there doesn't exist a spanning tree, so the number of selected edges will be less than  n - 1.
3.  For any cut C of the graph, if the weight of an edge E in the cut-set of C is strictly smaller than the weights of all other edges of the cut-set of C, then this edge belongs to all MSTs of the graph.

```
int spanningTree(vector<vector<int>> adj[], int n) {
    
    priority_queue<pair<int, int>, vector<pair<int, int>, greater<pair<int, int>> minHeap;
    vector<int> visited(n, 0);
    
    int sum = 0;
    minHeap.push({0, 0}) // {wt, node}
        
    while(!minHeap.empty()) {
        int weight = minHeap.top().first();
        int node = minHeap.top().second();
        if (visited[node]) continue;
        visited[node] = 1;
        sum += weight;
        
        for (auto it : adj[node]) {
            int adjNode = it[0];
            int wt = it[1];
            if (!visited[adjNode]) {
               minHeap.push(wt, adjNode);
            }
        }
    }
    return sum;
}
Time Complexity :
O((V+E)logV) 
```

### Kruskal's Algorithm 

1. Kruskal's algorithm initially places all the nodes of the original graph isolated from each other, to form a forest of single node trees, and then gradually merges these trees, combining at each iteration any two of all the trees with some edge of the original graph. Before the execution of the algorithm, all edges are sorted by weight (in non-decreasing order). Then begins the process of unification: pick all edges from the first to the last (in sorted order), and if the ends of the currently picked edge belong to different subtrees, these subtrees are combined, and the edge is added to the answer. After iterating through all the edges, all the vertices will belong to the same sub-tree, and we will get the answer.

```
int spanningTree(int V, vector<vector<int>> adj[]) {
        vector<pair<int, pair<int, int>>> edges;
        for (int i = 0; i < V; i++) {
            for (auto it : adj[i]) {
                int adjNode = it[0];
                int wt = it[1];
                int node = i;

                edges.push_back({wt, {node, adjNode}});
            }
        }
        DisjointSet ds(V);
        sort(edges.begin(), edges.end());
        int mstWt = 0;
        for (auto it : edges) {
            int wt = it.first;
            int u = it.second.first;
            int v = it.second.second;

            if (ds.findUPar(u) != ds.findUPar(v)) {
                mstWt += wt;
                ds.unionBySize(u, v);
            }
        }

        return mstWt;
}

Time Complexity :
O(ElogE)
```
