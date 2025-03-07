## Graph Alg on 2D Grid 

There are many problems where we need to apply graph algoorithm like BFS, DFS, Dijkastra's Alg on 2D grid. 

Cell --> Node
Sides --> Edges / Sides + Corner --> Edges

### DFS 

```
int vis[n+1][n+1];
int dx[] = {1, 0, -1, 0};
int dy[] = {0, 1,  0, -1};

bool isValid(int x, int y) {
    if (x < 1 || x > n || y < 1 || y > N) {
        return false;
    }
    if (vis[x][y] == 0) {
        return true;
    }
    return false;
}


dfs(int x, int y) {
    for (int i = 0;i<4;i++) {
        int new_x = x + dx[i];
        int new_y = y + dy[i];
        if (isValid(new_x, new_y)) {
            dfs(new_x, new_y);
        }
    }
}

int main() {
    dfs(1, 1);
}
```
