# 归并排序

## 代码
```cpp
void merge(int *a, int l, int m, int r) {  // Merge two sub-arraies a[l...m] & a[m + 1...r] into a[l...r].
    int n1 = m - l + 1;
    int n2 = r - m;
    int L[n1], R[n2];
    for (int i = 0; i < n1; i++) L[i] = a[l + i];
    for (int j = 0; j < n2; j++) R[j] = a[m + 1 + j];

    int i = 0, j = 0,
        k = l;  // Initial index of left array, right array, merged array.
    while (i < n1 && j < n2)
        if (L[i] <= R[j])
            a[k++] = L[i++];
        else
            a[k++] = R[j++];
    while (i < n1) a[k++] = L[i++];
    while (j < n2) a[k++] = R[j++];
}

void merge_sort(int *a, int l, int r) {  // Sort array a[l...r].
    if (l >= r) return;
    int m = (l + r) >> 1;
    merge_sort(a, l, m);
    merge_sort(a, m + 1, r);
    merge(a, l, m, r);
}
```


## 算法
分治法，后序递归。

将数组折半分成左、右两部分，分别排序，再对有序的左、右两部分执行合并操作。

时间复杂度 $O(nlogn)$ 。
