# 乘法逆元


## 问题
在 $O(n)$ 时间内求出 $0$ 到 $n$ 之间所有数在 $\pmod p$ 意义下的乘法逆元。


## 代码
```cpp
#include <bits/stdc++.h>

using namespace std;
using ll = long long;

const int maxn = 1000005;
const ll MOD = 1000000007;

ll qpw(ll a, ll b) {
    ll ans = 1;
    while (b) {
        if (b & 1) ans = ans * a % MOD, b--;
        a = a * a % MOD;
        b >>= 1;
    }
    return ans;
}

ll inv[maxn + 1];
void pre_process() {
    inv[1] = 1;
    for (int i = 2; i <= maxn; i++)
        inv[i] = (MOD - MOD / i) * inv[MOD % i] % MOD;
}
```

## 算法
对于任意正整数 $x$ ，在 $\pmod p$意义下的乘法逆元基于非马小定理可以用快速幂在 $O(\log p)$ 的时间求出，即

$$
x^{-1}=x^{p-2} \pmod {p-2}
$$

更进一步，利用 **动态规划** 可以在线性时间 $T(n)$ 生成前 $n$ 个正整数的乘法逆元表。

对于 $x^{-1} \pmod p$，首先将 $p$ 分成 $x$ 的余数和倍数两部分：

$$
\begin{align}
    p &= (p\%x) + \lfloor \frac{p}{x} \rfloor \times x \\
      &= a + bx 
\end{align}
$$

其中 $a=p\%x, b=\lfloor\frac{p}{x}\rfloor$ 

于是有

$$
a + bx \equiv 0 \pmod p
$$

将 $a$ 移到右边并在两边同时乘 $b^{-1}$

$$
x \equiv -b^{-1}a \pmod p
$$

对两边取逆

$$
x^{-1} = (-b)a^{-1} \pmod p
$$

将 $-b=p-\lfloor\frac{p}{x}\rfloor$$ 带入上式，得到

$$
x^{-1}=(p-\lfloor\frac{p}{x}\rfloor)(p\%x) \pmod p
$$

至此得到最优子结构，由于 $(p%x) < x$，故在求解 $x^{-1}$ 时， $(p\%x)^{-1}$ 是已经求解过的重叠子问题，可以直接得到结果。
