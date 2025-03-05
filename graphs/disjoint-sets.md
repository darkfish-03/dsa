## Disjoint Set 
1. This data structure provides the following capabilities. We are given several elements, each of which is a separate set. A DSU will have an operation to combine any two sets, and it will be able to tell in which set a specific element is.
2. find(v) - returns the representative (also called leader) of the set that contains the element v. This representative is an element of its corresponding set. It is selected in each set by the data structure itself (and can change over time, namely after union_sets calls). This representative can be used to check if two elements are part of the same set or not. a and b are exactly in the same set, if find(a) == find(b). Otherwise they are in different sets.
3. union(a, b) - merges the two specified sets (the set in which the element a is located, and the set in which the element b is located)
4. The data structure allows you to do each of these operations in almost  $O(1)$  time on average.
5. We will store the sets in the form of trees: each tree will correspond to one set. And the root of the tree will be the representative/leader of the set.
6. 

### Naive Implementation

```
int find(int v) {
    if (v == parent[v])
        return v;
    return find(parent[v]);
}

void union(int a, int b) {
    a = find(a);
    b = find(b);
    if (a != b)
        parent[b] = a;
}

This implementation is inefficient. It is easy to construct an example, so that the trees degenerate into long chains. In that case each call find_set(v) can take O(n) time.
```

### Path Compression
1. This optimization is designed for speeding up find_set.
2. If we call find(v) for some vertex v, we actually find the representative p for all vertices that we visit on the path between v and the actual representative p.
3. First find the representative of the set (root vertex), and then in the process of stack unwinding the visited nodes are attached directly to the representative.
4. This simple modification of the operation already achieves the time complexity O(log n) per call on average

### Union By Rank/ Size

1. In this optimization we will change the union operation.
2. We will change which tree gets attached to the other one. In the naive implementation the second tree always got attached to the first one. In practice that can lead to trees containing chains of length  O(n). With this optimization we will avoid this by choosing very carefully which tree gets attached.
3. Amortized complexity is the total time per operation, evaluated over a sequence of multiple operations. The idea is to guarantee the total time of the entire sequence, while allowing single operations to be much slower then the amortized time.
4. If we combine both optimizations - path compression with union by size / rank - we will reach nearly constant time queries. It turns out, that the final amortized time complexity is O(alpha(n)), where alpha(n) is the inverse Ackermann function, which grows very slowly.

### Implementation

```
class UnionFind {
    vector<int> parent, rank, size;
    
    public : 
    UnionFind(int n) {
        rank.resize(n + 1, 0);
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
        }
    }
    
    int find(int u) {
        if (u == parent[u]) {
            return u;
        }
        return parent[u] = find(parent[u]);
    }
    
    void unionByRank(int u, v) {
        int par_u = find(u);
        int par_v = find(v);
        if (par_u == par_v) return;
        if (rank(par_u) < rank(par_v)) {
            parent[par_u] = par_v;
        } else if (rank(par_v) < rank(par_u)){
            parent[par_v] = par_u;
        } else {
            parent[par_v] = par_u;
            rank[par_u]++;
        }
    }
    
    void unionBySize(int u, v) {
        int par_u = find(u);
        int par_v = find(v);
        if (par_u == par_v) return;
        if (size(par_u) < size(par_v)) {
            parent[par_u] = par_v;
            size[par_v] += size[par_u];
        } else {
            parent[par_v] = par_u;
            size[par_u] += size[par_v];
        }
    }
}
```

### Applications

https://cp-algorithms.com/data_structures/disjoint_set_union.html#applications-and-various-improvements
