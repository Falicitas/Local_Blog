# FHQ-treap

Treap（笛卡尔树）=BST（二叉搜索树）+Heap（堆）。一种不需要自旋，靠分裂和合并来尽可能贴近平衡的DS。

结构的基本还是BST树，不过当给每个点随机打上了rnk标记，跟据这个rnk标记将该树构建成小根堆的结构（即左右两子树的rnk都比根节点的大）。

由于同时满足BST和堆的性质， **Treap的结构是一定的** 。对于一棵树，rnk最小的当做根节点，此时左右两边的rnd都比根节点大。同时左子树所有元素的val比根小，右子树所有元素的val比根大，那么便将除根节点的节点划分成两部分。每一部分取最小的rnd当做对应根节点的左右儿子，故树结构确定。

由于rnk随机，故树高期望为$O(\log n)$。

给出快速随机函数：

```cpp
int rnd()
{
    static int seed = 233;
    return seed = (int) seed * 482711LL % 2147483647;
}
```

## 分裂

由于BST的中序遍历性质（见splay「二叉树」），每个节点对应的子树可以视作一个区间$[l,r]$。

关于分裂，见图：![](C:\Users\Administrator\Desktop\markdown图片\FHQ-treap1.png)

在如何理解这个区间上，对应着两种分裂方式：

将区间视作val值的值域。一般用于处理集合里的数。代码：

```cpp
void split(int now,int k,int &x,int &y){
    if(!now) return (void)(x = y = 0);
    if(tree[now].val<=k) x=now,split(tree[now].rs,k,tree[now].rs,y);
    else y=now,split(tree[now].ls,k,x,tree[now].ls);
    up(now);
}
```

将区间视作顺序排名。一般用于序列。代码：

```cpp
void split(int now,int k,int &x,int &y)//对于空树算是有特判的
{
    if(!now) return (void)(x = y = 0);
    if(T[T[now].ch[0]].siz>=k) y = now,split(T[now].ch[0],k,x,T[now].ch[0]);
    else x = now,split(T[now].ch[1],k-T[T[now].ch[0]].siz-1,T[now].ch[1],y);
    up(now);
}
```

上传的操作跟线段树类似。

如果要分裂区间$[l,r]$中的$[ql,qr]$，可以先将$[l,qr],[qr+1,r]$分出来，再分$[l,ql-1],[ql,qr]$。

分裂出来的两颗树也是Treap，即它们的结构也唯一确定。

## 合并

对于要合并的两个树$x,y$（这里假定$val_x<val_y$），既可以让y接到x的右子树，也可以将x接到y的左子树。但要保证Treap的 **堆** 的性质，merge过程需按rnk来确定谁做父节点。

![](C:\Users\Administrator\Desktop\markdown图片\FHQ-treap2.png)

代码：

```cpp
int merge(int u,int v)
{
    if(!u||!v) return u|v;//如果有任意一个树为空就返回非空的一棵的根
    if(T[u].rnd<T[v].rnd){//如果u为当前根
        tree[u].rs=merge(T[u].rs,v);//继续合并
        up(u);//上传
        return u;//返回根
    }
    else{//如果v为当前根
        T[v].ls=merge(u,T[v].ls);
        up(v);
        return v;
    }
}
```

## 插入

插入数据时：

如果是按val进行排序（对于按排名排序的插入操作，与这没有差别），则将树split成$[l,val],[val+1,r]$两个区间，然后让val和左区间合并，再和右区间合并。代码：

```cpp
int newnode(int s)
{
    int u = nw();//这里用了内存池
    T[u].key = rnd(),T[u].fa = 0,T[u].siz = 1;
    //...
    return u;
}

void insert(int s,int k)
{
    split(root,k,r1,r2);
    root = merge(merge(r1,newnode(s)),r2);
}
```

一般来说，合并时要保证两个区间不交叉。但对于插入单个节点（比如insert的$[l,val],[val,val]$），由于节点被一直下放到右子树（直到rnk小于剩余节点），就结果而言是没问题的。

自己实现的无旋treap无合并相同val的节点。由于merge和split很方便，不需要合并就可以轻松统计同val的个数。

## 删除

将val值孤立出来，然后合并val的左右子树（即不要根节点，相当于去掉了一个值）。代码：

```cpp
void erase(int s)
{
    split(root,s,r1,r3);
    split(r1,s-1,r1,r2);
    root = merge(r1,r3);
    del(r2);
}
```

## 查找

### 查找指定排名的数

根据左子树的大小来判即可。

```cpp
int kth(int k)
{
    int now = root;
    while(233){
        if(T[T[now].ch[0]].siz>=k) now = T[now].ch[0];
        else if(k==T[T[now].ch[0]].siz+1) return now;
        else k -= T[T[now].ch[0]].siz+1,now = T[now].ch[1];
    }
}
```



### 查找某个数/节点的排名

这里需分是按排名分裂还是按权值分裂。具体的：

> 当按权值分裂，很简单，只需要将[l,val-1]的区间分裂出来，统计子树大小即可。
>
> 当按排名分裂，由于插入，删除等操作，不能知道某节点当前的排名。这时则用 **回溯法** 来确定当前节点的排名。

具体回溯法的实现：

```cpp
int findsiz(int now)
{
    if(!root) return 0;
    int res = T[now].siz - T[T[now].ch[1]].siz;
    while(now!=root){
        if(now==T[T[now].fa].ch[1]) res += T[T[now].fa].siz - T[now].siz;
        now = T[now].fa;
    }
    return res;
}
```

<img src="C:\Users\Administrator\Desktop\markdown图片\FHQ-treap3.png" style="zoom:67%;" />

结合图片理解，即当当前节点向上回溯时，左拐时根节点及其左子树比当前节点的排名小，则此时统计进答案里。

> 按排名分裂的Treap，实际上不需要存当前节点的排名作为val（由于也维护不了），所以才需要采取回溯法。而对于按val分裂的Treap，则可以分出小于val的树进行大小统计。两者的差别需细致体会。

## 前驱、后驱

仅举val分裂的情况（按排名的不难写）：

```cpp
int findpre(int a){
    split(root,a-1,x,y);//分裂为(l,queryval-1)与(queryval,r)
    int tmp=tree[findkth(x,tree[x].siz)].val;//分出来的树的最右边
    root=merge(x,y);
    return tmp;
}

int findaft(int a){
    split(root,a,x,y);//分裂为(l,queryval)与(queryval+1,r)
    int tmp=tree[findkth(y,1)].val;/分出来的树的最左边
    root=merge(x,y);
    return tmp;
}
```

## 一些习题

### P2042 [NOI2005]维护数列

一些编程技巧：

将区间修改&区间翻转单独写成一个函数，对完整块、该下放的左区间右区间直接调用函数。

以及建树时用分治来$O(n)$建树。

### P3586 [POI2015]LOG

关注操作序列：操作s次，每次选出c个两两不相同的位置将其减1、、对于值>=s的位置，可以放入s个操作序列里。对于值val<s的位置则可以全部放入val个操作序列中。则可以写出有解的条件：$sum>=(c-t)*s$，$sum$为序列中<s的值的和，$t$为>=s的值的个数。写个平衡树维护值，$O(\log n)$找<s的个数即可。

### P4309 [TJOI2013]最长上升子序列

这个题啊，要注意 **插入的数是从1~n顺序插入的** ，即当前插入的数一定是区间最大的数。

离线的做法：

由于后插入的数都比当前的大，若考虑用树状数组取值域前的max来做LIS，可以知道后插入的数对当前位置的数的答案统计无影响。故用rope先构造出序列，计算出每个数在当前序列的最大值后，做个前缀max即可。还想不通，就比如n=20，当当前i=10的时候，把其他数忽略掉，求出ans[1~10]对应的LIS后，取max即是原构造的答案。

在线的做法：

split左右区间，当前插入的点的dp值为左区间的max{dp}+1。push_up的时机在于树发生改变后，故其实就是裸的平衡树。