# 背包dp-dp的经典设计

    重拾背包问题  

[toc]

---

## 01背包

经典转移方程：$f[i][v] = max(f[i-1][v],f[i-1][v-c_i]+w_i)$

反向费用循环省空间为：$f[v] = max(f[v],f[v-c_i]+w_i)$

还有个常数优化，$\begin{aligned}
&\text { for } i \leftarrow 1 \text { to } N\\
&\qquad \text { for } v \leftarrow V \text { to } \max \left(V-\Sigma_{i}^{N} C_{i}, C_{i}\right)
\end{aligned}$

背包空间大适用，不过这个不适合恰好装满的问题。

## 完全背包

由于每个物品可以做$O(n)$的决策，复杂度是$O(n^2V)$的。

首先每种物品至多选$\lfloor \frac{V}{C_i}\rfloor$，利用二进制的思想，将$C_i$分成$1,2,4,...,2^m,C_i-2^{m+1}+1$，复杂度优化到$O(nlognV)$

然鹅有$O(VN)$的做法：$\begin{array}{}
F[0 . . V] \leftarrow 0 \\
\text { for } i \leftarrow 1 \text { to } N \\
\qquad \begin{aligned}
\text { for } v & \leftarrow C_{i} \text { to } V \\
& F[v] \leftarrow \max \left(F[v], F\left[v-C_{i}\right]+W_{i}\right)
\end{aligned}
\end{array}$
这个要用到一维数组，原因是物品可以从已经选过自己的状态中转移过去。

## 多重背包

这个的代码实现有点有趣。由于不能超过k件同样的物品，故可以：

若$k>=V/C_i$，则直接看成完全背包；

反之，用二进制优化，转成01背包解决。

这个复杂度是$O(VNlog(lim)),lim = min(V/c[i],k[i]) \sim O(min(V,K))$

但可以优化到$O(NV)$。

回到多重背包的原理：$f[i][j] = f[i-1][j-t*c[i]] + t*w[i],j>=t*c[i],t<=k[i]$，容易发现$f[i][j]$可以做的决策来自$f[i-1][j-t*c[i]]$。表达式的值只跟决策t有关，满足单调队列的性质。故转移方程便改做$0~c[i]-1$的状态j按c[i]递增去转移，用单调队列维护即可。

单调队列蛮好写的，这里就不贴代码了、、记得清空。

## 可行性问题

01背包和完全背包等价为多重背包的特例，故只讨论多重背包的可行性问题。给n种物品，每个物品有k个，能否恰好填满空间为V的背包呢？（这里就不考虑物品的价值了）

复杂度是$O(nV)$的。

定义状态$dp(i,j)$为选第i个物品，已用j空间时还（剩余）可选第i号物品的最多的数量。

方程很好写：

initialization step: $dp[][]$ < - $-1,dp[0][0] = 0;$

``` cpp
for j = 0 to V 
    if(f[i-1][j]>=0)
    f[i][j] = k[i]
for j = 0 to V - c[i]
    if(f[i][j]>0){
           f[i][j+c[i]] = max(f[i][j+c[i]],f[i][j]-1);
    }
```

## 混合背包

如下

$\begin{array}{}
for \quad i \leftarrow 1 to N\\
\qquad if\ 第 i 件物品属于 01 背包 \\
\qquad\qquad    ZeroOnePack \left(F, C_{i}, W_{i}\right)\\
\qquad else\ if\ 第 i 件物品属手完全背包 \\
\qquad\qquad    CompletePack \left(F, C_{i}, W_{i}\right)\\
\qquad else\ if\ 第 i 件物品属手多重背包 \\
\qquad\qquad    MultiplePack \left(F, C_{i}, W_{i}, N_{i}\right)\\
\end{array}$

混合背包的多重背包就不考虑$O(VN)$做法了。。巨麻烦。

## 三种基础背包及可行性背包&混合背包代码

```cpp
void zero_one_pack()
{
    REP(i,1,n){
        _REP(j,V,c[i]){
            f[j] = max(f[j],f[j-c[i]]+w[i]);
        }
    }
}

//complete pack

void complete_pack()
{
    REP(i,1,n){
        REP(j,c[i],V){
            f[j] = max(f[j],f[j-c[i]]+w[i]);
        }
    }
}

//multiple pack

void multiple_pack()//if want to cut logM, use monotonic queue instead of it. The column note it.
{
    REP(i,1,n){
        if(k[i]>=V/c[i]){
            REP(j,c[i],V){
                f[j] = max(f[j],f[j-c[i]]+w[i]);
            }
            return ;
        }
        int t = 1,M = k[i];
        while(t<M){
            _REP(j,V,t*c[i]){
                f[j] = max(f[j],f[j-t*c[i]]+t*w[i]);
            }
            M -= t;
            t<<=1;
        }
        _REP(j,V,M*c[i]){
            f[j] = max(f[j],f[j-M*c[i]]+M*w[i]);
        }
    }
}

//judge if exact V can reach in multiple pack situation

void available_volume()
{
    memset(f,-1,sizeof f);
    f[0][0] = 0;
    REP(i,1,n){
        REP(j,0,V){
            if(f[i-1][j]>=0)
                f[i][j] = k[i];
        REP(j,0,V-c[i])
            if(f[i][j]>0)
                f[i][j+c[i]] = max(f[i][j+c[i]],f[i][j]-1);
    }
}


//mixed pack

void mixed_pack()
{
    //make some modification from up context
    REP(i,1,n){
        if(01) 01();
        else if(complete) complete();
        else multiple();
    }
}
```

## 二维费用

一个物品产生两方面的费用，记为$f(i,j,k)$。$f(i,j,k) = max(f(i-1,j,k),f(i-1,j-c1[i],k-c2[i])+w[i])$根据一维背包的思想，可以将第一维优化。

### 例题1

一维背包问题中，最后不能拿超过R个物品。这时候可以把每个物品看做费用为$C1_{i}=1$的物品，最后就能保证只拿R个物品了。

## 分组的背包问题

基于01背包的原理，每组物品只选一个or不选，写出转移方程：

$f[k][j] = max(f[k-1][j-c[i]]+w[i]),i\in 第k组的元素$

省空间的话：

最内循环01背包，第二层放倒序体积，最外层是按组循环。

是01背包的应用，照着01背包的思路就可以很快推出这个做法。

复杂度为$O(nV)$，由于将所有组的元素加起来才$n$个。

```cpp
vector<int> cluster[maxk];//don't add the pack whose volume is over V.
void cluster_pack()
{
    REP(i,1,k){ //1~k cluster
        REP(j,V,0){
            for(int t=0;t<cluster[i].size();t++){
                k0 = cluster[i][t];
                if(j>=c[k0]){
                    f[j] = max(f[j],f[j-c[k0]] + w[k0]);
                }
            }
        }
    }
}
```

### 优化

由于分组背包中每一组至多选1个，那么就可以像无限背包那样优化选择：当物品i,j满足：$c_i<=c_j,w_i>=c_j$，则选i比选j好。利用计数排序的原理，在长度为V的数组上插值，同个$c_i$的物品保留一个价值最高的。这时的计数数组上只需留下满足二元偏序的元素：$c_i<=c_j,w_i<=c_j$。由于计数数组上默认$c_i$有序，将后面的价值w大于等于前面的放入新组里就行了。（数据若按二维偏序来出的话，就一点优化都没有了。。）

```cpp
vector<int> cluster[maxk];//don't add the pack whose volume is over V.
int cntarr[maxV];
void select(int p)
{
    memset(cntarr,0,sizeof cntarr);
    w[0] = -1;
    for(int i=0;i<cluster[p].size();i++){
        int pac = cluster[p][i];
        cntarr[c[pac]] = (w[cntarr[c[pac]]] < w[pac] ? pac : cntarr[c[pac]);
    }
    cluster[p].clear();
    int idx = 0;
    REP(i,0,V-1){
        if(w[cntarr[i]]>w[idx]){
            cluster[p].push_back(cntarr[i]);
            idx = cntarr[i];
        }
    }
}
```

## 有依赖的背包问题

现在有一些物品，物品间可能存在依赖关系：选物品i，必须选物品j。先给出简化版：若物品i依赖物品j，则物品i不再依赖其他物品；物品依赖的层数只有1，即不存在链式依赖。在树结构上就是有根树的子树大小均为1。

给物品做简单的定义：出度为0的物品为“主件”，出度为1的物品为附件。主件间的附件没有交集。

先对第i个主件进行分析：

> 1. 全不选 $1$ 种方案（直接不考虑）
> 2. 选主件不选附件 $1$ 种方案 
> 3. 选主件选附件 $2^n$ 种方案，n为附件个数。

可以发现对于第i个主件来说，上面的方案两两互斥。也就是对于k个主件来说构成了k组背包，复杂度为$O(k2^n)$。

每一个主件中方案的选择，只关心占用空间V的最大价值是多少，附件也是至多选一个，故对主件i与对应附件集做01背包，$f[0]\sim f[c[主件]-1] = -1, f[c[主件]]=w[主件]$，附件就照01背包去做就好了。最后将每一组的$(f[c[主件]~V],c[主件]~V)$加入到一个新的数组代表第i组可选的方案，转变成分组背包来做。复杂度为$O(nV^2)$（大概）。

### 依赖再一般化

每个物品至多依赖另一个物品不变，而附件也可以被其他物品依赖。这样便构成一棵树。这里不建议用上面的01背包做法。dd大佬先对附件进行 **分组背包** ，范围是父节点i对应的$c[i],f[0\to V-c[i]]$。附件的 **分组背包** 做完后，将决策$(v,f[v-c[i]]+w[i]),v\in [c[i],V]$加入新数组vector中，返回给i的父节点。树与树之间也是 **分组背包** 。（复杂度其实跟节点个数有关，不算高）。最好连个虚点，这样就是一棵树了。

### O(nV)的树形背包

首先是一棵树。

对于一棵树求dfs序，采用刷表的形式来dp。

预处理一下$pre(u)$，代表u节点以上的父节点（既不包括u的到根节点的链）的费用之和。具体的，`pre[v] = pre[fa]+w[fa] `。

$dp(i+1,j+w(u)) = \max(dp(i+1,j+w(u)),dp(i,j)+v(u)),u = rnk(i),j\geq pre(u)$，对于选当前节点的转移方程。由于至少祖先节点的所有点都得选，所以$j\geq pre(u)$。

$dp(i+siz(u),j) = \max(dp(i+siz(u),j),dp(i,j))$。表示不选当前节点，那么子树也必然不选。

## 泛化物品

这里是对物品的一般化：对于一个泛化物品，给定一个费用v，就对应一个价值w。写成映射就是:$h[v]=w_v,v\in [0,V]$。只是将上面的思路抽象出来而已，掌握上面原理更重要。

## 输出方案

基础的操作。对于估值函数$f(i,j,k)$，多创建一个$cho(i,j,k)$，记录更新$(i,j,k)$状态的$(i',j',k')$。字典序最小的话便考虑倒着求（指动态规划从$n\sim1$,由1往n回溯）。有些小细节，比如倒着求时不选当前的最优值==选当前的最优值，那需由选当前的来更新（因为当前的下标更小）。

## 最优方案的总数

用两个数组来记录。$f[i][j]$为最优方案，$g[i][j]$为最优方案的路径数。很明显，$f[i][j]$由谁更新，$g[i][j]$就加谁。

## 第k优解

原理跟图论有关。假设一个点可以从两个点出发到达，那么这个点的路径方案就是两个点方案的总和。第k优解将每个点的状态存储的解视为一个有序数组，若一个点可以从两个决策中到达，那么这个点的方案就是两个决策的有序数组的合并。由归并排序的思路可知，合并的复杂度是$O(k)$的。故对于估价函数f，只需加一维长度为k，合并时取可做决策的前k大/前k小即可。

## 完结

这篇算是专题。写的过程能感觉到知识范围很广，单纯写cpp文件记录模板不太行。还是专题和cpp文件两份都贴上代码，出去比赛时可以直接印。当然一些散装知识直接印cpp就可。

回看有疑问的话，就看背包九讲pdf把，已下。

$\begin{array}{}
for(i,1,n) \\
\quad for(j,1,n) \\
\qquad for(k,1,n)
\end{array}$

就是枚举最大



