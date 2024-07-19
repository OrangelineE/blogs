# Graph
## DFS
void DFS(vertex v) {
    visited[v] = true;
    for(each adjacent node n : v) {
        if(!visited[n]) {
            DFS(n);
        }
    }
}
## BFS