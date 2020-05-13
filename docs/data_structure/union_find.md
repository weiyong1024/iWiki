# 并查集

## 问题
对于有 $n$ 个节点的图，设计一种维护动态连通性问题的数据结构，支持如下操作：

* Find(i) - 查询编号为 $i$ 的节点所属的连通分量

* Union(i, j) - 连接编号为 $i$ 和 $j$ 的节点（二者所在的连通分量连通）


## 代码
```cpp
class UnionFind {
   public:
    UnionFind(int _sz) : sz(_sz) {
        pre = vector<int>(sz);
        for (int i = 0; i < sz; i++) pre[i] = i;
    }
    int Find(int x) { // Find root.
        int r = x;
        while (r != pre[r]) r = pre[r];
        // Path compress.
        while (x != r) {
            int tmp = pre[x];
            pre[x] = r;
            x = tmp;
        }
        return r;
    }
    void Join(int x, int y) {
        int rx = Find(x), ry = Find(y);
        if (rx != ry) pre[rx] = ry;
    }
    int size() { return sz; }

   private:
    int sz;
    vector<int> pre;
};
```


## 算法

数组 $pre$ 记录了每个节点的前导节点。

并查集每次`Union`操作随机将一个门派的掌门作为另一个门派掌门的直接上级；而每次`Find`操作在逐层向上找到掌门之后又将路径中所有过程节点都改成掌门的直接下级。所以说，当`Union`操作的增多会导致树状数据结构趋于不平衡，而`Find`操作很多的时候，将最终导致所有节点都以掌门为直接上级（汇报距离变成 $1$ ）。 