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
