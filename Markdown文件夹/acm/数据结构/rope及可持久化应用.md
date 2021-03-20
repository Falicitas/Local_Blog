# rope及可持久化应用

rope的底层是平衡树。支持以下操作：

| 函数           | 功能             |
| -------------- | ---------------- |
| push_back(x)   | 在末尾添加x      |
| insert(pos,x)  | 在pos插入x       |
| erase(pos,x)   | 从pos开始删除x个 |
| replace(pos,x) | 从pos开始换成x   |
| substr(pos,x)  | 提取pos开始x个   |
| at(x)/[x]      | 访问第x个元素    |

需要引入头文件和命名空间：

```cpp
#include <ext/rope>
using namespace __gnu_cxx;
```



上述的pos是光标，插入x指在pos后插入。故pos的范围在$[0,rope.size]$。rope下标从0开始。

## 可持久化应用

rope数据间拷贝仅拷贝根节点，所以是$O(1)$的。只需要调用下面的开辟空间命令就可实现可持久化：

```cpp
rope<char>*a,*b;
a=new rope<char>;
b=new rope<char>(*a);//O(1)拷贝
```

## 一些习题

### P6166 [IOI2012]scrivener

对一个文本进行下列三种操作：

Type(alp)：文本后敲一个小写字母。

Undo(U)：回撤U步指令（包括Undo与Type）。

Query(x)：询问x光标后的一个字符。

由于回撤包括自己的操作，所以得上可持久化。回撤到当前版本的$id-U-1$即可。

### P4008 [NOI2003]文本编辑器