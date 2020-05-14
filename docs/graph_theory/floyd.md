# 所有点对间的最短路径

## 问题
给定带实数权的有向图 $g$ ，求任意两点之间的最短距离


## 代码
```cpp
#include <bits/stdc++.h>

using namespace std;

const int INF = 1e9;
const int maxn = 10;

int n;
int g[maxn][maxn];

int d[maxn][maxn];
int p[maxn][maxn];

void Floyd() {
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++) d[i][j] = g[i][j], p[i][j] = -1;

    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (d[i][k] + d[k][j] < d[i][j])
                    d[i][j] = d[i][k] + d[k][j], p[i][j] = k;
}
```

* $g$ - 邻接矩阵

* $n$ - 顶点数量

* $d$ - 任意两点间最短路径的长度

* $p$ - 任意两点间最短路径的中转点（用于生成具体路径）


## 算法
Floyd算法属于动态规划， **重叠子问题** 是“可以使用前 $k$ 个顶点作为中转点的最短路径”。
