# Z函数（扩展KMP）

## 问题
给定字符串 $s$，返回字符串 $z$，$z[i]$ 是 $s[i, n-1]$ 与 $s$ 的LCP（最长公共前缀）的长度。

## 代码
```cpp
vector<int> ZFunction(string s) {
    int n = s.length();
    vector<int> z(n);
    for (int i = 1, l = 0, r = 0; i < n; ++i) {
        if (i <= r) z[i] = min(z[i - l], r - i + 1);
        while (i + z[i] < n && s[z[i]] == s[i + z[i]]) z[i]++;
        if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
    }
    return z;
}
```

## 算法
维护已发现的最靠右侧的匹配段 $[l ,r]$，即算法当前扫描到的最靠右的位置。

注意到算法中 `for` 循环的初始位置为 $1$ ，如果初始位置为 $0$ 则第一次执行循环时 $[l, r]$ 被设置成 $[0, n - 1]$ ，算法进而退化成朴素的 $O(n^2)$ 版本。

## 复杂度
* 时间　$O(n)$
注意到 `for` 循环最多执行 $n$ 次，只需证明内层 `while` 循环的执行次数上线是 $O(n)$ 的。不难证明 `while` 每次执行必然使右边界 `r` 增大 $1$。

