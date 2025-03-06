## Euler Path 

1. Any random traversal of graph is called walk. (vertex and edges can be repeated)
2. A walk in which no edge is repeated is called trail. (vertex can be repeated)
3. A trail which starts and end at same vertex is called euler circuit. Every edge must be visited. |><|
4. A graph containing euler circuit is called euler graph.
5. All the edges should be in single component. If there are multiple components then no edge should exist in other components.
6. All the vertices should have even degree.
7. All the vertices with non zero degree are connected in a component. Rest all vertices must have zero degree.
9. Graph with no edge is a euler graph.
10. Semi eulerian path doesn't start and end at same vertex, but covers all edges exactly once. |-<|
11. Algorithm :
    Check for connectivity :
      find a node with degree > 0. If no node found, then its a euler graph.
      else do dfs. Post dfs check if any node with degree > 0 is unvisited. 
    count no. of odd degree nodes.
      =0 --> euler graph
      =2 --> semi euler graph
      >2 --> not euler graph
      
https://gist.github.com/SuryaPratapK/b750d259655ad0523e281762fa93d2d2
