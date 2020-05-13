# 快速排序

## 代码
```cpp
void quick(int *a, int l, int r) {  // Sort a[l...r].
    if (l >= r) return;
    int i = l + 1, j = r;
    while (1) {  //
        while (!(a[l] < a[i] || i == r)) i++;
        while (!(a[l] >= a[j] || j == l)) j--;
        if (i < j) {
            int tmp = a[i];
            a[i] = a[j], a[j] = tmp;
        } else
            break;
    }
    int tmp = a[l];
    a[l] = a[j], a[j] = tmp;
    quick(a, l, j - 1);
    quick(a, j + 1, r);
}
```

## 算法
分治法，先序递归。

将序列中的元素按首元大小分边，再对两边分别递归处理。

时间复杂度 $O(n\log{n})$ 。


## 实现
上述“分边”操作
出自唐发根老师的《数据结构》教材，用两个指针从序列两端假币将整个序列按相对第一个元素的大小分类。