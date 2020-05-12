# 最大公约数


## Euclid算法
计算 $big$ 和 $small$ （其中 $small <= big$ ）的最大公约数

* 只要 $samll$ 不为 $0$ 则：

$big，samll = small，big % small$

* 否则： $big$ 为所求

Euclid算法的时间复杂度为 $O(max(a, b))$ 。


## 代码

迭代版
```cpp
int gcd(int big, int small) {
    int tmp;
    while (small != 0) tmp = small, small = big % small, big = small;
    return big;
}
```

递归版
```cpp
int gcd(int big, int small) {
    if (small == 0) return big;
    return gcd(big % small, small);
}
```
