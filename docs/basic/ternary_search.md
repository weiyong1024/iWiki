# 三分搜索

## 问题
已知函数 $f(x)$ 在区间 $[l, r]$ 上单峰且连续，求 $f(x)$ 在 $[l, r]$上的极值。


## 算法
使用标准三分搜索算法，每次迭代将当前区间的长度缩小 $\frac{1}{3}$ 。

理论上最终收敛到极值点或极值区间的左端，但实际中由于复点误差导致的判等错误，当 $f(x)$ 在区间 $[l_m, r_m]$ 上取极值时，最终定位往往在线段内的随机点，但返回的函数值仍可视为 $f(x)$ 在 $[l, r]$ 上的极值。


## 代码

在 $[-1000, 1000]$ 范围内搜索单峰连续函数 $f(x)$ 极小值

```cpp
double ternary_search() {
    double l = -1000, r = 1000;
    for (int i = 0; i < 300; i++) {
        double m1 = (2 * l + r) / 3;
        double m2 = (l + 2 * r) / 3;
        if (f(m1) >= f(m2)) l = m1;
        else r = m2;
    }
    return f(l);
}
```

注： 迭代 $100$ 次后区间长度为先前的 $2.4597 \times 10^{-18}$ ，迭代 $1000$ 次之后区间长度为缩减到原来的 $1.4881 \times 10^{-53}$ ，故一般数百次的迭代能够满足算法竞赛中的精度要求。


## 变体 - 平面三分搜索

当需要求解的单峰连续二元函数 $f_{x, y}(x, y)$ 在矩形区域 $x \in [l_x, r_x], y \in [l_y, r_y]$ 上的极值时，可使用平面三分搜索算法。具体地，分两层调用一维三分搜索算法。

从几何意义来看，平面三分搜索首先利用一维三分搜索求解给定 $x$ 时函数在 $y$ 方向上的极值 $f_x(x) = \max_{y \in [l_y, r_y]}f(x, y)$，然后利用三分搜索求解 $f_x(x)$ 在 $x$ 方向上的极值。


### 代码

在 $[-1000, 1000] \times [-1000, 1000]$ 上搜索单峰函数 $f(x)$ 的极小值

```cpp
double t_search_y(double x) {
    double l = -1000, r = 1000;
    for (int i = 0; i < 300; i++) {
        double m1 = (2 * l + r) / 3;
        double m2 = (l + 2 * r) / 3;
        if (f(x, m1) >= f(x, m2)) l = m1;
        else r = m2;
    }
    return f(x, l);
}
double t_search_x() {
    double l = -1000, r = 1000;
    for (int i = 0; i < 300; i++) {
        double m1 = (2 * l + r) / 3;
        double m2 = (l + 2 * r) / 3;
        if (t_search_y(m1) >= t_search_y(m2)) l = m1;
        else r = m2;
    }
    return t_search_y(l);
}
```


## 模板封装

### 一维三分搜索
```cpp
class TernarySearch {
   public:
    TernarySearch(function<double(double)> _f) : f(_f) {
        l = -1000, r = 1000;
    }

    double MaxValue() {
        for (int i = 0; i < iter_num; i++) {
            double m1 = (2 * l + r) / 3;
            double m2 = (l + 2 * r) / 3;
            if (f(m1) >= f(m2)) l = m1;
            else r = m2;
        }
        return f(l);
    }

   private:
    int iter_num = 300;
    double l, r;
    function<double(double)> f;
};
```

### 平面三分搜索
```cpp
class TernarySearch2D {
   public:
    TernarySearch2D(function<double(double, double)> _f) : f(_f) {
        lx = -1000, rx = 1000, ly = -1000, ry = -1000;
    }

    double MaxValue() {
        double l = lx, r = rx;
        for (int i = 0; i < iter_num; i++) {
            double m1 = (2 * l + r) / 3;
            double m2 = (l + 2 * r) / 3;
            if (MaxValueY(m1) >= MaxValueY(m2)) l = m1;
            else r = m2;
        }
        return MaxValueY(l);
    }

   private:
    double MaxValueY(double x) {
        double l = ly, r = ry;
        for (int i = 0; i < iter_num; i++) {
            double m1 = (2 * l + r) / 3;
            double m2 = (l + 2 * r) / 3;
            if (f(x, m1) >= f(x, m2)) l = m1;
            else r = m2;
        }
        return f(x, l);
    }

    int iter_num = 300;
    double lx, rx, ly, ry;
    function<double(double, double)> f;
};
```
