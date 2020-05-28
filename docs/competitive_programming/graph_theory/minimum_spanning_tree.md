# 最小生成树

## 问题
给定无向连通图 $g$ ，找到一棵生成树，使得边的总权重最小


## 算法I - Prim
维护一个MST点集，初始只有任一点（一般取编号为 $0$ 的点），每次取所有一端在MST点集内部另一端在MST点集外部的边中权值最小的一条，将该边的另一端加入MST点集并记录该边，则经过 $n-1$ 步得到最小生成树。

Prim算法属于贪心法，其正确性基于MST性质：
对于一颗 **正在构造中** 的最小生成树，设 $U, V$ 分别为树的顶点集及其补集。若边 $(u, v)$ 为一端在当前点集中 $u \in U$ ，另一端不在当前点集中 $v \in V$ ，且权重最小的边，则必然存在一颗包含该边 $(u, v)$ 的最小生成树。


## 代码
```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const int maxn = 10;
const int INF = 2e9;

int n;
int G[maxn][maxn];

int NextVertex(int low_cost[], bool mst_set[]) {
    int mn = INF, mn_idx = -1;
    for (int i = 0; i < n; i++)
        if (!mst_set[i] && low_cost[i] < mn) mn = low_cost[i], mn_idx = i;
    return mn_idx;
}

ll PrimMST() {
    int par[n];       // Store which inner vertex to connect for outer vertexes.
    int low_cost[n];  // lowest cost for outer vertexes to connect to a inner vertex.
    bool mst_set[n];  // Mark inner vertices.

    // Initially put the 1st vertex into mst_set.
    mst_set[0] = true, par[0] = -1;
    for (int i = 1; i < n; i++)
        low_cost[i] = G[0][i], mst_set[i] = false, par[i] = 0;

    for (int i = 0; i < n - 1; i++) {  // There are n - 1 steps to construct MST.
        int u = NextVertex(low_cost, mst_set);
        mst_set[u] = true;
        for (int v = 0; v < n; v++)
            if (!mst_set[v] && G[u][v] < low_cost[v])
                par[v] = u, low_cost[v] = G[u][v];
    }

    ll ans = 0;
    for (int i = 1; i < n; i++) ans += G[i][par[i]];
    return ans;
}
```

* $G$ - 邻接矩阵

* $n$ - 顶点数量

* $par$ - $par[i]$ 表示编号为 $i$ 的顶点在被加入MST的时候所连的正在生成中MST上的顶点（用于获取具体生成树的边集， $par[1, n-1]$ 即为所求）

* $low_cost$ - 当前阶段正在生成中的MST之外的点到当前MST的最短距离

* $$mst_set$ - 属于正在生成中的MST的顶点标记

以上实现的时间复杂度为 $O(|V|^2)$ 。


## 算法2 - Kruskal

Kruskal算法的思路很清晰，如使用并查集，可将步骤总结如下：

1. 新建图 $G$ ，图 $G$ 中有与原图相同的顶点，但没有边；

2. 将原图中所有的边按权值从小到大排序；

3. 从权值最小的边开始，如果这两个边连接的两个顶点在图 $G$ 内不在一个连通分量中，则添加这条边到图 $G$ 中；

4. 重复3，直到图 $G$ 只剩一个连通分量。
（注：该图只关注动态连通性，用并查集实现就好。）


## 优化

根据CLRS，如果使用普通二叉堆，则可以将Prim和Kruskal算法的时间复杂度限制在 $O(E\log V)$ ，如果使用斐波那契堆，Prim算法的运行时间将改善为 $O(E+V\log V)$ 。此运行时间在 $|V| \ll |E|$（稀疏图） 的情况下较二叉堆有相当大的改进。