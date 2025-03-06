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


