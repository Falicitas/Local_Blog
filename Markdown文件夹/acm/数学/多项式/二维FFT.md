# 二维FFT

计算$f(x,y) * g(x,y)$。

采用FFT的思路：

DFT：分别取$x$奇$y$奇$f_0(x,y)$，$x$奇$y$偶$f_1(x,y)$，$x$偶$y$奇$f_2(x,y)$，$x$偶$y$偶$f_3(x,y)$。有

$f(x,y) = f_0(x^2,y^2) + xf_1(x^2,y^2) + yf_2(x^2,y^2) + xyf_3(x^2,y^2)$

递归求点值，有$f(w_n^i,w_n^j) = \dots$（略，这里默认$n = 2^k$）

IDFT：带入共轭单位根，直觉告诉我应该是除$\frac{1}{n^2}$。（当然也是可以通过IDFT的式子计算得到的）

复杂度$T(n,n) = 4T(\frac{n}{2},\frac{n}{2}) + n^2\Rightarrow O(n^2log_2n^2)$

