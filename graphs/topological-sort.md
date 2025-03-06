## Topological Sort

1. Refers to any linear ordering of vertices for a DAG such that if there is an edge b/w u and v, then u appears before in that ordering.

### BFS | Kahn's Algorithm

1. Algorithm Steps:
    Compute In-degrees
    Enqueue all vertices with in-degree 0
    Pull from queue and it to topo order, reduce the indegree of adj vertices by 1, If any updated vertex has in-degree 0, enqueue it.
    Check for cycles - if order doesn't contain all the vertecies, there's a cycle.

```
vector<int> topologicalSort(int N, vector<vector<int>>& adj) {
    vector<int> inDegree(V, 0); // Store in-degrees of all vertices
    vector<int> topoOrder;      // Store the topological order

    for (int i = 0; i < N; i++) {
        for (int v : adj[i]) {
            inDegree[v]++;
        }
    }

    queue<int> q;
    for (int u = 0; u < N; u++) {
        if (inDegree[u] == 0) {
            q.push(u);
        }
    }

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        topoOrder.push_back(u); 
        for (int v : adj[u]) {
            inDegree[v]--;
            if (inDegree[v] == 0) {
                q.push(v);
            }
        }
    }
    if (topoOrder.size() != V) {
        cout << "Graph contains a cycle. No valid topological order exists." << endl;
        return {};
    }
    return topoOrder;
}
```

### DFS

1. Intuition : When starting from some vertex v, DFS tries to traverse along all edges outgoing from v. It stops at the edges for which the ends have been already been visited previously, and traverses along the rest of the edges and continues recursively at their ends. Thus, by the time of the function call dfs(v) has finished, all vertices that are reachable from  v have been either directly (via one edge) or indirectly visited by the search.

```
class Solution {
    private :
    void dfs(int node, vector<int> adj[], vector<int> vis, stack<int> st) {
        visited[node] = 1;
        
        for (auto adjNode : adj[node]) {
            if (!visited[adjNode]) {
                dfs(adjNode, adj, vis, st);
            }
        }
        st.push(node);
    }
    
    vector<int> topoSort(int V, vector<int> adj[]) {
        vector<int> visited(V, 0);
        stack<int> st;
        for (int i = 0;i<V;i++) {
            if (!visited[i]) {
                dfs(i, adj, st, vis);
            }
        }
        
        vector<int> ans;
        while(!st.empty()) {
            ans.push_back(st.top());
            st.pop();
        }
        return ans;
    }
}
```
