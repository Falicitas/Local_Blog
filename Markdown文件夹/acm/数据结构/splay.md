# Splay

一种不需要随时树平衡但能保证单步操作期望为$O(\log n)$的平衡树。

普通的BST，其实对于随机插入查询的数据来说，单次操作期望是$O(\log n)$的。在插入 **带趋势的数据** 才会被卡成单次操作$O(n)$。为了预防这种数据，在每次插入（查询）数据时将该数据扔到根节点上，可以发现完全顺序的数据甚至能达到$O(1)$的复杂度。故对于何种数据，期望复杂度为$O(\log n)$，但常数大。

## 二叉树、插入与查询

Splay的结构是BST，故满足BST的性质，即每个节点的左子树任意节点不大于根节点，右子树任意节点不小于根节点。同时对于一个二叉树， **中序遍历** 时是一个有序数列。

查询与插入时根据插入数的权值来移动遍历指针。在splay树中，每次插入查询后将数旋至根节点，以达到防止被 **带趋势的数据** 卡（比如顺序添加1,2,3,4,1000,1001,1002,1003，此时将1000放到根节点时，插入1001只需要比较1次）

## rotate、splay

为保证树尽量的平衡，加入了rotate操作。

rotate操作在保证树还是BST的前提下（一个节点至多两子树，中序遍历不发生改变）使树的高尽量减少差距。对于旋转节点x，x的父亲y，y的父亲z，三点的位置关系共四种。

这时候可以用异或来优化操作。具体的，令`w`为x,y的关系。此时可以发现rotate时，x将y放置`w^1`的位置，x的`w^1`位置的子树放到y的`w`位置，而z与x的关系同之前的z与y的关系。操作一下就旋转成功了。

splay是种操作，一般带两个参数splay(u,to)表示将u最终旋到to的子节点上。例如当to=0，就是把u旋到根节点上。

splay采用双旋操作。在普通的rotate过程中，仅将编号x旋至根节点可能并不会优化树的平衡。对于 **三点共线** 的情况，通常先旋一次父节点，再旋x点来保证树的尽量平衡。

代码为：

```cpp
void upd(int x)
{
    if(x){//边界情况，不让节点0有任何信息
        T[x].siz = T[x].cnt;
        if(T[x].ch[0]) T[x].siz += T[T[x].ch[0]].siz;
        if(T[x].ch[1]) T[x].siz += T[T[x].ch[1]].siz;
    }
}

void rotate(int x)
{
	int y = T[x].fa,z = T[y].fa,w = (T[y].ch[1] == x);
	if(z) T[z].ch[T[z].ch[1] == y] = x;T[x].fa = z;//x->z.边界情况特判0节点
	T[y].ch[w] = T[x].ch[w ^ 1],T[T[x].ch[w ^ 1]].fa = y;//son_x->y
	T[x].ch[w ^ 1] = y,T[y].fa = x;//y->x
	upd(y),upd(x);//旋和被旋的点
}

void splay(int x,int goal)
{
	while(T[x].fa != goal){
		int y = T[x].fa,z = T[y].fa;
		if (z != goal)//如果z是goal，单次操作只用单旋，x的父亲就是goal了。
            ((T[y].ch[1] == x) ^ (T[z].ch[1] == y)) ? rotate(x) : rotate(y);
        rotate(x);
	}
	if(!goal) root = x;
}
```

> 由于只有splay会调用rotate，而splay会判别当前节点u是否该旋。故在rotate里不用加被旋父节点是否为0的特判。在rotate里要注意的是z可能为0。由于要让一切编号为0的虚点（后面会讲，包括根节点的父节点，以及未满节点的空指针指向的点）不存任何信息，此处特判防止z有子节点。

## 前驱、后继

对于数x，找到后将其放到根节点上。此时先往左走一格然后贪心往右走可得小于x的最大值节点（前驱）；先往右走一格然后贪心往左走可得大于x的最小值节点（后继）。

> 如果没有x值：
>
> 当有小于x值的节点，会将其小于x值的最大值节点传到根节点，此时特判。
>
> 当没有小于x值的节点，不会发生rotate，根节点即为大于x的最小值节点，此时特判。（不过不会出现这种情况，毕竟加了-inf。故没有x时会传入小于x的最大值节点方到根节点上）

## 删除、第k小、x的排名

删除时，将x的前驱翻至根节点，x的后驱翻至前驱的子节点。比前驱大又比后驱小的数只有x本身，此时x处在的节点在后驱的左子节点上。cnt--或者左子指针=0即可。

第k小则是根据左节点的size来递归找就行。

x的排名则是直接splay到根节点上，然后查询左子树有多大即可。

> 可能不存在x。对于x的排名要特判根节点是否为x。若根节点小于x，此时要将根节点的cnt算进去。

```cpp
int next_bound(int x, int f)
{
	find(x);
	int u = root;
	if(T[u].val > x && f) return u;//此时把>x的最小点传到了根节点上（其实并不会有这种情况，由于加了-inf）
	if(T[u].val < x && !f) return u;//此时将<x的最大点传到了根节点上
	u = T[u].ch[f];
	while(T[u].ch[f^1]) u = T[u].ch[f^1];
	return u;
}

void erase(int x)
{
	int last = next_bound(x, 0);
	int next = next_bound(x, 1);
	splay(last,0),splay(next,last);
	int del = T[next].ch[0];
	if (T[del].cnt > 1) T[del].cnt--,splay(del, 0);
	else T[next].ch[0] = 0;
	upd(next);upd(last);
}

int kth(int x)//直接返回值
{
	int u = root;
	if (T[u].siz < x) return 0;
	while(233){
		int y = T[u].ch[0];
		if (x > T[y].siz + T[u].cnt) x -= T[y].siz + T[u].cnt,u = T[u].ch[1];//别写成跟T[u].siz比较了。。T[u].siz可是包含两颗子树的siz的
		else if (T[y].siz >= x) u = y;
		else return T[u].val;
	}
}
```

## 区间转置

首先按编号建树（类似线段树的建树）。旋转[l,r]时，将l的前驱，r的后驱分别rotate到根节点、根节点右子节点

，然后在被孤立的子树上打tag。然后对于任意的操作，记得下放tag就行。

> 同样的，下放tag时不要让0节点下放tag，叶节点不存在也不要放。

```cpp
int kth(int x)//查询第k小
{
    int u = rt;
    while(233){
        down(u);
        if(T[T[u].ch[0]].siz>=x) u = T[u].ch[0];
        else{
            x -= T[T[u].ch[0]].siz + 1;
            if(!x) return u;
            u = T[u].ch[1];
        }
    }
}

void rev(int l,int r)
{
    l = kth(l-1),r = kth(r+1);
    splay(l,0),splay(r,l);
    T[T[r].ch[0]].tag ^= 1;
}

void dfs(int u)
{
    //printf("#%d\n",u);
    down(u);
    if(T[u].ch[0]) dfs(T[u].ch[0]);
    if(T[u].val!=-inf&&T[u].val!=inf) printf("%d ",T[u].val);
    if(T[u].ch[1]) dfs(T[u].ch[1]);
}
```

