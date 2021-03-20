# FWT

这个东西啊，会用就好了啊？

本质是要求下标按位运算的卷积，即$C_{k} = \sum\limits_{i\oplus j=k}A_i B_j$，其中 $\oplus$ 是二元位运算。

代码：

```cpp
//由于不会发生循环卷积，空间开够后，运算长度是len，那么lim=1<<(log2(len-1)+1)即可。
void OR(int *f, int opt)
{
    for (int o = 2, k = 1; o <= lim; o <<= 1, k <<= 1)
        for (int i = 0; i < lim; i += o)
            for (int j = 0; j < k; j++)
                (f[i+j+k] += 1LL * f[i+j] * opt % mod) %= mod;
}

void AND(int *f, int opt)
{
    for (int o = 2, k = 1; o <= lim; o <<= 1, k <<= 1)
        for (int i = 0; i < lim; i += o)
            for (int j = 0; j < k; j++)
                (f[i+j] += 1LL * f[i+j+k] * opt % mod) %= mod;
}

void XOR(int *f, int opt)//求逆时x = 1 / 2.不在模系时需要直接修改成 / 2
{   for (int o = 2, k = 1; o <= lim; o <<= 1, k <<= 1)
        for (int i = 0; i < lim; i += o)
            for (int j = 0; j < k; j++)
                (f[i+j] += f[i+j+k]) %= mod,
                f[i+j+k] = (f[i+j] - f[i+j+k] - f[i+j+k]) % mod,
                f[i+j] = 1LL * f[i+j] * opt % mod, f[i+j+k] = 1LL * f[i+j+k] * opt % mod;
                //f[i+j] = 1LL * f[i+j] / (opt == 1 ? 1 : 2), f[i+j+k] = 1LL * f[i+j+k] / (opt == 1 ? 1 : 2);
}
```

## 一些习题

### CF662C Binary Table

这题，首先按逻辑推一推。

对于行的翻转，可以抽象成一个值域为$[0,2^n-1]$的集合$X$。那么此时的最少的1可以写成：

$ans[X] = \sum\limits_{i=1}^{m}f(S_i\oplus X)$，其中$f(S_i\oplus X)$是对于某列取反或者不取反取最小值。那么原答案就是$ans = \min\{ans[X]\}$

发现有些列的状态是一样的。换一种枚举方式：

$ans[X] = \sum\limits_{i=0}^{2^n-1}cnt_if(i\oplus X)$，$cnt_i$为状态为$i$的列的个数。

$ans[X] = \sum\limits_{i=0}^{2^n-1}\sum\limits_{j=0}^{2^n-1}cnt_if(j)[i\oplus X = j]$。贡献的套路划分计算。由于异或满足异或两次等于其他数原数，有

$ans[X] = \sum\limits_{i=0}^{2^n-1}\sum\limits_{j=0}^{2^n-1}cnt_if(j)[i\oplus j = X] = ans[X] = \sum\limits_{i\oplus j = X}cnt_i f_j$，然后就开心的AC了。复杂度为$O(n2^n)$。

## P6097 【模板】子集卷积

求 $c_{k}=\sum\limits_{i \& j=0 \atop i \mid j=k} a_{i} b_{j}$ ， $k\in[0,2^n-1],n\in[0,20]$ 。

$i | j = k$ 可以使用FWT来计算。考虑提出 $i\&j = 0$ ，这个即为 $|i| + |j| = |i \cup j|$ 。

令 $f_{i, j}=\left\{\begin{array}{l}a_{j}(|j|=i) \\ 0\end{array}, g_{i, j}=\left\{\begin{array}{l}b_{j}(|j|=i) \\ 0\end{array}\right.\right.$ ， $h_{i}=\sum_{k=0}^{i} f_{k} * g_{i-k}$ ，此时的 $h_{|i|,i}$ 即满足了 $|i| + |j| = |i \cup j|$ 。但还需满足 $i | j = k$ ，故形式应为

$\begin{aligned} h_{i} &=\sum_{i} \operatorname{IFWT}\left(\operatorname{FWT}\left(f_{k}\right) \cdot \operatorname{FMT}\left(g_{i-k}\right)\right) \\ &=\operatorname{IFWT}\left(\sum_{i} \operatorname{FMT}\left(f_{k}\right) \cdot \operatorname{FMT}\left(g_{i-k}\right)\right) \end{aligned}$

推得的下式，是由于FWT，IFWT具有可加性，可以加完后再IFWT。