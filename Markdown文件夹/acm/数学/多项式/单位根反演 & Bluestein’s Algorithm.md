# 单位根反演 & Bluestein’s Algorithm

## 单位根反演

xx反演，就是将一个式子演成含xx的式子。

单位根反演的式子为：$\frac{1}{n} \sum_{i=0}^{n-1}\left(\omega_{n}^{k}\right)^{i}=[n \mid k]$ 

分类讨论，易证。

## 循环卷积 & DFT|IDFT

 **循环卷积** 的式子定义：$c_{r}=\sum_{p, q}[(p+q) \bmod n=r] a_{p} b_{q}$，其中$a_i,b_i,i\in[0,n-1]$处均有定义。

求法：单位根反演展开：$[(p+q)\bmod n = r] = [n \mid (p + q - r)] = \frac{1}{n}\sum\limits_{i=0}^{n-1}(w_n^{p+q-r})^i$

放回式子里，有$c_r = \frac{1}{n}\sum\limits_{k=0}^{n-1}w_n^{-rk}\sum\limits_{p=0}^{n-1}a_pw_n^{pk}\sum\limits_{q=0}^{n-1}b_qw_n^{qk}$。发现后面两个式子很独立，令$A_k = \sum\limits_{p=0}^{n-1}a_pw_n^{pk},B_k = \sum\limits_{q=0}^{n-1}b_qw_n^{qk}$以便于观察。

很明显，根据**DFT**的定义，$A_k$是关于多项式$f(x) = \sum a_ix^i$的点值形式，$B_k$同理（设其多项式为$g(x)$）。故后面两项为以长度为$n$的$\mathrm{DFT}(f(x))*\mathrm{DFT}(g(x))$。

类似的，对于$c_k$来说，右边的式子明显为 **IDFT** 的定义，即为$\mathrm{IDFT}(\mathrm{DFT}(f(x))*\mathrm{DFT}(g(x)))(n)$。故证得：

**DFT**为关于多项式的长度为$n$的循环卷积， **IDFT** 则为关于多项式的长度为$n$的循环逆卷积。在复数域，单位根为$w_n^1 = \cos\frac{2\pi}{n} + i\sin\frac{2\pi}{n}$；在模域下，单位根为$g^{\frac{p-1}{n}}$，$g$是关于模数$p$的原根。

通常的FFT会在一个多项式后面补零，以避免循环卷积的影响（因为$\bmod 2^k$（$2^k$为卷积的长度）压根没影响正常的多项式运算）而如果就要以长度为$n$的**循环卷积**，则不能补零，就以长度为$n$来做DFT/IDFT。

对于长度$n = 2^k$来说，自然可以使用一般的DFT/IDFT，由于 **循环卷积** 的式子里要求单位根$w_n$恰好以多项式长度$n$为划分单位，而对于一般的DFT/IDFT来说运算长度$lim=n$，所以可直接做。而对于任意的多项式长度$n$，谨提出两种解法：递归法，Bluestein's Algorithm。

循环卷积的性质：对长度为$n$的多项式$f(x)$乘$x$，相当于对$f(x)$向右循环平移一位。

## 递归法

考虑普通的FFT/NTT，DFT求出奇偶项系数表示的子多项式对应的 **点值** ，利用$A(x) = A_1(x^2) + xA_2(x^2)$求出多项式$A$的点值；IDFT则带入共轭单位根，运算结束后除运算长度$n$。

该式可以递归的本质是此时多项式的运算长度$lim = 2^k$，第若干层的子多项式的运算长度均为$2$的倍数，所以可以递归到$1$。

于是乎就可以提出递归法的FFT/NTT的做法：将多项式长度$n$按质因子分解，每次选一个质因子$P$，将多项式分为$P$份（当$P=2$时自然就分成两份，即奇偶两式）。

拿$P=3$举例，分成三式多项式，

$A_0(x) = a_0 + a_3x + a_6x^2 + \dots + a_{n-3}x^{\frac{n}{3}}$

$A_1(x) = a_1 + a_4x + a_5x^2 + \dots + a_{n-2}x^{\frac{n}{3}}$

$A_2(x) = a_2 + a_5x + a_7x^2 + \dots + a_{n-1}x^{\frac{n}{3}}$，

则$A(x) = A_0(x^3) + xA_1(x^3) + x^2A_2(x^3)$

递归求得$A_0,A_1,A_2$的点值，有多项式$A$的点值为$A(w_n^j) = \sum\limits_{i=0}^{P-1}w_n^{ij}A_i(w_{\frac{n}{P}}^{j\bmod \frac{n}{p}})$，这里的$n$为当前递归的运算长度。可以预处理出初始多项式长度$n'$的单位根，有$w^{q}_n = w_{n'}^{\frac{qn'}{n}}$，加快运算速度。

瓶颈在于最大的质因子$P_m$，复杂度为$O(P_mnlog_{P_m}n)$。

如果还不知道怎么写，参考 **P4191 [CTSC2010]性能优化** 的代码。

## Bluestein's Algorithm

采用的 **核心策略** 为$ij = \left(\begin{array}{c}i+j \\ 2\end{array}\right)-\left(\begin{array}{l}i \\ 2\end{array}\right)-\left(\begin{array}{l}j \\ 2\end{array}\right)$

`DFT`：

$y_{k}=\sum\limits_{i=0}^{n-1} a_{i} \omega_{n}^{k i}$

出现了指数$ki$，用策略变换一下有：$\omega_{n}^{-\dbinom{k}{2}} \sum\limits_{i=0}^{n-1} a_{i} \omega_{n}^{\dbinom{k+i}{2}} \omega_{n}^{-\dbinom{i}{2}}$是个卷积式，可以用ntt解决。

具体的，令$f(x) = \sum\limits_{i=0}^{n-1}a_iw_n^{-\dbinom{i}{2}},f' = rev(f),g(x) = w_n^{\dbinom{i}{2}}$，运算长度为$lim \geq 3n$。

`IDFT`：

$c_{k}=\frac{1}{n} \sum\limits_{i=0}^{n-1} a_{i} \omega_{n}^{-k i}$

$ = \frac{1}{n} \omega_{n}^{\dbinom{k}{2}} \sum\limits_{i=0}^{n-1} a_{i} \omega_{n}^{\dbinom{i}{2}} \omega_{n}^{-\dbinom{k+i}{2}}$

同样也是卷积式。

具体的，令$f(x) = \sum\limits_{i=0}^{n-1}a_iw_n^{\dbinom{i}{2}},f' = rev(f),g(x) = w_n^{-\dbinom{i}{2}}$，运算长度为$lim \geq 3n$。

复杂度上界为3个完整的FFT/NTT即9个 **DFT/IDFT** ，由于求$a,b$点值的结构相似，可优化几个 **DFT/IDFT** ，常数仍很大就是了。

## 一些题目

### P5293 [HNOI2019]白兔之舞

若某个思路没有办法推进下去，需去考虑其他思路以走向终点。

先考虑$n = 1$的情况，很明显$x = y = 1$。

令$W = w[1][1]$。题目要求走$i$步模$k$等于$t$的方案数，而走$i$步可考虑组合意义：从$[1,L]$挑$i$个点走，则一共有方案数$\dbinom{L}{i}W^i$种方案数。$i\bmod k = t$，则枚举$i$，$ans_t = \sum\limits_{i=0}^{L}[k \mid (i-t)] \dbinom{L}{i}W^i$。见到互质，套路往单位根反演思路走，有

$ans_t = \sum\limits_{i=0}^L \dbinom{L}{i}W^i \frac{1}{k}\sum\limits_{j=0}^{k-1}(w_k^{i-t})^j$

$ = \frac{1}{k} \sum\limits_{i=0}^L\dbinom{L}{i} W^i\sum\limits_{j=0}^{k-1}w_k^{ij}w_k^{-tj}$。考虑变换遍历顺序，有

$ = \frac{1}{k}\sum\limits_{j=0}^{k-1}w_k^{-tj}\sum\limits_{i=0}^{L}w_k^{ij}W^i\dbinom{L}{i}$。眼尖一点可以发现后面是二项式的形式，有

$ = \frac{1}{k}\sum\limits_{j=0}^{k-1}w_k^{-tj}(w_k^jW + 1)^L$。后面的式子仅跟$j$有关，可以预处理出来，记作$c_j$。利用bluestein’s algorithm的策略，变换$-tj = \dbinom{t}{2} + \dbinom{j}{2} - \dbinom{t+j}{2}$，原式有

$=\frac{1}{k} \sum\limits_{j=0}^{k-1} \omega_{k}^{\left(\begin{array}{c}t \\ 2\end{array}\right)+\left(\begin{array}{c}j \\ 2\end{array}\right)-\left(\begin{array}{c}t+j \\ 2\end{array}\right)} c_{j}$

$=\frac{1}{k} \omega_{k}^{\left(\begin{array}{l}t \\ 2\end{array}\right)} \sum\limits_{j=0}^{k-1} \omega_{k}^{\left(\begin{array}{l}j \\ 2\end{array}\right)-\left(\begin{array}{c}t+j \\ 2\end{array}\right)}{c}_{j}$

$=\frac{1}{k} \omega_{k}^{\left(\begin{array}{l}t \\ 2\end{array}\right)} \sum\limits_{j=0}^{k-1} \omega_{k}^{\left(\begin{array}{l}j \\ 2\end{array}\right)}{c}_{j} \omega_{k}^{-\left(\begin{array}{c}t+j \\ 2\end{array}\right)}$

令$f(x) = \sum\limits_{i=0}^{k-1} \omega_{k}^{\left(\begin{array}{l}i \\ 2\end{array}\right)}{c}_{i},f'(x) = rev (f(x))(k),g(x) = \sum\limits_{i=0}^{2k-1}\omega_{k}^{-\left(\begin{array}{c}i \\ 2\end{array}\right)}$

则原式有$ans_t = \frac{1}{k} \omega_{k}^{\left(\begin{array}{l}t \\ 2\end{array}\right)} (f'(x)*g(x)(k-1+t))$

不为正常模数，MTT一下即可。

当$n=3$时，实际上只需要将$M$换成给定的$w[][]$矩阵即可（基于三个点分别对下三个点均有对应$w[][]$的贡献），运算元换成矩阵的形式。

### P4191 [CTSC2010]性能优化

依据上面讲的，就是裸题了。如果忘记如何写代码了可以看看代码。

### TN's Kingdom III - Assassination

Bluestein's Algorithm裸题。查出了原来的fft有问题- -

### CF901E Cyclic Cipher

题目大意：给定长为$n$的序列$b,c$，以及式子$c_{k}=\sum_{i=0}^{n-1}\left(b_{i-k \bmod n}-a_{i}\right)^{2}$，求出序列$a$。

拆式子，有$c_k = \sum\limits_{i=0}^{n-1} (b_i^2 + a_i^2) - 2\sum\limits_{i=0}^{n-1}b_{i-k\bmod i}a_i$

由于对于任意的$c_k,k\in[0,n-1]$，都有定值$\sum\limits_{i=0}^{n-1}(b_i^2 + a_i^2)$，考虑 **差分的trick** ：通过求得差分的值，只要确定一个数，就能通过差分的值来确定原来的数。

有$\Delta c_k = c_k - c_{k-1} = 2\sum\limits_{i=0}^{n-1}b_{i-k+1}a_i - 2\sum\limits_{i=0}^{n-1}b_{i-k}a_i$，有两种合并方法，把$b$合并在一起or把$a$合并在一起，先考虑前者（后者是做不了的，原理后面讲）：

有$\Delta c_k = -2b_{n-k}(a_0 - a_{n-1}) - 2\sum\limits_{i=1}^{n-1}b_{i-k}(a_i - a_{i-1})$。记$\Delta a_i = a_i - a_{i-1},\Delta a_0 = a_0 - a_{n-1}$，合并两项有：

$\Delta c_k = -2\sum\limits_{i=0}b_{i-k}\Delta a_{i}$，此时反转$b$，有$\Delta c_k = -2\sum\limits_{i=0}b_{-i-1+k}\Delta a_{i}$。

考虑到$\Delta c_k$下指标为$k$，而卷积式为$k-1$，对$b$进行 **循环平移** 操作：令$g_0(x) = \sum\limits_{i=0}^{n-1} \Delta b_i x^i,g'(x) = -2x$，令$g(x) = g_0(x) * g'(x)$，其中$*$为循环卷积，$h(x) = \sum\limits_{i=0} \Delta c_i x^i,f(x) = \sum\limits_{i=0} \Delta a_i x^i$，则有$f(x) = \frac{h(x)}{g(x)}$，即在循环卷积下的多项式除法。此时可以用Bluestein's Algorithm来做循环卷积的线性卷积部分。而做点值除法时，$f(w_n^i) = \frac{h(w_n^i)}{g(w_n^i)}$时$g(w_n^i)$会否等于0？由题目的性质，由于$b$ **循环线性无关** ，故不会等于0。至于原理也放后面讲。

考虑已经求得了$\Delta a_i$，即有$n-1$个方程组：

$\left\{\begin{aligned}  \Delta a_{1}=& a_{1}-a_{0} \\ \vdots & \\ \Delta a_{n-1}=& a_{n-1}-a_{n-2} \end{aligned}\right.$

假设$a_0$是确定的数，则可通过$a_k = a_0 + \sum\limits_{i=1}^k \Delta a_i$来求得。根据一开始给的方程，$c_0 = \sum\limits_{i=0}^{n-1}(b_i - a_i)^2$，有$na_0^2 + 2\sum\limits_{i=0}^{n-1}(s_i-b_i)a_0 + \sum\limits_{i=0}^{n-1}(s_i - b_i)^2 - c_0 = 0,s_k = \sum\limits_{i=1}^{k}\Delta a_i$，求个一元二次方程组即可。由于要求的序列$a$是 **整数列** ，故$a_0$也需为整数。此时就求得了序列$a$，也说明了为什么会存在两个序列$a_i$。

> 关于循环卷积与线性卷积：
>
> 要注意，循环卷积式子用的单位根$w_n$与线性卷积里用的单位根$w_n$是不同的东西。具体来说，一是循卷里的单位根对模数$p-1$除的是序列的长度$n$即$g^{\frac{p-1}{n}}$，而线卷除的是基于递归思路的长为$2^k$的长度即$g^{\frac{p-1}{2^k}}$。严格意义上循卷和线卷可以不用同一个模数，但在取模过程中可能会出现 **失真** 问题，故一般需统一模数。
>
> 对于一般情况，Bluestein's algorithm都是在实数域上做循卷的（即用FFT），可以看到循卷和线卷都是对$2\pi$的划分，没有失真的问题。而此题的精度要求十分严格，导致不能使用fft来做。通过计算能确定循卷最终运算的序列$\Delta a_i\leq[-6000,6000]$，运用一个trick：让模数$p>2maxx,maxx = 6000$，且$n|p-1,2^k|p-1$。最后运算得到的$\Delta a_i < maxx$则原来是非负数，而$\Delta a_i>maxx$则原来是负数，令$\Delta a_i = mod - \Delta a_i$即可。由于是$p = t*lcm(n,2^k)$，可能是long long型的，运算时基本都要用上long long，且套龟速乘。
>
> 这里还想讲一下在 **模域** 上的运算问题：可能会有疑问，为什么NTT能 **还原** 多项式乘法加法，其实很简单，假设原本的**运算结果**就没超过模数，即不会导致 **失真** 问题时，在作乘作加时其实就相当于走多了几个环，最后回到该回到的地方。虽然运算后原来是 **小数** 的，个人认为也是 **失真**（即个人观点觉得，对于 **作除** 跟乘加减不同，会无法还原，除非运算完后所有系数保证是整数），但这道题可能是由于，如果由$\Delta a_i$来确定的$a_0$是整数，可以推出$a_1,a_2,\dots,a_{n-1}$也是整数的结论（暂时没有证明）

后记：关于为什么拿$b_i$作合并而不是$a_i$，以及题目的循环线性无关如何保证$g(w_n^i)$不为$0$：

首先题目给的条件是，不存在非全零的$x_i$，使得下面的方程组成立：

$\left\{\begin{array}{}x_{0} b_{0} +x_{1} b_{n-1}+\cdots+x_{n-1} b_{1}=0 \\ x_{0} b_{1}  +x_{1} b_{0}+\cdots+x_{n-1} b_{2}=0 \\ \vdots & \\ x_{0} b_{n-1}+x_{1} b_{n-2}+\cdots+x_{n-1} b_{0}  =0\end{array}\right.$

换言之，仅有$x_i = 0,i\in[0,n-1]$时才有唯一解，即有唯一零解，根据 **克拉默法则** 有系数矩阵行列式$|A|$不为0：

$A = \left[\begin{array}{cccc}b_{0} & b_{n-1} & \cdots & b_{1} \\ b_{1} & b_{0} & \cdots & b_{2} \\ \vdots & \vdots & \ddots & \vdots \\ b_{n-1} & b_{n-2} & \cdots & b_{0}\end{array}\right]$

该矩阵比较特殊，每一行由上一行向右平移一位得来，称其为 **循环矩阵（circulant matrix）**。

令$g2(x) = b_0 + \sum\limits_{i=1}b_{n-i}x^i$，

与**范德蒙矩阵**$V = \left[\begin{array}{cccc}1 & 1 & \cdots & 1 \\ \epsilon_{0}^{1} & \epsilon_{1}^{1} & \cdots & \epsilon_{n-1}^{1} \\ \vdots & \vdots & \ddots & \vdots \\ \epsilon_{0}^{n-1} & \epsilon_{1}^{n-1} & \cdots & \epsilon_{n-1}^{n-1}\end{array}\right]$，其中$\epsilon_i = w_n^i$

发现$A V=\left[\begin{array}{cccc}g_{2}\left(\epsilon_{0}\right) & g_{2}\left(\epsilon_{1}\right) & \cdots & g_{2}\left(\epsilon_{n-1}\right) \\ \epsilon_{0}^{1} g_{2}\left(\epsilon_{0}\right) & \epsilon_{1}^{1} g_{2}\left(\epsilon_{1}\right) & \cdots & \epsilon_{n-1}^{1} g_{2}\left(\epsilon_{n-1}\right) \\ \vdots & \vdots & \ddots & \vdots \\ \epsilon_{0}^{n-1} g_{2}\left(\epsilon_{0}\right) & \epsilon_{1}^{n-1} g_{2}\left(\epsilon_{1}\right) & \cdots & \epsilon_{n-1}^{n-1} g_{2}\left(\epsilon_{n-1}\right)\end{array}\right] = \left[\begin{array}{cccc}1 & 1 & \cdots & 1 \\ \epsilon_{0}^{1} & \epsilon_{1}^{1} & \cdots & \epsilon_{n-1}^{1} \\ \vdots & \vdots & \ddots & \vdots \\ \epsilon_{0}^{n-1} & \epsilon_{1}^{n-1} & \cdots & \epsilon_{n-1}^{n-1}\end{array}\right]       \left[\begin{array}{cccc}g_2(\epsilon_0) & 0 & \cdots & 0 \\ 0 & g_2(\epsilon_{1}) & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & g_2(\epsilon_{n-1})\end{array}\right]$

易知$|AV| = |V|\prod\limits_{i=0}^{n-1}g_2(\epsilon_{i})$

对于范德蒙矩阵，$|V| = \prod\limits_{0\leq j<i\leq n-1}(\epsilon_i - \epsilon_j),j<i$时$(\epsilon_i - \epsilon_j)\neq 0$，故$|AV| = |A||V| = |V|\prod\limits_{i=0}^{n-1}g_2(\epsilon_{i})$，故$|A| = \prod\limits_{i=0}^{n-1}g_2(w_n^i)$。由于$|A|\neq 0$，故$\prod\limits_{i=0}^{n-1}g_2(w_n^i) \neq 0 \Rightarrow \forall i,g_2(w_n^i)\neq 0$。由于$g(x) = g_2(x) *(-2x)$，而单位根不为0，所以$g(w_n^i)\neq 0$。而如果合并$a_i$，$g(x) = \sum\limits_{i=0} \Delta b_ix^i$就可能没这样的结论。

另外有神奇的$O(1)$自然溢出快速乘：

```cpp
ll mul(ll a,ll b){return (a*b-(ll)((long double)a*b/mod)*mod+mod)%mod;}
```

[基于原理的博客](http://footoredo.logdown.com/posts/278715/Some-OI-the-game-skills)。~~都不舍得叫他龟速乘了~~

### CF1270I Xor on Figures

首先考虑一维的情况，对于长度为$2^k$的序列$a_i$的生成函数为$A$，有若干的点$x_i$构成的序列（记生成函数为$f(x)$），每次对$x_{i+d\bmod 2^k}$的点异或一个值$w$，使$A$全为零。以下的乘法运算都是 **异或循环卷积** 。比如一维的$[x^k]f(x) = \bigotimes_{i=0}^{k}g_ih_{k-i}$。

先按位思考，对于某一位$bit_t$：要使得$A$为零，即$f(x)$与其向右平移的选中的若干多项式$x^if(x)$的加法等于$A$。有$A_t = 2^t\sum\limits_{i=0}^{2^k-1}f(x)x^is_i$，$s_i$为向右平移$i$位的多项式选不选（即非零即一）。令$g(x) = s_ix^i$，有$g(x)f(x)2^t = A_t$。

很明显要求的就是$g(x)$啦，由于$A$的位与位是加法的关系，即有$g(x)f(x) = A,f_{x[i],y[i]}=1$。扩展至二维为$f_{x,y}g_{x,y} = A$.定义二维的 **异或循环卷积** 为$[x^0y^0](a\times b) = \bigotimes_{i=0}^{2^{k}-1} \bigotimes_{j=0}^{2^{k}-1} a[i][j] \times b\left[(x-i) \bmod 2^{k}\right]\left[(y-j) \bmod 2^{k}\right]$

 现在的难题是如何求$f_{i,j}$在异或循环卷积下的逆元。

首先，多项式的逆元的定义为$f_{x,y}f^{-1}_{x,y} = 1$；

其次，考虑$f^2$，仅有$(2x_i\bmod 2^k,2y_i\bmod 2^k)$的点上才有值，由于$(x_i+x_j,y_i+y_j)$的点会被贡献两次。而$f^{2^k}$有$[x^iy^j]f^{2^k} = [i=0,j=0]$。故$f^{2^k-1} = f^{-1} = \prod\limits_{i=0}^{k-1}f^i$。

所以原式等于$g(x) = A\prod\limits_{i=0}^{k-1}f^i$，由于每个$f_i$至多有$t$个点有值，所以总复杂度为$O(tk4^k)$。

