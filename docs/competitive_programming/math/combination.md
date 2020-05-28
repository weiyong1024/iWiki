# 组合数

## 问题

设计一个类，提供计算组合数 $C_x^y=\frac{x!}{y!(x-y)!}$ 的方法，结果对 $10^9 + 7$ 取模


## 代码
以下`Combination`提供`int C(int x, int y)`方法计算组合数$C_x^y$

默认构造函数要求 $0 \leq y \leq x \leq 2e5$ ，并提供了修改上限的构造函数。

```cpp
class Combination {
   public:
    Combination() { PreProcess(); }

    Combination(int _mx) : mx(_mx) { PreProcess(); }

    int C(int x, int y) {  // Choose y from x.
        assert(0 <= y), assert(y <= x), assert(x <= mx);
        return 1ll * fac[x] * facinv[y] % mod * facinv[x - y] % mod;
    }

   private:
    int mx = 2e5;
    int mod = 1e9 + 7;
    vector<int> fac, facinv;

    int qpower(int a, int b) {
        int ans = 1;
        while (b) {
            if (b & 1) ans = 1ll * ans * a % mod;
            a = 1ll * a * a % mod;
            b >>= 1;
        }
        return ans;
    }

    void PreProcess() {
        fac.resize(mx + 1), facinv.resize(mx + 1);
        fac[0] = 1;
        for (int i = 1; i <= mx; i++) {
            fac[i] = 1ll * fac[i - 1] * i % mod;
        }
        facinv[mx] = qpower(fac[mx], mod - 2);
        for (int i = mx - 1; i >= 0; i--) {
            facinv[i] = 1ll * facinv[i + 1] * (i + 1) % mod;
        }
    }
};
```


## 算法

根据组合数计算式 $C_x^y=\frac{x!}{y!(x-y)!}$，问题的关键在于预处理求出 $x \in [0, x_{max}]$ 范围内所有数的阶乘 $x!$ 和阶乘的乘法逆元 $(x!)^{-1}$ 。

显然，阶乘表 $x!$ 可以在 $O(n)$时间内得到。

下面求阶乘表的逆元表：

由

$$
((x - 1)!)^{-1} = (x!)^{-1} \cdot x
$$

可知阶乘逆元表可降序生成，那么如何获得初始值 $(x_{max}!)^{-1}$ 呢？

根据费马小定理：

$$
a^{p-1} \equiv 1 \pmod p
$$

可得

$$
a \cdot a^{p-2} \equiv 1 \pmod p
$$

由上式可知 $a^{p-2}$ 可作为 $a$ 在 $\pmod p$ 意义下的乘法逆元。

即

$$
(x_{max}!)^{-1} = (x_{max}!)^{p-2} \pmod p
$$