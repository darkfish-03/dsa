## In and Out time of nodes

1. Given 2 nodes, find whether one node lies in the subtree of another node. 
2. In the context of DFS, in-time refers to the moment when a vertex is first visited (explored), and out-time refers to the moment when the DFS has finished exploring all the neighbors of a vertex (i.e., when the algorithm finishes processing the vertex and backtracks).
3. Application : Bridges and Articulation Points, Flattening of tree.
4. 
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
