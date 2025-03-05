## In and Out time of nodes

1. Given 2 nodes, find whether one node lies in the subtree of another node. 
2. In the context of DFS, in-time refers to the moment when a vertex is first visited (explored), and out-time refers to the moment when the DFS has finished exploring all the neighbors of a vertex (i.e., when the algorithm finishes processing the vertex and backtracks).
3. Application : Bridges and Articulation Points, Flattening of tree.
4. The node with higher in time but smaller out time will lie in the subtree of another node. 
```
int timer = 1;

bool dfs(int v) {
    
    visited[v] = 1;
    in[v] = timer++;
    
    for (int child : adj[v]) {
        if (visited[child] == 0) {
            dfs(child)
        }
    }
    out[v] = timer++;
}
```



## Bridges 

1. A bridge is an edge in a graph which when removed makes graph disconneted or in other words increases the number of connected components.
2. During a DFS traversal there are certain edges which are traversed referred as forward edges and edges which are not traversed are called back edges. 
The backedges can never be bridges. Backedges results in cycle.
3. Brute force : for each edge --> remove it and check number of connected components increases or not. 

```
int timer = 0;

vector<int> visited(V, 0);
vector<int> in(V);
vector<int> low(V);

int dfs(int node, int parent) {
    
    visited[node] = 1;
    in[node] = low[node] = timer++;
    
    for (int adjNode : adj[node]) {
        if (adjNode == parent) continue;
        if (visited[adjNode]) {
            // back edge -- cannot be a bridge
            // update the low time to ancestor with lowest in time via which node can be visited
            low[node] = min(low[node], in[adjNode]);
        } else {
            // forward edge
            dfs(adjNode, node);
            // evaluate if forward edge is a bridge
            if (low[adjNode] > in[node]) {
                cout >> "its a bridge" >> node >> "-->" >>  adjNode;
            }
            // attempts to minimise itself. 
            low[node] = min(low[node], low[adjNode]);
        }
    }
}

dfs(0, -1);
```

## Articulation Point

1. An articulation point or cut vertex is a vertex which when removed makes graph disconneted or in other words increases the number of connected components.
2. Not all the endpoints of bridge are articulation points. Only nodes with indegree >=1 would be articulation points. 
3. Articulation points can also exist without existence of bridges (eg *). Hence, finding bridge algorithm cannot be reused for articulation points.
4. Root node requires special handling as it may or may not be articulation point.
5. Since same node can be detected as articulation point by different subtree, that's why use a set. 

```
int n; // number of nodes
vector<vector<int>> adj; // adjacency list of graph

vector<bool> visited;
vector<int> tin, low;
int timer;

void dfs(int v, int p = -1) {
    visited[v] = true;
    tin[v] = low[v] = timer++;
    int children=0;
    for (int to : adj[v]) {
        if (to == p) continue;
        if (visited[to]) {
            low[v] = min(low[v], tin[to]);
        } else {
            dfs(to, v);
            low[v] = min(low[v], low[to]);
            if (low[to] >= tin[v] && p!=-1)
                IS_CUTPOINT(v);
            ++children;
        }
    }
    if(p == -1 && children > 1)
        IS_CUTPOINT(v);
}

void find_cutpoints() {
    timer = 0;
    visited.assign(n, false);
    tin.assign(n, -1);
    low.assign(n, -1);
    for (int i = 0; i < n; ++i) {
        if (!visited[i])
            dfs (i);
    }
}
```

## Strongly Connected Components 
