# Python基本语法

放张大图：

![](C:\Users\Administrator\Desktop\markdown图片\python语法简图.png)

以下仅记录图中没有的笔记。

## 交互式编程 & 脚本式编程 & 主函数

交互式编程使用 Python 解释器的 **交互模式** 进来编写代码。简单来说，就是使用 *IDLE Shell* 编写程序。编写过程是交互的过程。

脚本式编程。平常写的cpp文件都是 **脚本** ，即 “剧本” ，写完后让编译器演绎。那么python对应的是py文件，编辑完毕后交给编译器编译链接，然后在控制台完成交互。

对于每个写完的 **脚本** ，若无主函数，可以称作是一个 **模块（module）**。在编译链接py文件时，默认会给`__name__`传一个`__main__`值，这时就代表执行了主函数。如果没写`if __name__=='__main__':`，就不会执行主函数，脚本就仅为一个模块而已。

## 输入与输出

标准输入输出：

格式化输出：格式化跟c++一致。`print("xxx is %d %d %d" %(x,y,z),end = '')`，可以用`%()`框起来所有输出的变量。由于`print`默认换行，若不换行可以加上`end = ''`。

输入：`input([prompt])`是行输入。可以这么使用：`n,m = map(int,input("请输入n,m：").split())`，表示将字符串按空字符分开，读入n,m，并映射成int类型。（默认float）

文件输入输出：

文件声明：`file = open("file_name","mode")`

输入：`file = open("file_name","r");file.readline()`，也是得到一个字符串，可以使用`split()`。

输出到文件里：`file = open("file_name","w");file.write()`。这个`write`跟标准的print使用方式一样，但 **默认不换行** 。

关于mode

<img src="C:\Users\Administrator\Desktop\markdown图片\python文件mode.png" style="zoom:67%;" />

### 三种数字类型

float，int，complex。

## 循环

`for [iterator] in [list]`，代表遍历list里的 **所有元素**。

其中应用比较多的函数为`range(N),range(1,n+1)`（仅有一参数时从0开始），表示生成一个$[beg,beg+1,\dots,end-1]$的列表。

更宽泛的， `range(beg,end,step = 1)` ，即range默认步长为1。只需要传-1就可以实现逆序遍历。 

于是有`for [iterator] in range(N)`的形式。

更泛化的循环，则采用下面的形式：

自定义一个循环函数，用`yield`来返回一个值。当程序执行至`yield`，会停止，下次调用时则会继续执行yield下面的语句。

举个例子，遍历邻接表：

```python
N = int(1e5 + 5)
M = int(1e5 + 5)
INF = 0x3f3f3f3f

class qxx:
    def __init__(self):
        self.next = 0
        self.w = 0
        self.v = 0
p = [-1 for i in range(N)]
edge = [qxx() for i in range(M)]

def findnex(u):
    i = p[u]
    while i!=-1:
        yield i
        i = edge[i].next

def dfs(u):
    for i in findnex(u):
        v = edge[i].v
        #...
    print("end while.")
```

## 类的声明与初始函数

见上面代码。

## main函数部分

`if __name__ == '__main__':`

## 附：使用cmd/git bash/powershell安装软件包

本来是要另开博文的，在这里先简单写写，以后系统学习时再详细记录。

首先都是一串命令。在确保命令下载的文件不损坏系统的前提下，用 **管理员身份** 运行cmd/git bash...来执行命令，以防安装失败。

另外一些软件包（比如说python的模块）在官方网站下载会很慢，此时则使用国内镜像网站下载（不过现在pycharm上已经内置国内镜像网站了，下载没问题）

