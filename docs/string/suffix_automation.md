# 后缀自动机
本节内容部分来自陈立杰2012NOI冬令营讲稿，在此对陈立杰对SAM所做的总结表示感谢。

本节将从性质分析、线性时间构造算法两个层面来讨论后缀自动机。


## SAM分析
设要分析的字符串为，SAM的本质是一个增加了后缀链的DFA（有限状态自动机）。其状态是的所有子串的分组，分组的依据是子串的结束位置集合（right集合）。

* 对于一个状态，其所包含的所有子串是其中最长字符串的一系列长度连续的后缀。

* 不同状态对应的right集合要么不相交，要么相互真包含，这条性质保证了状态数是线性的。

* 不同状态对应的right集合之间的包含关系个构成了一棵树状结构，将其称为parent树。


## SAM的线性构造算法
对于每个状态 $st$ ，记录其中最长字符串的长度 $val$ ，另外记录其在 $parent$ 树上的父亲地址，以及其在 $DFA$ 中的转移边。则其中包含字符串的长度区间为 $[st \rightarrow par \rightarrow val, st \rightarrow val]$ 。采用如下增量法在线构造：

令当前串为 $T$ ，加入字符 $x$ 

1. 令 $p=ST(T), Right(p)=\{length(T)\}$的节点
2. 新建 $np=ST(Tx), Right(p)=\{llength(T) + 1\}$的节点 
3. 对于 $p$ 和 $p$ 没有标号 $x$ 的祖先 $v$ , $trans(v, x)=np$
4. 找到 $p$ 的第一个存在标号 $x$ 的边的祖先 $v_p$ 。如果这样的 $v_p$ 不存在，那么 $Parent(np)=root$ ，算法终止
5. 令 $q=trans(v_p, x)$ ，若 $MAX(q)=MAX(v_p) + 1$ ，令 $Parent(np)=q$ ，算法终止
6. 否则新建节点 $nq$ ，令 $trans(nq, *)=trans(q, *)$ 
7. 用 $nq$ 替代 $q$ 在 $parent$ 树中的位置，让 $q$ 和 $np$ 都成为 $nq$ 的孩子
* $Parent(nq)=Parent(q)$

* $Parent(q) = nq$

* $Parent(np)=nq$
8. 对所有 $trans(v, x)=q$ 的 $p$ 的祖先 $v$ ，$trans(v, x)$ 改成 $nq$


## 代码
```cpp
struct State {
    State *par, *trans[26];
    int val;
    State(int _val) : par(0), val(_val) { memset(trans, 0, sizeof trans); }
};

State *root, *last = new State(0);

void extend(int w) {
    State *p = last;
    State *np = p ? new State(p->val + 1) : new State(1);
    while (p && p->trans[w] == 0) p->trans[w] = np, p = p->par;
    if (p == 0)
        np->par = root;
    else {
        State *q = p->trans[w];
        if (p->val + 1 == q->val)
            np->par = q;
        else {
            State *nq = new State(p->val + 1);
            memcpy(nq->trans, q->trans, sizeof q->trans);
            nq->par = q->par;
            q->par = nq;
            np->par = nq;
            while (p && p->trans[w] == q) p->trans[w] = nq, p = p->par;
        }
    }
    last = np;
}
```


## 应用
1. 求字符串 $s$ 的所有循环移位中字典序最小的

沿 $s\#s$ 的SAM按照字典序移动 $len(s)$ 次得到的字符串为所求，时间空间复杂度都是线性。
