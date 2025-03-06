## Detect a cycle in undirected graph 

### BFS

```
class Solution {
  private: 
  bool detect(int src, vector<int> adj[], int vis[]) {
      vis[src] = 1; 
      // store <source node, parent node>
      queue<pair<int,int>> q; 
      q.push({src, -1}); 
      // traverse until queue is not empty
      while(!q.empty()) {
          int node = q.front().first; 
          int parent = q.front().second; 
          q.pop(); 
          
          // go to all adjacent nodes
          for(auto adjacentNode: adj[node]) {
              // if adjacent node is unvisited
              if(!vis[adjacentNode]) {
                  vis[adjacentNode] = 1; 
                  q.push({adjacentNode, node}); 
              }
              // if adjacent node is visited and is not it's own parent node
              else if(parent != adjacentNode) {
                  // yes it is a cycle
                  return true; 
              }
          }
      }
      // there's no cycle
      return false; 
  }
  public:
    // Function to detect cycle in an undirected graph.
    bool isCycle(int V, vector<int> adj[]) {
        // initialise them as unvisited 
        int vis[V] = {0};
        for(int i = 0;i<V;i++) {
            if(!vis[i]) {
                if(detect(i, adj, vis)) return true; 
            }
        }
        return false; 
    }
};
```

DFS 

```
vector<bool> vis;
bool dfs(vector<vector<int>> &g, int v, int p) {
    vis[v] = true;
    for(int &u:g[v]) {
        if(!vis[u]) {
            // If cycle is found anywhere, return true; Note that return(dfs(g,u,v)) is incorrect.
            if(dfs(g, u, v)) return true;
        } else if(u != p) return true;
    }
    return false;
}
bool isCycle(vector<vector<int>>& adj) {
    int n = adj.size();
    vis.resize(n, 0);
    bool ans = false;
    for(int i=0; i<n; i++) {
        if(!vis[i]) ans |= dfs(adj, i, -1);
    }
    return ans;
}
```

