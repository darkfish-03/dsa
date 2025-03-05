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

1. It is a subset C of vertices such that for any 2 vertices in C there exists a path b/w them in a graph.
2. It is applicable to directed graphs only. There is no such concept in undirected graph.
3. Transposed Graph : direction of edge is reversed. SCC remains unchanged.
4. Condesnsation graph composed of SCC is acyclic in nature.
5. out[ci] > out[cj] if there exists ci --> cj.
6. A dag has atleast one node with 0 indegree.
7. Kosaraju algorithm involves 2 dfs calls. First call determines the order in which node (starting with scc having highest out time as it will have indegree of zero) should be visited during second dfs on transposed graph.

```
class Solution
{
private:
    void dfs(int node, vector<int> &vis, vector<int> adj[], stack<int> &st) {
        vis[node] = 1;
        for (auto it : adj[node]) {
            if (!vis[it]) {
                dfs(it, vis, adj, st);
            }
        }

        st.push(node);
    }
private:
    void dfs3(int node, vector<int> &vis, vector<int> adjT[]) {
        vis[node] = 1;
        for (auto it : adjT[node]) {
            if (!vis[it]) {
                dfs3(it, vis, adjT);
            }
        }
    }
public:
    //Function to find number of strongly connected components in the graph.
    int kosaraju(int V, vector<int> adj[])
    {
        vector<int> vis(V, 0);
        stack<int> st;
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfs(i, vis, adj, st);
            }
        }

        vector<int> adjT[V];
        for (int i = 0; i < V; i++) {
            vis[i] = 0;
            for (auto it : adj[i]) {
                // i -> it
                // it -> i
                adjT[it].push_back(i);
            }
        }
        int scc = 0;
        while (!st.empty()) {
            int node = st.top();
            st.pop();
            if (!vis[node]) {
                scc++;
                dfs3(node, vis, adjT);
            }
        }
        return scc;
    }
};

```
