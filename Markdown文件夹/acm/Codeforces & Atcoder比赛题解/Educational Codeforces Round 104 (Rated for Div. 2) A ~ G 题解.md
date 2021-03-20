# Educational Codeforces Round 104 (Rated for Div. 2) A ~ G 题解

## A. Arena

只要比序列最小的严格大，ans+1。

复杂度 $O(n)$ 。

## B. Cat Cycle

n 为偶数时两猫不会相遇，直接 $O(1)$ 得出答案。

n 为奇数时：可以 $O(1)$ 确定大猫的位置；小猫在大猫的 *循环向左* 位置有循环的关系，也是可以 $O(1)$ 求。故先求出大猫的位置，然后减去相对距离即为小猫位置。

复杂度 $O(1)$ 。

## C. Minimum Ties

由于奇数个人，每一个人参与k=偶数场比赛，故可以安排 k / 2 场败， k / 2 场胜；

偶数个人，则每人参与一场平局赛，然后转成奇数个人的构造问题。

奇数个人，考虑这样构造：

![](https://raw.githubusercontent.com/Falicitas/Image-Hosting/main/N5NEA%40DEX%5B6%7B1L%5BR2JH2CJ6.png)

## D. Pythagorean Triples

$$
\begin{aligned}
& c^2 = a^2 + b^2,c = a^2 - b
\\
\Rightarrow & c^2 - c = b ^ 2 + b
\\
\Rightarrow & (c-b)(c+b) = b+c
\\
\Rightarrow & c = b+1,a^2 = 2b+1
\end{aligned}
$$

看到这个约束关系，可以想到是 $O(\sqrt{n})$ 的枚举方式。

那么就枚举 a ，求出 b,c ，得出 (a,b,c) 满足两个约束，那么当n >= 最大 c 就含该式。即套个二分就完事了。

复杂度 $O(\sqrt{n} + T\log n)$ 。

## E. Cheap Dinner



## F. Ones



## G. String Counting

