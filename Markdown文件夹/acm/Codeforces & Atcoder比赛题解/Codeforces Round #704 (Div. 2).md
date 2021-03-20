# Codeforces Round #704 (Div. 2) A ~ E 题解

## A. Three swimmers

分别取模后取 $\min(a-p,b-p,c-p)$ 。

## B. Card Deck

观察公式，第 i 位的数的权值比 i+1,i+2,...,n 的权值和大，所以优先放序列最大的在前头。

记录序列前缀最大值及最大值位置，跳指针输出即可。

复杂度 $O(n)$ 。

## C. Maximum width

找在文本串匹配的最大间距。

对于每位，先从左到右，能匹配上的就匹配上（即往左“挤”着匹配），对于模式串记录每个位置最左能在文本串的 l[i] 位置匹配。从右到左，相似的得到 r[i] 。

容易感性证明，最大间距即为 $\max(r[i+1] - l[i])$ 。

复杂度 $O(n)$ 。

## D. Genius's Gambit

一开始的思路容易想到诸如 1000 - 0001 的字符 “1” + 1 的构造形式。

具体的，按照图例构造：

![](https://raw.githubusercontent.com/Falicitas/Image-Hosting/main/%7DFSHUO8%40Q59OKL%7BVUULCR(4.png)

也就是，当 $k > a + b - 2$ 就不存在构造方案。

处理一下细节即可。

复杂度 $O(a+b)$ 。

## E. Almost Fault-Tolerant Database

很有意思的匹配问题。

由于能容纳至多 2 个不匹配的位置，那么可以猜测更换答案串的次数是平凡的。

存在与第一个串超过 4 个位置不同的必然不存在答案（怎么换都换不来）。

那么找到一个与第一个串相差 3 - 4 个位置的串，将答案串的部分位更换。再搜索。

总复杂度为 $O(Cnm),C\leq 30$ 。

