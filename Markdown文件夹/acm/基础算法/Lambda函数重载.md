# Lambda函数重载

目前仅学了一种重载形式：

```cpp
sort(f,f+n,[](int x,int y){return g[x] < g[y];});
```

此时$f_i,g_i$绑定在一起，排完序后$g[i] < g[j]$，当然对应的$f$也发生了变化。