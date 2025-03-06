## Graph Representation

### Adjacency Matrix 

```
int main() {
   int n, m; // n nodes, m edges
   cin >> n >> m;
   int adj[n+1][n+1];
   
   for (int i = 0;i<m; i++) {
       int u, v;
       cin >> u >> v;
       adj[u][v] = 1;
       adj[v][u] = 1;
   }
}


```

### Adjacency List 

```
int main() {
   int n, m; // n nodes, m edges
   cin >> n >> m;
   vector<int> adj[n+1];
   // vector< pair <int,int> > adjList[n+1]; for weighted graph
   
   for (int i = 0;i<m; i++) {
       int u, v;
       cin >> u >> v;
       adj[u].push_back(v);
       adj[v].push_back(u);
   }
}

Space Complexity :
O(2E)

```


## Graph Traversal

### BFS

```
vector<vector<int>> adj;  // adjacency list representation
int n; // number of nodes
int s; // source vertex

queue<int> q;
vector<bool> used(n);
vector<int> d(n), p(n);

q.push(s);
used[s] = true;
p[s] = -1;
while (!q.empty()) {
    int v = q.front();
    q.pop();
    for (int u : adj[v]) {
        if (!used[u]) {
            used[u] = true;
            q.push(u);
            d[u] = d[v] + 1;
            p[u] = v;
        }
    }
}

if (!used[u]) {
    cout << "No path!";
} else {
    vector<int> path;
    for (int v = u; v != -1; v = p[v])
        path.push_back(v);
    reverse(path.begin(), path.end());
    cout << "Path: ";
    for (int v : path)
        cout << v << " ";
}

Time Complexity :
O(V + 2E)
```

