# `STL`之迭代器

## 1. 迭代器的分类

迭代器的类型：

按照使用方式分：

1. 输入迭代器(`InputIterator`)
2. 输出迭代器(`OutputIterator`)

按照访问方式分：

1. ==单向迭代器(`ForwordIterator`)  仅支持 `++`==

   `forword_list`，`unordered_set`，`unordered_set`

2. ==双向迭代器(`BidirectionalIterator`)   支持 `++，--`==

   `list`，`set`， `map`

3. ==随机访问迭代器(`RandomAccessIterator`)   支持 `++， -- ，+ ，-`==

   `string`，`vector`，`deque`，`array`

   **自下向上兼容！**

   

另外还有一种**插入迭代器**，也是一种输出迭代器(==输出型参数==)，可以在迭代器的位置插入数据。插入迭代器有三类：`front_insert_iterator`(头插)，`back_insert_iterator`(尾插)，`insert_iterator`(在区间的指定位置之前插入)。

使用标准库提供的函数可以构造上述三个插入迭代器。

```c++
// 分别是
front_inserter()
back_inserter()
inserter()
```

==它们的存在就是为了解耦合！！！==作为容器和其他函数中间的中介！容器看不到其他函数，函数也看不到容器，都只通过这个迭代器操作。

