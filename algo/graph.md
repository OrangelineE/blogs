# Graph

## Adjacent Matrix 
    
### 有什么好处？

✔️ 直观、简单、好理解

✔️ 方便检查任意一对顶点间是否存在边

✔️ 方便找任一顶点的所有“邻接点”（有边直接相连的顶点）

✔️ 方便计算任一顶点的“度”（从该点发出的边数为“出度”，指向该点的边数为“入度”）

+ 无向图: 对应行（或列）非0元素的个数
+ 有向图: 对应行非0元素的个数是“出度”；对应列非0元素的个数是“入度”

### 有什么不好？

x 浪费空间 —— 存稀疏图（点很多而边很少）有大量无效元素，但是对稠密图（特别是完全图）还是很合算的

x 浪费时间 —— 统计稀疏图中一共有多少条边

## Adjacent List 

### 有什么好处？

✔️ 方便找任一顶点的所有“邻接点”

✔️ 节约稀疏图的空间
需要 N 个头指针 + 2E 个结点（每个结点至少 2 个域）
方便计算任一顶点的“度”:

✔️ 方便计算任一顶点的“度” ？

+ 对无向图: 是的

+ 对有向图: 只能计算“出度”；需要构造“逆邻接表”（存指向自己的边）来方便计算“入度”

### 有什么不好？

x 不方便计算一对顶点间是否存在边



## DFS
It is simialr to `pre-order` traversal of a tree.

Time Complexity:
+ `O(V+E)` (if using adjacent list).
+ `O(V^2)` (if using adjacent matrix).

``` C++
void DFS(vertex v) {
    visited[v] = true;
    for(each adjacent node n : v) {
        if(!visited[n]) {
            DFS(n);
        }
    }
}
```

But the difference is that graph needs a '`visited`' array since there might be `cycles` in the graph.

However, there is no cycle in the tree. Therefore, you don't need to add a 'visited' array when you are traversing a tree.

## BFS
It is similar to the level-order of a tree

```C++
void bfs(vertex v) {
    visited[v] = true;
    queue<vertex> q;
    q.push(v);
    while(!q.empty()) {
        vertex cur = q.front();
         q.pop();

        for(int neighbor : cur) {
            if(!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```
Time Complexity:
+ `O(V+E)` (if using adjacent list).
+ `O(V^2)` (if using adjacent matrix).

## Some Definitions
1. Connectivity

    Definition: If there is an (undirected) path from 
    V to W, then V and W are connected.

2. Path 
    
    Definition: A path from V to W is a sequence of vertices {V, V1, V2, ...,W} such that there is an edge between each pair of consecutive vertices in the graph.

3. Length of Path 
    
    Definition: The length of a path is the number of edges in the path (if weighted, it is the sum of the weights of all edges).

4. Simple Path 

    Definition: If all vertices between V and W are distinct, it is called a simple path. (In other words, there is no cycle in the path)

5. Cycle Path

    Definition: A cycle path (or simply a cycle) in a graph is a path that starts and ends at the same vertex, with all edges and vertices being distinct except for the starting and ending vertex.

6. Connected Components 

    Definition: In an undirected graph, a connected component is a `maximal subgraph` in which any two vertices are connected to each other by paths, and which is connected to no additional vertices in the supergraph.
    连通分量：无向图的极大连通子图
    + 极大顶点数：再加一个顶点就不连通了
    + 极大边数：包含子图中所有顶点相连的所有边

7. Strongly Connected Components (SCCs)
    Definition: In a directed graph, a strongly connected component is a maximal subgraph in which `any two vertices` are `reachable` from each other, meaning there is a directed path from any vertex to every other vertex in the component.

    强连通：有向图中顶点V和W之间存在双向路径，则称V和W是强连通的
    强连通图：有向图中任意两个顶点均强连通
    强连通分量：有向图的极大强连通子图

Example：

Every time when you call dfs(V), you traverse the connected component of Vertex V

## Shortest Path
### Single-Source
#### (Directed) Unweighted Graph

无权单源最短路：BFS

求从起点S到其余各个顶点W的最短路径

1. 需要dist[W]来记录 W 到 V 的距离，也充当visited数组的作用，
这里我们不需要visited数组啦！

    + dist[S] = 0, 其余初始化为-1

2. path[W]记录S到W的路上经过的某顶点


Time Complexity: O(|V| + |E|)

``` C++
void unweighted(Vertex S) {
    queue q;
    q.push(S);
    while(!q.empty()) {
        Vertex v = q.front();
        for(adjacent nodes W of V) {
            if (dist[W] == -1) { //act like `visited` array
                dist[W] = dist[V] + 1;
                path[W] = V; //Its prior node
                q.push(W); 
            }
        }
    }
}
```

#### (Directed) Weighted Graph
`Dijkstra算法` (不能用来解决有负边的情况哦)

+ 令 S = { 源点 s + 已经确定了最短路径的顶点 v_i }

+ 对任一未收录的顶点 v，定义 dist[v] 为到 v 的最短路径长度，但该路径仅经过 S 中的顶点。即路径 { s → (v_i ∈ S) → v } 的最小长度

+ 若路径是按照递增（非递减）的顺序生成的，则真的最短路径必须只经过 S 中的顶点

+ 每次从未收录的顶点中选一个 dist 最小的收录（贪心）
增加一个 v 进入 S，可能影响另外一个 w 的 dist 值！

    `dist[w] = min(dist[w], dist[v] + the weight of <v, w>) `  

+ 需要的数据结构有
    1. int dist[] to calculate the shortest path from the source
    2. bool collective[] to check if the vertex is included in the set S

``` C++
void Dijkstra(Vertex s) {
//sort as the edge weight
while(1) {
    V = 未收录顶点中dist最小者
    if(这样的V不存在){
        break;
    }
    collective[V] = true;
        for(adjacent nodes n of V) {
            if(collective[V] == false) {
                if(dist[cur] + edge[cur][n] < dist[n]) {
                    dist[n] = dist[cur] + edge[cur][n];
                    path[n] = V;
                }
            }
            
        }
    } 
}
```
//TODO: Add C++ Implementation Here

怎么找`未收录顶点中dist最小者`呢？
1. 直接扫描所有未收录顶点——O(|V|)

最终时间复杂度是 O(V^2 + |E|)
2. 可用`最小堆`!
+ 将dist存在最小堆里——O(log|V|)
+ 更新dist[W]的值——O(log|V|)

最终时间复杂度是T = O(|V|log|V| + |E|log|V|) = O(|E|log|V|)

### Multi-Source
1. 可以将单源最短路重复｜V｜遍, T = O(|V|^3 + |E| * |V|)-对稀疏图效果好
2. Floyd算法， T = O(|V^3|)-对稠密图效果好
    因为是稠密图，所以用邻接矩阵

Definition:

Floyd-Warshall is an algorithm for finding the shortest paths in a weighted graph with positive or negative edge weights (but with no negative cycles).

Use Cases:

Finding the shortest paths between all pairs of vertices in a graph.
Used in routing algorithms, network analysis, and various other applications in computer science.

Steps:

1. Initialize the Distance Matrix:

    Create a distance matrix dist[][] where dist[i][j] is the weight of the edge from vertex i to vertex j.
    If there is no edge between i and j, set dist[i][j] to infinity (∞).
    Set dist[i][i] to 0 for all vertices i.
2. Iterative Update:

    Use three nested loops to iterate over all pairs of vertices (i, j) and an intermediate vertex k.
    Update the distance matrix with the following formula:
    ```C++
    if (dist[i][j] > dist[i][k] + dist[k][j])
        dist[i][j] = dist[i][k] + dist[k][j]
    ```


3. After the iterations, dist[i][j] will contain the shortest path distance from vertex i to vertex j.

```C++
void Floyd() {
    for (i = 0; i < N; i++) {
        for (j = 0; j < N; j++) {
            dist[i][j] = graph[i][j];
            path[i][j] = -1;
        }
    }
    for (k = 0; k < N; k++) {
        for (i = 0; i < N; i++) {
            for (j = 0; j < N; j++) {
                if (dist[i][k] + dist[k][j] < dist[i][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                    path[i][j] = k;
                }
            }
        }
    }
}
```


## Topological Sorting

Topological Order:

+ If there is a directed path from V to W in a graph, then V must come before W in the ordering. A sequence of vertices that satisfies this condition is called a topological order.

+ The process of obtaining a topological order is called topological sorting.

AOV:

+ If a graph has a valid topological order, then it must be a Directed Acyclic Graph (DAG).

```C++
void TopSort() {
    for (cnt = 0; cnt < |V|; cnt++) {
        V = 未输出的入度为0的顶点; [1] // O(|V|)
        if (这样的V不存在) {
            Error("图中有回路");
            break;
        }
        输出V, 或者记录V的输出序号;
        for (V 的每个邻接点 W)
            Indegree[W]--;
    }
}
```
T = O(V^2)

We can optimize [1] by putting all in-degree vertices into a container and each time we just need to grab a vertex from the container.

Optimazition:
```C++
void TopSort() {
    for (图中每个顶点 V) {
        if (Indegree[V] == 0)
            Enqueue(V, Q);
    }
    while (!IsEmpty(Q)) {
        V = Dequeue(Q);
        输出V, 或者记录V的输出序号; cnt++;
        for (V 的每个邻接点 W) { //O(E)
            if (--Indegree[W] == 0)
                Enqueue(W, Q);
        }
    }
    if (cnt != |V|)
        Error("图中有回路");
}
```
T = O(|V| + |E|)

It can also `check if the graph is a DAG`.




Example：旅游规划
有了一张自驾旅游路线图，你会知道城市间的高速公路长度、以及该公路要收取的过路费。现在需要你写一个程序，帮助前来咨询的游客找一条出发地和目的地之间的最短路径。如果有若干条路径都是最短的，那么需要输出最便宜的一条路径。

输入格式:

输入说明：输入数据的第1行给出4个正整数N、M、S、D，其中N（2 ≤ N ≤ 500）是城市的个数，顺便假设城市的编号为0~(N−1)；M是高速公路的条数；S是出发地的城市编号；D是目的地的城市编号。随后的M行中，每行给出一条高速公路的信息，分别是：城市1、城市2、高速公路长度、收费额，中间用空格分开，数字均为整数且不超过500。输入保证解的存在。

输出格式:

在一行里输出路径的长度和收费总额，数字间以空格分隔，输出结尾不能有多余空格。

这道题其实就是在Dijkstra算法的基础上进行改进。在求最短路径的基础上，再加上一个求最少费用，当然最小路径的优先级比最小费用要高，所有只有在两条路径长度相同时，才判断这两条路径哪一条所需费用最小，然后选择费用最小的那一条路径。

```C++
#include<iostream>
#define MaxVertex 505
#define INF 100000
typedef int Vertex;
int N; // Vertex Number
int M; // Edge
int S; // Source
int D;  // Destination 
int dist[MaxVertex];  // 
int cost[MaxVertex]; // how much we spend
bool collected[MaxVertex];  // If it is included
int value[MaxVertex][MaxVertex];  // cost for each edge
int G[MaxVertex][MaxVertex];
using namespace std; 


void build(){
	Vertex v1,v2,w1,w2;
	cin>>N>>M>>S>>D;
	for(Vertex i=0;i<N;i++){
		for(Vertex j=0;j<N;j++){
			G[i][j] = INF;
			value[i][j] = INF; 
		}
		cost[i] = 0;
		collected[i] = false;
		dist[i] = INF;
	}
	for(int i=0;i<M;i++){
		cin>>v1>>v2>>w1>>w2;
		G[v1][v2] = w1;
		G[v2][v1] = w1; 
		value[v1][v2] = w2;
		value[v2][v1] = w2;
	}
}


void InitSource(){
	dist[S] = 0;
	collected[S] = true;
	for(Vertex i=0;i<N;i++)
		if(G[S][i]){
			dist[i] = G[S][i];
			cost[i] = value[S][i];
		}
}

//Find the uncollected vertex with the minimum distance
Vertex FindMin(){
	int min = INF;
	Vertex xb = -1;
	for(Vertex i=0;i<N;i++)
		if(S!=i && !collected[i] && dist[i] < min){
			min = dist[i];
			xb = i;
		}
	return xb;
}

void Dijkstra(){
	InitSource();
	while(1){
		Vertex v = FindMin();
		if(v==-1)
			break;
		collected[v] = true;
		for(Vertex w=0;w<N;w++)
			if(!collected[w] && G[v][w])
				if(dist[v] + G[v][w] < dist[w]){  // If there is a shorter path
					dist[w] = dist[v] + G[v][w];
					cost[w] = cost[v] + value[v][w];
				}else if(dist[v] + G[v][w] == dist[w] && cost[v] + value[v][w] < cost[w]){  // If paths are equal but one costs less money
					cost[w] = cost[v] + value[v][w];
				}
	}
}


int main(){
	build();
	Dijkstra();
	cout<<dist[D]<<" "<<cost[D];
	return 0;
}
```

Dijkstra 其它变种：
1. 要求数最短路径有多少条 (Count the Number of Shortest Paths)

    count[V] represents the number of shortest paths from the source vertex s to the vertex V. This count keeps track of how many different ways we can reach vertex V using the shortest path length.

    + count[s] = 1;
    + 如果找到更短路:
        count[W] = count[V];
    + 如果找到等长路:
        count[W] += count[V];

2. 要求边数最少的最短路 (Find the Shortest Path with the Fewest Edges)

    Just imagine that the weight of each edge is 1.

    The count array is used to store the number of edges required to reach each vertex along the shortest path from the source vertex.
    + count[s] = 0;
    + 如果找到更短路:
        count[W] = count[V] + 1; //If there is another weight w, 1 can be changed to w
    + 如果找到等长路:
        count[W] = min(count[W], count[V] + 1);
