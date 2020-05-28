# 二分图匹配
本页面部分定义引自本科同学renfei的[个人博客](https://www.renfei.org/blog/bipartite-matching.html)。

## 问题（最大二分匹配）
给定二分图，求解其最大匹配的匹配边数

### 二分图
二分图 $G=(V,E)$ 是一个无向图，其顶点集 $V$ 可分解为两个互不相交的子集 $A, B$，对 $\forall e=(a, b) \in E$ 有 $a \in A, b \in B$

### 匹配
在图论中，一个「匹配」（matching）是一个边的集合，其中任意两条边都没有公共顶点。

* 最大匹配：一个图的所有匹配中，所含匹配变数最多的匹配，则称其为一个最大匹配

* 完美匹配：如果一个图的所有匹配中，所有顶点都是匹配点，则称其为一个完美匹配


## 算法
二分图的最大匹配问题在CLRS的解法是由最大网络流的算法规约得到。但一种更简便的方式是通过不断寻找 **增广路** 的方式得到，这种求最大匹配的算法被称为匈牙利算法。

### 交替路与增广路
* 交替路：从一个未匹配点出发，依次经过非匹配边、匹配边、非匹配边…形成的路径叫交替路。

* 增广路：从一个未匹配点出发，走交替路，如果途径另一个未匹配点（出发的点不算），则这条交替路称为增广路（agumenting path）

增广路有一个重要特点：非匹配边比匹配边多一条。因此，研究增广路的意义是改进匹配。只要把增广路中的匹配边和非匹配边的身份交换即可。由于中间的匹配节点不存在其他相连的匹配边，所以这样做不会破坏匹配的性质。交换后，图中的匹配边数目比原来多了 $1$ 条。

我们可以通过不停地找增广路来增加匹配中的匹配边和匹配点。找不到增广路时，达到最大匹配（这是增广路定理）。匈牙利算法正是这么做的。


## 代码
```cpp
class Match {
   public:
    Match(int _n, int _m) : n(_n), m(_m) {
        assert(0 <= n && 0 <= m);
        g.resize(n);
    }

    void Add(int from, int to) {
        assert(0 <= from && from <= n && 0 <= to && to <= m);
        g[from].push_back(to);
    }

    int MaxMatchNum() {
        int res = 0, iter = 0;
        vector<int> pa(n, -1), pb(m, -1), was(n, 0);

        while (1) {
            iter++;
            int add = 0;
            function<bool(int)> Dfs = [&](int v) {  // Find an augmenting path starting from v
                was[v] = iter;
                for (int u : g[v])
                    if (pb[u] == -1) {
                        pa[v] = u;
                        pb[u] = v;
                        return true;
                    }
                for (int u : g[v])
                    if (was[pb[u]] != iter && Dfs(pb[u])) {
                        pa[v] = u;
                        pb[u] = v;
                        return true;
                    }
                return false;
            };
            
            for (int i = 0; i < n; i++) if (pa[i] == -1 && Dfs(i)) add++;
            if (add == 0) break;
            res += add;
        }
        return res;
    }

   private:
    int n, m;
    vector<vector<int>> g;
};
```

* $n, m$ - 二分图两个互不相交顶点集的顶点数

* $g$ - 二分图

* $res$ - 当前匹配数

* $pa$ - $A$ 中顶点的匹配对象，失配为 $-1$

* $pb$ - $B$ 中顶点的匹配对象，失配为 $-1$

* $iter$ - 当前迭代次数

* $was$ - $A$ 中顶点最近被修改的迭代轮次（实现中配合$iter$用于寻找当前轮次中未被修改的点，避免增广路找到已经被改变的点上）


## 讨论

Q: 上述实现中每次迭代都从左侧开始寻找增广路，这样有没有可能漏掉从右侧开始的增广路呢？
A: 不会，因为增广路的长度必然是奇数跳，这导致起点终点必然位于分别位于左右两侧，由二分图的无向性，不妨取左侧点为起点。
