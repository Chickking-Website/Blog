---
title: 图的存储与遍历
date: 2018-04-15 17:44:20
tags: [OI,图论,算法模板]
toc: true
---
图的存储与一般有三种形式，邻接矩阵、邻接表、链式前向星（邻接表的一种）。  
本文全文使用这个图来进行演示。  
![图示](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201804/graph_test.png)  
我们规定，一共有 $n$ 个点，$m$ 条边。输入数据的格式为 “起点”、“终点”、“边权”。则这个图的输入数据为: 
```plain
1 2 2
1 4 3
2 3 1
2 5 3
3 1 2
5 3 2
5 4 4
```
### 邻接矩阵
由于邻接矩阵空间消耗巨大，一般不使用。
邻接矩阵初始化时，我们使用无穷。  
$\begin{bmatrix} \infty & \infty & \infty & \infty & \infty \\ \infty & \infty & \infty & \infty & \infty \\ \infty & \infty & \infty & \infty & \infty \\ \infty & \infty & \infty & \infty & \infty \\ \infty & \infty & \infty & \infty & \infty \end{bmatrix}$  
因为对于有些题目，存在瞬移术，可以将某个权重的边跳过，这时可以把它的边权设为 0，这样就和不连通的情况产生了冲突，因此，就不能使用 0 为初值，当然，对于不存在这种情况的题目，仍可使用 0。  
对于这个图，其邻接矩阵为：   
$\begin{bmatrix} 0 & 2 & \infty & 3 & \infty \\ \infty & 0 & 1 & \infty & 3 \\ 2 & \infty & 0 & \infty & \infty \\ \infty & \infty & \infty & 0 & \infty \\ \infty & \infty & 2 & 4 & 0 \end{bmatrix}$  
邻接矩阵的实质，就是用元素的有无来判断两个点是否连通。  
因此，邻接矩阵的遍历效率很低，空间开销也很大，而且不能存储重边，可以存储自环(不过好像用处不大)。  
建图、存储的时间复杂度为 $\text{O}(n^2)$ (若初始化为 $0$ 则为 $O(m)$ 的时间复杂度)，空间复杂度为 $\text{O}(n^2)$。  
遍历的时间复杂度为 $\text{O}(n^2)$。  
模板如下： 
```cpp
#define INF LLONG_MAX
typedef long long ll;
const int MAXN = 1000 + 1;

ll adj_matrix[MAXN][MAXN]; // 邻接矩阵声明，若为不带权图，则可以将 long long 改为 bool.

inline void addEdge(int prev, int next, ll w = 1) {   // 加边，默认为有向图，边权默认为 1，便于使用不带权图
    adj_matrix[prev][next] = w;
}

void init(int maxn, bool is_zero = false) {     // 初始化
    if (is_zero) return;                        // 若初始化为 0 则直接退出
    for (int i = 1; i <= maxn; i++)             // 外循环
        for (int j = 1; j <= maxn; j++) {       // 内循环
            if (i == j) continue;               // 若起点终点则初始化为 0 
            adj_matrix[i][j] = INF;             // 否则初始化为无穷大
        }
}

void read(int const m, bool directed = true, bool have_weight = true) { // m 为数据组数，即边数，directed 为是否有向，have_weight 为是否带权
    int a = 0, b = 0, w = 1;                // 声明变量
    for (int i = 1; i <= m; i++) {          // 开始读入数据
        scanf("%d %d", &a, &b);             // 读入
        if (have_weight) scanf("%d", &w);   // 若带权，就读入权值
        addEdge(a, b, w);                   // 加边
        if (!directed) addEdge(b, a, w);    // 若不带方向，即为无向图，添加反向边
    }
}

void traversal(int amount_of_vertexes) {    // 遍历，传入参数为点数
    for (int i = 1; i <= amount_of_vertexes; i++)
        for (int j = 1; j <= amount_of_vertexes; j++) {
            if (i == j) continue;
            if (adj_matrix[i][j] != INF) ;  //操作一下
        }
}
```
下面我们将介绍一种更加优越的存储方式。
### 邻接表
邻接表，是一种十分神奇的东西，它的基本组成是节点数组和边链表。  
像之前那个图，邻接表如下：   
![邻接表](https://raw.githubusercontent.com/Chickking-Website/SomeFile/master/201804/list_fixed.png)
根据输入可以发现，邻接表是一种**动态**存储方式。  
邻接表的边是倒序存储的，也就是说，邻接表的构建和输入顺序是有关联的，但并不影响。  
邻接表可以存储重边和自环。
事实上，边链表**相对输入顺序**，是**倒序**存储的。  
显然，动态建图存储的时间、空间复杂度为 $\text{O}(m)$。  
遍历的时间复杂度为 $\text{O}(m)$。  
可以看出，因为邻接表使用了链表，因此内存管理是一个大问题，而且写起来也略微麻烦。
模板如下： 
```cpp
typedef long long ll;
const int MAXN = 1000 + 1;

struct Node {
    struct Edge *firstEdge;     // 第一条边 (事实上是最后一条边，因为倒序)
    Node() {
        firstEdge = NULL;       // 设置指针初值为空
    }
} Graph[MAXN];
struct Edge {
    Node *prev, *next;          // 前驱点、后继点
    ll w;                       // 权值
    Edge *nextEdge;             // 下一条边 (事实上是上一条边，因为倒序)
    
    Edge(Node *const &prev, Node *const &next, const ll &w) : prev(prev), next(next), w(w), nextEdge(prev->firstEdge) {}    // 构造函数，利用 initializer list，自动设置 nextEdge 为下(事实上为上)一条边。
};

inline void addEdge(int prev, int next, ll w = 1) {   // 添加边，默认有向，边权 w 默认为 1，便于使用不带权图
    Graph[prev].firstEdge = new Edge (&Graph[prev], &Graph[next], w);
}

void read(int const m, bool directed = true, bool have_weight = true) { // m 为数据组数，即边数，directed 为是否有向，have_weight 为是否带权
    int a = 0, b = 0, w = 1;                // 声明变量
    for (int i = 1; i <= m; i++) {          // 开始读入数据
        scanf("%d %d", &a, &b);             // 读入
        if (have_weight) scanf("%d", &w);   // 若带权，就读入权值
        addEdge(a, b, w);                   // 加边
        if (!directed) addEdge(b, a, w);    // 若不带方向，即为无向图，添加反向边
    }
}

void traversal(int amount_of_vertexes) {    // 遍历，传入参数为点数
    for (int i = 1; i <= amount_of_vertexes; i++) {
        for (Edge* e = Graph[i].firstEdge; e != NULL; e = e->nextEdge) {
            /*e->next ...;
            e->prev ...;
            ...*/ // 此处可以操作
        }
    }
}
```
前方高能预警。
### 链式前向星
链式前向星是一种更加~~神奇~~优越的存储方式。  
链式前向星的结构为一个点信息数组，一个边信息结构体数组，一个当前处理边的编号计数器。其核心思想是给边编号，这样就能方便地用数组存储了。  
链式前向星也可以存储重边和自环。
链式前向星使用了数组来模拟链表，这样就避免了复杂的内存管理。而它是一种类似邻接表的存储方式，因此，也具有空间复杂度低，时间复杂度低的特性。  
显然，由于读入完成则建图完成，建图的时间复杂度为 $\text{O}(m)$，存储的空间复杂度为 $\text{O}(n+m)$。  
遍历的时间复杂度应该为 $\text{O}(m)$ (个人分析，如有误请指正)。  
根据我们的输入，点信息数组 head 的值为：  
```plain
下标：1 2 3 4 5
数据：2 4 5 0 7
```
边信息数组中的信息如下： 
```cpp
edges[1].next = 2 ; edges[1].w = 2 ; edges[1].nextEdge = 0
edges[2].next = 4 ; edges[2].w = 3 ; edges[2].nextEdge = 1
edges[3].next = 3 ; edges[3].w = 1 ; edges[3].nextEdge = 0
edges[4].next = 5 ; edges[4].w = 3 ; edges[4].nextEdge = 3
edges[5].next = 1 ; edges[5].w = 2 ; edges[5].nextEdge = 0
edges[6].next = 3 ; edges[6].w = 2 ; edges[6].nextEdge = 0
edges[7].next = 4 ; edges[7].w = 4 ; edges[7].nextEdge = 6
```
模板如下： 
```cpp
const int MAXN = 1000, MAXM = 2000;
int head[MAXN];     // 存储点的信息
int edge_count;     // 当前处理的边的编号
 
struct Edge {
    int prev, next, nextEdge;   // 前驱、后继、下一条边
    long long w;                // 边权值
} edges[MAXM];

void addEdge(int const a, int const b, int w = 1) {
    edges[++edge_count].prev = a;   // 设置前驱点为起点 a，++edge_count 令当前处理的边的编号 +1
    edges[edge_count].next = b;     // 设置后继点为终点 b
    edges[edge_count].w = w;        // 设置边权
    edges[edge_count].nextEdge = head[a];   // 从点信息数组 head 中取出下一条边的编号，若当前为第一条边则取出初值 0
    head[a] = edge_count;           // 记录当前的边编号到点信息数组 head 中，为下一条边的增添做准备
}

void read(int const m, bool directed = true, bool have_weight = true) { // m 为数据组数，即边数，directed 为是否有向，have_weight 为是否带权
    int a = 0, b = 0, w = 1;                // 声明变量
    for (int i = 1; i <= m; i++) {          // 开始读入数据
        scanf("%d %d", &a, &b);             // 读入
        if (have_weight) scanf("%d", &w);   // 若带权，就读入权值
        addEdge(a, b, w);                   // 加边
        if (!directed) addEdge(b, a, w);    // 若不带方向，即为无向图，添加反向边
    }
}

void traversal(int const n) {               // n 为点数
    for (int i = 1; i <= n; i++) {          // 从第 1 个点开始遍历
        for (int k = head[i]; k; k = edges[k].nextEdge) { // 令 k 为第 1 条边，直到最后一条边，当 k 到达最后一条边时为 0，结束遍历。
            // do something.
        }
    }
}
```
事实上，除了不能像邻接矩阵那样直接判断两个点之间是否连通，链式前向星几乎是完美的。  
在实际应用中，也可以根据题目读入的数据，通过动态数组来做，能够进一步优化空间开销。