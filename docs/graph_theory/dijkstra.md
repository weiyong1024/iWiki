# 单源最短路


## 问题
给定带非负权的有向图 $g$ 和源顶点 $s$，求从s出发到图中所有顶点的最短路径长度。


## 代码
```cpp
#include <bits/stdc++.h>

using namespace std;

const int INF = 2e9;
const int maxn = 10;

int n;
int g[maxn][maxn];

bool vis[maxn];
int dis[maxn];
int pre[maxn];

void Dijkstra(int s) {
    for (int i = 0; i < n; i++) {	// Init.
        dis[i] = g[s][i];
        vis[i] = i == s ? true : false;
        pre[i] = dis[i] == INF ? -1 : s;
    }

    for (int i = 0; i < n - 1; i++) {	// n - 1 steps.
        int mn = INF;
        int u = s;
        for (int j = 0; j < n; j++)		// Find the closest vertex u can be reached from s.
            if (!vis[j] && dis[j] < mn)
                u = j, mn = dis[j];
        vis[u] = true;
        for (int j = 0; j < n; j++)		// Update distance according to u
            if (!vis[j] && g[u][j] < INF)
                if (dis[u] + g[u][j] < dis[j])
                    dis[j] = dis[u] + g[u][j], pre[j] = u;
    }
}
```

* $g$ - 邻接矩阵

* $n$ - 顶点数量

* $vis$ - 已访问顶点标记（已访问顶点集合 $U$ ）

* $dis$ - 源点 $s$ 到其他顶点的最短路径长度

* $pre$ - 源点 $s$ 到其他顶点的最短路径上的前驱结点（用于生成具体路径）


## 算法
Dijkstra算法总是选择剩余点集中最近的顶点加入到已访问点集，属于贪心法，欲证明其正确性即是证明贪心过程每一步的局部最优在该问题中就是全局最优解。

算法上述实现的时间复杂度为 $O(|V|^2)$ ，适合稠密图。用基于堆结构的优先队列维护已访问点集可以进一步优化之。算法共 $|V|$ 步，每一步的复杂度为 **寻找最近点** 和 **更新距离** 复杂度之和。根据CLRS，二叉堆实现的时间复杂度为 $O((V+E)\log E)$ ，斐波那契堆实现的时间复杂度为 $O(V\log V + E)$。