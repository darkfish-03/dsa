### Diameter of a tree

1. Refers to longest path between any 2 nodes in a tree. 
2. Brute force : run DFS for each node, and find the distance of farthest node. 
3. Optimal We can find diameter in 2 DFS runs.
4. Take a arbitrary node as root, run a DFS from it and find the farthest node from it. Let this node be x.
5. Run a dfs from x and find the maximum distance from this node to any other node, i.e the diameter of tree.

Problem Set : https://www.spoj.com/problems/PT07Z/

### Subtree Size 

```
int dfs(int src) {
    vis[src] = 1;
    int curr_size = 1;

    for (int adjNode : adj[src)) {
        if (vis[adjNode] == 0) {
           curr_size += dfs(adjNode);
    }
    subTreeSize[node] = curr_size;
    return curr_size;
}
```
