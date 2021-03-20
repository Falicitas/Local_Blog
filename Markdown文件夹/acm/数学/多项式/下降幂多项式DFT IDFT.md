# 下降幂多项式DFT IDFT

现有多项式$F(x)=\sum_{i=0}^{n-1} a_{i} x^{\underline{i}}$，求该多项式的DFT/IDFT。

首先考虑多项式$x^{\underline{n}}$的点值的EGF：

$ = \sum\limits_{i=0}^{\infty}\frac{i^{\underline{n}}}{i!}x^i = \sum\limits_{i=0}^{\infty}\frac{x^i}{(i-n)!} = x^n\sum\limits_{i=0}^{\infty}\frac{x^i}{i!} = x^ne^x$

设$G(x)$为$F(x)$的一系列点值的多项式。构造$F(x)$的点值的EGF，有

$\sum\limits_{i=0}^{\infty}\frac{F(i)}{i!}x^i = \sum\limits_{i=0}^{\infty}\frac{1}{i!}x^i\sum\limits_{j=0}^{n-1}f_ji^{\underline{j}}$。调换遍历顺序，有

$ = \sum\limits_{j=0}^{n-1}f_j\sum\limits_{i=0}^{\infty}\frac{1}{(i-j)!}x^i = \sum\limits_{j=0}^{n-1}f_jx^j\sum\limits_{i=0}^{\infty}\frac{1}{i!}x^i = e^x\sum\limits_{j=0}^{n-1}f_jx^j$

将$F(x)$看做普通多项式，$G(x)$为点值的EGF，则有$e^xF(x) = G(x)$，点值运算完后（记得点值EGF转OGF），直接$G(x)e^{-x} = F(x)$。

## 下降幂的一些性质

### 下降幂二项式

下降幂二项式$(a+b)^{\underline{n}} = \sum\limits_{i=0}^n \dbinom{n}{i}a^{\underline{i}}b^{\underline{n-i}}$

~~8太想证hhh~~被迫学会证明。

二项式展开下降幂：

$\sum_{i=0}^{n}\left(\begin{array}{c}n \\ i\end{array}\right)\left(\begin{array}{c}a \\ i\end{array}\right)\left(\begin{array}{c}b \\ n-i\end{array}\right) i !(n-i) !$，合并$\dbinom{n}{i}i!(n-i)!$，有

$=n ! \sum_{i=0}^{n}\left(\begin{array}{c}a \\ i\end{array}\right)\left(\begin{array}{c}b \\ n-i\end{array}\right)$，后面两个写成生成函数，闭形式为$(1+x)^a,(1+x)^b$，所以有下式（类比「欧拉数」里的下界为定值）

$=n !\left(\begin{array}{c}a+b \\ n\end{array}\right)=(a+b)^{\underline{n}}$

### 计算平移点值

对于多项式$f(x) = \sum\limits_{i=0}^{n}a_ix^{\underline{i}}$，现要计算平移$m$位的点值$f(m),f(m+1),\dots,f(m+n)$

先列出等价多项式

$f(x+c) = \sum\limits_{i=0}^{n}a_i(x+c)^{\underline{i}}$，二项式展开，

$ = \sum\limits_{i=0}^{n}a_i\sum\limits_{j=0}^{i}\dbinom{i}{j}x^{\underline{j}}c^{\underline{i-j}}$

$ = \sum\limits_{i=0}^{n}i!a_i\sum\limits_{j=0}^{i}\frac{x^{\underline{j}}}{j!}\frac{c^{\underline{i-j}}}{(i-j)!}$。变换遍历顺序，

$ = \sum\limits_{j=0}^n\frac{x^{\underline{j}}}{j!}\sum\limits_{i=j}^{n}i!a_i\frac{c^{\underline{i-j}}}{(i-j)!}$。后面的式子是卷积的形式，令$g_i = (n-i)!a_{n-i},h_i = \frac{c^{\underline{i}}}{i!}$，有

$ = \sum\limits_{j=0}^n \frac{x^{\underline{j}}}{j!}((g*h)(n-j))$，再做多一次卷积变成**点值**即可。

这里要注意，上界为$n$，上界改成$n-1$，对应函数要做修改。

## 一些习题

### P5667 拉格朗日插值2

用下降幂来做的方法：

点值转系数多项式直接EGF乘$e^{-x}$，加上上述步骤，共三次NTT。

