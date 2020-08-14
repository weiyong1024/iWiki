# 二分搜索


## 问题
寻找阈值：若所求问题具有大于等于某一阈值“可行”、小于该阈值“不可行”的二段性，或者属于寻找满足条件的最小数量问题，可利用寻找阈值的二分搜索求解


## 代码
`check()`函数用于判定当前值是否可行

### 连续型区间的二分搜索

在 $x \in [0, 10^9]$ 范围内寻找满足条件 $check(x)=true$ 的指定精度的最小 $x$

```cpp
double bi_search() {
	double l = 0, r = 1e9;
	for (int i = 0; i < 300; i++) {
		double m = (l + r) / 2l;
		if (check(m)) r = m;
		else l = m;
	}
	return l;
}
```

迭代步数可由 **初始区间长度** 和 **精度要求** 确定。

### 离散型区间的二分搜索

在 $x \in [0, n - 1], x \in \mathbb{Z}$ 范围内寻找满足条件 $check(x)=true$ 的最小 $x$ ，如不存在返回 $-1$ 。

```cpp
int bi_search() {
	int ans = -1;
	int l = 0, r = n - 1;
	while (l <= r) {
		int m = (l + r) >> 1;
		if (check(m)) ans = m, r = m - 1;
		else l = m + 1;
	}
	return ans;
}
```

$[l, r]$ 维护的是剩余搜索域。


## 应用

### 在有序数组中判断目标值的存在性

以数组下标为搜索域，实现函数返回`bool`型变量，判断目标值 $target$ 是否在序列 $a$ 中出现。

```cpp
bool bi_search(vector<int> a, int target) {
    int l = 0, r = a.size() - 1;
    while (l <= r) {
        int m = (l + r) >> 1;
        if (a[m] == target) return true;
        if (a[m] < target) l = m + 1;
        else r = m - 1;
    }
    return false;
}
```

$[l, r]$ 维护的是剩余搜索域。
