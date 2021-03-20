# vector语法

## 构造函数

`vector():`创建一个空vector。

`vector(int Size):`创建一个vector,元素个数为nSize。

`vector(int Size,const T& t):`创建一个vector，元素个数为nSize,且值均为类型为T的t。

`vector(const vector&):`复制构造函数。

`vector(begin,end):`复制`[begin,end)`区间内另一个数组的元素到vector中，此时新vec的大小等于`end - begin`。（当然可以`vector<int>(begin,end)`）。

## 增加函数

`void push_back(const T& x):`向量尾部增加一个元素x。

`iterator insert(iterator it,const T& x):`向量中迭代器指向**元素前**增加一个元素x。

`iterator insert(iterator it,int n,const T& x):`向量中迭代器指向元素前增加n个相同的元素x。

`iterator insert(iterator it,const_iterator first,const_iterator last):`向量中迭代器指向元素前插入另一个相同类型向量的`[first,last)`间的数据。

## 遍历函数

`reference at(int pos):`返回pos位置元素的引用。

`reference front():`返回首元素的引用。

`reference back():`返回尾元素的引用。

`iterator begin():`返回向量头指针，指向第一个元素。

`iterator end():`返回向量尾指针，指向向量最后一个元素的下一个位置。

`reverse_iterator rbegin():`反向迭代器，指向最后一个元素。

`reverse_iterator rend():`反向迭代器，指向第一个元素之前的位置。

## 判断函数

`bool empty() const:`判断向量是否为空，若为空，则向量中无元素。

## 大小函数

`int size() const:`返回向量中元素的个数。

`int capacity() const:`返回当前向量所能容纳的最大元素值。

`int max_size() const:`返回最大可允许的vector元素数量值。

## 交换函数

`void swap(vector&):`交换两个同类型向量的数据。