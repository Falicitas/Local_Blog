# vector的resize

需要区分`size`和`capacity`，`resize`和`reserve`，以避免在使用`vector`时**效率低**或者**访问错误**等问题。

## size 与 capacity

`size`只实际存储了多少元素；

`capacity`表示在下一次空间扩增前现有的容器容量。

## resize 和 reserve

`reserve`仅改变`capacity`。如果有操作需要大量进行`push_back`操作，则先`reserve`个上界操作数以避免频繁扩容。

而`resize`则是**删除**或**填充**容器，以达到目标容量。用`resize`就最好别再用`push_back`了，不仅会触发扩容机制，其push进来的元素是放在size+1的位置的。例如空vector`resize(10),push_back(1)`，第$11$号元素才是1。

### vector扩容机制

其是一个很低效的扩容手段：新增一段两倍于原来空间的连续空间，再将原来的容器元素一一赋值过去。

