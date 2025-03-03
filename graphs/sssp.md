# Single Source Shortest Path

## BFS/ DFS

1. Can only solve the “shortest path” problem in “unweighted graphs”. In real scenario a shortest distance path might be more time consuming. 

## Dijkastra Algorithm

1. Can only be used to solve the “single source shortest path” problem in a graph with non-negative weights.
2. “Dijkstra's Algorithm” uses a “greedy approach”.
3. Each step selects the “minimum weight” from the currently reached vertices to find the “shortest path” to other vertices.
4. Edge relaxation means checking if going through a specific edge provides a shorter path to a node. If it does, we update the shortest distance to that node. For example: You know the shortest distance to a city A. You check if taking the road (edge) from A to city B gives a shorter distance to B than what you already know. If yes, update the shortest distance to B. This is called relaxing the edge.
5. Intuition : https://leetcode.com/discuss/general-discussion/1059477/A-noob%27s-guide-to-Djikstra%27s-Algorithm

## Bellman-Ford Algorithm

1. Can solve the “single-source shortest path” in a weighted directed graph with any weights, including, of course, negative weights.

## Floyd-Warshall Algorithm
