# 埃拉托色尼筛选法

## 问题
寻找 $n$ 以内所有素数


## 算法
初始化长度为 $n$ 的空表未被标记，从 $2$ 到 $n$ 递增，若当前元素未被标记，则记录该元素为素数，并标记该元素在 $n$ 以内的所有倍数为合数。

据Codeforces上的水友所述该算法的时间复杂度为 $O(n\log n \log n)$


## 代码
```cpp
const int N = 20;
bool vis[N + 5];
std::vector<int> Eratosthenes_sieve() {
    std::vector<int> primes;
    for (int i = 2; i <= N; i++) {
        if (!vis[i]) primes.push_back(i);
        for (int j = i * i; j <= N; j += i)
            if (!vis[j]) vis[j] = true;
    }
    return primes;
}
```


## 应用
1. 生成 $n$ 以内所整数的最小素因数  
在标记 $p$ 的所有倍数的过程中 $p$ 即为所有倍数的最小素因数
