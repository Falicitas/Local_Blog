# FFT&NTT

## 总结

后续学习中~~非常~~可能会忘记FFT、NTT原理，但在应用中只需记住：

> **对于一个n阶多项式来说，仅需n+1个不相同的点即可唯一确定（根据牛顿插值的思路）。**
>
> **DFT过程实际在求自变量$x = w_n^0,w_n^1,...,w_n^{lim-1}$的$y_i$值，即求了lim个点值，IDFT则是将lim个点值转成lim个系数值。**
>
> **由上述两个子总结，可得知要「求新多项式G的系数」，就必须保证若干个多项式的乘或加$G = f * h * c + w * \dots $组合起来的阶<lim，也即要有上界lim个$G$的点值才能唯一确定多项式G，不嫌多，只怕少。**
>
> **NTT（包括MTT）不能解决的：系数是浮点数；系数为负数且题目不取模（在取模意义上会出错）；系数大于模数。此时要使用FFT。**ps:使用NTT时先保证多项式相乘时的系数为非负，有WA的教训。。
>
> 多项式乘法运算时，对于系数表示的多项式$f,g$来说，不需要保证运算后的长度**恰好**为两个的度相加，只需要**下一次运算**时，$f,g$所留下的项作乘后不超过**lim**能取到的最大范围。比如在模$x^k$下，可以将运算项数$f,g$的项数**deg = k + 5**，**lim>=2*deg**。运算完后，需将下一次参与运算的$f,g$在$deg$之后的位都清空，不然**lim>=2*deg**的**lim**必不能构造正确的多项式，即其会影响**点值计算**最终影响**系数计算**。

## $e^{i\theta}$

图形上的代表含义为从原点指向单位圆从(1,0)点逆时针绕圆走$\theta$弧度的点，即一个**复数向量**。

复数**相乘**遵循：**模长相乘，幅角相加**的原则。由于复数$z = a[\cos ({\theta})+i \cdot \sin (\theta)] = a\cdot e^{i\theta}$，即$z$由唯一的$(a,\theta)$表示，可利用其来证明系数相乘，指数相加。

## 单位复根

令$\omega_n = e^{\frac{2\pi i}{n}},e^{\frac{2 \pi i}{n}}=\cos \left(\frac{2 \pi}{n}\right)+i \cdot \sin \left(\frac{2 \pi}{n}\right)$（对$e^{i\theta}$泰勒展开，可得到此式）。对于不定方程$x^n=1$的复数领域解可以有$n$个：$\{\omega^k_n|k=0,1,...,n-1\}$，此时称$\omega_n$为n次单位复根。对于任意$(w_n^i)^n = 1$，可以欧拉式子展开后得来。

### 单位复根的性质

$\begin{array}{}
\omega_{n}^{n}=1 \\
\omega_{n}^{k}=\omega_{2 n}^{2 k} \\
\omega_{2 n}^{k+n}=-\omega_{2 n}^{k}
\end{array}$

## FFT

由于n个项的多项式$f(x) = a_0 + a_1x + a_2x^2 + ... + a_{n-1}x^{n-1}$由n个$(\omega_{n}^{0},f(\omega_{n}^{0})),(\omega_{n}^{1},f(\omega_{n}^{1}))...,(\omega_{n}^{n-1},f(\omega_{n}^{n-1}))$点值所确定，故要用上至少n个复数值。FFT的式子需要长度为$2^m$项的多项式，故多项式后面添加形式上的系数0，构成长度为$lim = 2^m$，最高项次数为$2^m-1$（**为什么要添系数0在总结那会写**）。

FFT实际便是由$(a_0,a_1,...,a_{n-1})$来确定n个点值的，朴素的过程单是代值就是$O(n)$了、、要将整个过程优化到$O(nlogn)$。采用分治的思想，将次数分为奇偶两部分，分别对其进行分治，目的是在$O(nlogn)$复杂度下求得所有的点值$(w_n^0,y_0),(w_n^1,y_1),...,(w_n^{lim-1},y_{lim-1})$。

由例子确定递归式：

$f(x)=\left(a_{0}+a_{2} x^{2}+a_{4} x^{4}+a_{6} x^{6}\right)+\left(a_{1} x+a_{3} x^{3}+a_{5} x^{5}+a_{7} x^{7}\right)\\
=\left(a_{0}+a_{2} x^{2}+a_{4} x^{4}+a_{6} x^{6}\right)+x\left(a_{1}+a_{3} x^{2}+a_{5} x^{4}+a_{7} x^{6}\right)$

令$G(x) = \{a_0,a_2,a_4,a_6\},H(x) = \{a_1,a_3,a_5,a_7\}$

则$F(x)=G\left(x^{2}\right)+x \times H\left(x^{2}\right)$

利用单位复根的性质得到

$\begin{aligned}
\operatorname{DFT}\left(f\left(\omega_{n}^{k}\right)\right) &=\operatorname{DFT}\left(G\left(\left(\omega_{n}^{k}\right)^{2}\right)\right)+\omega_{n}^{k} \times \operatorname{DFT}\left(H\left(\left(\omega_{n}^{k}\right)^{2}\right)\right) \\
&=\operatorname{DFT}\left(G\left(\omega_{n}^{2 k}\right)\right)+\omega_{n}^{k} \times \operatorname{DFT}\left(H\left(\omega_{n}^{2 k}\right)\right) \\
&=\operatorname{DFT}\left(G\left(\omega_{n / 2}^{k}\right)\right)+\omega_{n}^{k} \times \operatorname{DFT}\left(H\left(\omega_{n / 2}^{k}\right)\right)
\end{aligned}$
同理可得
$\begin{aligned}
\operatorname{DFT}\left(f\left(\omega_{n}^{k+n / 2}\right)\right) &=\operatorname{DFT}\left(G\left(\omega_{n}^{2 k+n}\right)\right)+\omega_{n}^{k+n / 2} \times \operatorname{DFT}\left(H\left(\omega_{n}^{2 k+n}\right)\right) \\
&=\operatorname{DFT}\left(G\left(\omega_{n}^{2 k}\right)\right)-\omega_{n}^{k} \times \operatorname{DFT}\left(H\left(\omega_{n}^{2 k}\right)\right) \\
&=\operatorname{DFT}\left(G\left(\omega_{n / 2}^{k}\right)\right)-\omega_{n}^{k} \times \operatorname{DFT}\left(H\left(\omega_{n / 2}^{k}\right)\right)
\end{aligned}$

意思直白点就是
> 当递归到n=1时$f(a_i) = a_i$;
> 否则$f[k] = g[k] + \omega_{n}^{k} * h[k],k\in [0,n/2)\\
f[k] = g[k-n/2] + \omega_{n}^{k-n/2} * h[k-n/2],k\in [n/2,n)$

ps:第二个公式通常写成：$f[k+n/2] = g[k] + \omega_{n}^{k} * h[k],k\in [0,n/2)$这样可以跟着上个式子的循环一块走。

## 蝴蝶变换

名字的来源？当尝试去模拟分治的过程：

$\text { 设初始序列为 }\left\{x_{0}, x_{1}, x_{2}, x_{3}, x_{4}, x_{5}, x_{6}, x_{7}\right\}$

$\text { 一次二分之后 }\left\{x_{0}, x_{2}, x_{4}, x_{6}\right\},\left\{x_{1}, x_{3}, x_{5}, x_{7}\right\}$

$\text { 两次二分之后 }\left\{x_{0}, x_{4}\right\}\left\{x_{2}, x_{6}\right\},\left\{x_{1}, x_{3}\right\},\left\{x_{5}, x_{7}\right\}$

$\text { 三次二分之后 }\left\{x_{0}\right\}\left\{x_{4}\right\}\left\{x_{2}\right\}\left\{x_{6}\right\}\left\{x_{1}\right\}\left\{x_{5}\right\}\left\{x_{3}\right\}\left\{x_{7}\right\}$


会发现系数的坐标变换是有规律的，即更改后的位置跟更改前的位置在长度为len，二进制表示下照中心对称。此时将递归的式子改成循环，模拟分治的反过程，完成计算的操作。原理与递归版的完全一样，节约了递归产生的额外空间占用。

## IDFT

没有深入研究，其实就是将运算中的pi 取相反数，结尾除个n就是IDFT。。。没太大兴趣看。。。（NTT还没看呢）

## FFT模板

```cpp
#include <cmath>
#include <cstdio>
#include <cstring>
#include <iostream>

const double PI = acos(-1.0);
struct Complex {
  double x, y;
  Complex(double _x = 0.0, double _y = 0.0) {
    x = _x;
    y = _y;
  }
  Complex operator-(const Complex &b) const {
    return Complex(x - b.x, y - b.y);
  }
  Complex operator+(const Complex &b) const {
    return Complex(x + b.x, y + b.y);
  }
  Complex operator*(const Complex &b) const {
    return Complex(x * b.x - y * b.y, x * b.y + y * b.x);
  }
};
/*
 * 进行 FFT 和 IFFT 前的反置变换
 * 位置 i 和 i 的二进制反转后的位置互换
 *len 必须为 2 的幂
 */
void change(Complex y[], int len) {
  int i, j, k;
  for (int i = 1, j = len / 2; i < len - 1; i++) {
    if (i < j) swap(y[i], y[j]);
    // 交换互为小标反转的元素，i<j 保证交换一次
    // i 做正常的 + 1，j 做反转类型的 + 1，始终保持 i 和 j 是反转的
    k = len / 2;
    while (j >= k) {
      j = j - k;
      k = k / 2;
    }
    if (j < k) j += k;
  }
}
/*
 * 做 FFT
 *len 必须是 2^k 形式
 *on == 1 时是 DFT，on == -1 时是 IDFT
 */
void fft(Complex y[], int len, int on) {
  change(y, len);
  for (int h = 2; h <= len; h <<= 1) {
    Complex wn(cos(2 * PI / h), sin(on * 2 * PI / h));
    for (int j = 0; j < len; j += h) {
      Complex w(1, 0);
      for (int k = j; k < j + h / 2; k++) {
        Complex u = y[k];
        Complex t = w * y[k + h / 2];
        y[k] = u + t;
        y[k + h / 2] = u - t;
        w = w * wn;
      }
    }
  }
  if (on == -1) {
    for (int i = 0; i < len; i++) {
      y[i].x /= len;
    }
  }
}

const int MAXN = 200020;
Complex x1[MAXN], x2[MAXN];
char str1[MAXN / 2], str2[MAXN / 2];
int sum[MAXN];

int main() {
  while (scanf("%s%s", str1, str2) == 2) {
    int len1 = strlen(str1);
    int len2 = strlen(str2);
    int len = 1;
    while (len < len1 * 2 || len < len2 * 2) len <<= 1;
    for (int i = 0; i < len1; i++) x1[i] = Complex(str1[len1 - 1 - i] - '0', 0);
    for (int i = len1; i < len; i++) x1[i] = Complex(0, 0);
    for (int i = 0; i < len2; i++) x2[i] = Complex(str2[len2 - 1 - i] - '0', 0);
    for (int i = len2; i < len; i++) x2[i] = Complex(0, 0);
    fft(x1, len, 1);
    fft(x2, len, 1);
    for (int i = 0; i < len; i++) x1[i] = x1[i] * x2[i];
    fft(x1, len, -1);
    for (int i = 0; i < len; i++) sum[i] = int(x1[i].x + 0.5);
    for (int i = 0; i < len; i++) {
      sum[i + 1] += sum[i] / 10;
      sum[i] %= 10;
    }
    len = len1 + len2 - 1;
    while (sum[len] == 0 && len > 0) len--;
    for (int i = len; i >= 0; i--) printf("%c", sum[i] + '0');
    printf("\n");
  }
  return 0;
}
```



## NTT

### 群

单独有一篇博客有写。

### 原根

单独由一篇博客有写。

### NTT

由于原根的性质跟单位复根同构，所以在某些取模条件下能用原根代替单位复根。下面的$n$代表的是FFT里的$n$，是不小于多项式的长度的的大小为2^m的一个数。

用于NTT的质数$p$可以写成$p = qn + 1$，常见的模数p相关信息：

> $\begin{array}{l}
p=1004535809=479 \times 2^{21}+1, g=3 \\
p=998244353=7 \times 17 \times 2^{23}+1, g=3
\end{array}$

对NTT处理多项式长度和模数的关系很模糊，，所以准备了两个板子：

当模数为998244353,1004535809,469762049，用通常情况模数NTT：

```cpp
#define g 3//模数的原根
#define mod 998244353//通常情况下的模数。另外两个1004535809，469762049

int qp(ll base,ll n)//快速幂
{
	ll res = 1;
	while(n){
        if(n&1) (res *= base) %= mod;
        (base *= base) %= mod;
        n<<=1;
    }
    return (int)res;
}

void change(int a[],int len)
{
    int bit=0;
	while ((1<<bit)<len)++bit;
	REP(i,0,len-1)
	{
		rev[i]=(rev[i>>1]>>1)|((i&1)<<(bit-1));
		if (i<rev[i])swap(a[i],a[rev[i]]);
	}//前面和FFT一样
}

void ntt(int a[],int len,int inv)
{
	change(a,len);
	for (int mid=1;mid<len;mid*=2)
	{
		int tmp=qp(g,(mod-1)/(mid*2));//原根代替单位根
		if (inv==-1)tmp=qp(tmp,mod-2);//逆变换则乘上逆元
		for (int i=0;i<len;i+=mid*2)
		{
			int omega=1;
			for (ll j=0;j<mid;++j,omega=omega*tmp%mod)
			{
				int x=a[i+j],y=omega*a[i+j+mid]%mod;
				a[i+j]=(x+y)%mod,a[i+j+mid]=(x-y+mod)%mod;//注意取模
			}
		}//大体和FFT差不多
	}
}

```

以及任意模数NTT:

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
int mod;
namespace Math {
	int qp(ll base, ll n, const int mod) {
		ll res = 1;
		while(n){
            if(n&1) (res *= base) %= mod;
            (base *= base) %= mod;
            n>>=1;
		}
		return (int)res;
	}
	int inv(int x,const int mod) { return qp(x, mod - 2, mod); }
}

const int mod1 = 998244353, mod2 = 1004535809, mod3 = 469762049, G = 3;
const ll mod_1_2 = 1LL * mod1 * mod2;
const int inv_1 = Math::inv(mod1, mod2), inv_2 = Math::inv(mod_1_2 % mod3, mod3);
struct Int {
	int A, B, C;
	Int() { }
	Int(int __num) : A(__num), B(__num), C(__num) { }
	Int(int __A, int __B, int __C) : A(__A), B(__B), C(__C) { }
	static Int reduce(const Int &x) {
		return Int(x.A + (x.A >> 31 & mod1), x.B + (x.B >> 31 & mod2), x.C + (x.C >> 31 & mod3));
	}
	friend Int operator + (const Int &lhs, const Int &rhs) {
		return reduce(Int(lhs.A + rhs.A - mod1, lhs.B + rhs.B - mod2, lhs.C + rhs.C - mod3));
	}
    friend Int operator - (const Int &lhs, const Int &rhs) {
		return reduce(Int(lhs.A - rhs.A, lhs.B - rhs.B, lhs.C - rhs.C));
	}
    friend Int operator * (const Int &lhs, const Int &rhs) {
		return Int(1LL * lhs.A * rhs.A % mod1, 1LL * lhs.B * rhs.B % mod2, 1LL * lhs.C * rhs.C % mod3);
	}
    int get() {
		ll x = 1LL * (B - A + mod2) % mod2 * inv_1 % mod2 * mod1 + A;
		return (1LL * (C - x % mod3 + mod3) % mod3 * inv_2 % mod3 * (mod_1_2 % mod) % mod + x) % mod;
	}
} ;

#define maxn 131072

namespace Poly {
#define N (maxn << 1)
	int lim, s, rev[N];
	Int Wn[N | 1];
    void init(int n) {
		s = -1, lim = 1; while(lim < n) lim <<= 1, ++s;
		for (int i = 1; i < lim; ++i) rev[i] = rev[i >> 1] >> 1 | (i & 1) << s;
		const Int t(Math::qp(G, (mod1 - 1) / lim, mod1), Math::qp(G, (mod2 - 1) / lim, mod2), Math::qp(G, (mod3 - 1) / lim, mod3));
		*Wn = Int(1); for (Int *i = Wn; i != Wn + lim; ++i) *(i + 1) = *i * t;
	}
    void NTT(Int *A, int op = 1) {
		for (int i = 1; i < lim; ++i) if (i < rev[i]) swap(A[i], A[rev[i]]);
		for (int mid = 1; mid < lim; mid <<= 1) {
			int t = lim / mid >> 1;
			for (int i = 0; i < lim; i += mid << 1) {
				for (int j = 0; j < mid; ++j) {
					Int W = op ? Wn[t * j] : Wn[lim - t * j];
					Int X = A[i + j], Y = A[i + j + mid] * W;
					A[i + j] = X + Y, A[i + j + mid] = X - Y;
				}
			}
		}
		if (!op) {
			Int ilim(Math::inv(lim, mod1), Math::inv(lim, mod2), Math::inv(lim, mod3));
			for (Int *i = A; i != A + lim; ++i) *i = (*i) * ilim;
		}
	}
#undef N
}

int n, m;
Int A[maxn << 1], B[maxn << 1];
int main() {
	scanf("%d%d%d", &n, &m, &mod); ++n, ++m;//n,m是多项式最高次数
	for (int i = 0, x; i < n; ++i) scanf("%d", &x), A[i] = Int(x % mod);
	for (int i = 0, x; i < m; ++i) scanf("%d", &x), B[i] = Int(x % mod);
	Poly::init(n + m);
	Poly::NTT(A), Poly::NTT(B);
	for (int i = 0; i < Poly::lim; ++i) A[i] = A[i] * B[i];
	Poly::NTT(A, 0);
	for (int i = 0; i < n + m - 1; ++i) {
		printf("%d", A[i].get());
		putchar(i == n + m - 2 ? '\n' : ' ');
	}
	return 0;
}

```

上述数论变换 (NTT) 公式中, 要求 $P$是素数且$ N$必须是$ P-1$的因子。由于$N$经常等于2的幂，所以可以构造形
如 $P=c \cdot 2^{k}+1$的素数。通常来说可以选择$P$为费马素数, 这样的变换叫做费马数数论变换。

所以能否用通常情况下的NTT，一是看$P=c \cdot 2^{k}+1$中$2^k$是否大于多项式乘法的长度，只要长度足够就可以用该模数$P$。原根的话就用求原根的程序来求。

**在没学透NTT之前，就将题目模数当做NTT的工具兼取模的数，非常规的模数就用MTT。**

## 总结

数论是个圈，学其他知识的时候说不定就把关于原根和元素的阶相关的原理给证明了。在取模系中理解取余除法等的原理，之后的之后再学吧。

