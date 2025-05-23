## Bipartite Graph 

1. Color the graph with 2 colors such that no two adjacent nodes have same color.
2. Any graph with no cycle or even cycle length can be bipartite.

### BFS

```
class Solution {
    
    private :
    bool bfs(int start, vector<int> adj[], vector<int> & color) {
        color[start] = 0;
        queue<int> q;
	    q.push(start);
        
        while(!q.empty()) {
            int node = q.front();
            q.pop();
            for (int adjNode : adj[node]) {
                if (color[adjNode] == -1) {
                    color[adjNode] = !color[node];
                    q.push(adjNode);
                } else if(color[adjNode] == color[node]) {
	                return false; 
	            }
            }
        }
        return true;
     }
    
    public :
    bool isBipartite(int n, vector<int> adj[]) {
        vector<int> color(n, -1);
        
        for (int i = 0;i<n;i++) {
            if (color[n] == -1) {
                if (!bfs(i, adj, color)) return false;
            }
        }
        return true;
    }
}
```

DFS 

```
class Solution {
    
    private :
     bool dfs(int start, int col, vector<int> adj[], vector<int> & color) {
        color[start] = col;
        
        for (int adjNode : adj[start]) {
            if (color[adj] == -1) {
                if (!dfs(adjNode, !col, adj, color)) {
                    return false;
                }
            } else if (color[adjNode] == color[node]) {
                return false;
            }
        }
        return true;
     }
    
    
    public :
    bool isBipartite(int n, vector<int> adj[]) {
        vector<int> col(n, -1);
        
        for (int i = 0;i<n;i++) {
            if (col[n] == -1) {
                if (!dfs(i, 0, adj, col)) return false;
            }
        }
        return true;
    }
}
```
