# `	vector`

C++常用的几个技术：仿函数，`lambda`表达式，包装器。

`vector`：可以动态扩容的数组。

## 1. `vector`常用接口

### 1. 默认成员函数

| (constructor)构造函数声明                                   | 接口说明                 |
| ----------------------------------------------------------- | ------------------------ |
| `vector()`（重点）                                          | 无参构造                 |
| `vector(size_type n, const value_type& val = value_type())` | 构造并初始化n个`val`     |
| `vector (const vector& x); `（重点）                        | 拷贝构造                 |
| `vector (InputIterator first, InputIterator last); `        | 使用迭代器进行初始化构造 |

### 2. 容量操作

| 容量空间           | 接口说明                         |
| ------------------ | -------------------------------- |
| `size `            | 获取数据个数                     |
| `capacity `        | 获取容量大小                     |
| `empty `           | 判断是否为空                     |
| `resize`（重点）   | 改变vector的size，扩容+初始化    |
| `reserve `（重点） | 改变vector的capacity，不改变size |

`resize`，`reserve`一般不会缩容！

### 3. 访问及遍历操作

| iterator的使用        | 接口说明                                                     |
| --------------------- | ------------------------------------------------------------ |
| `begin + end`（重点） | 获取第一个数据位置的iterator/const_iterator， 获取最后一个数据的下一个位置 的iterator/const_iterator |
| `rbegin + rend`       | 获取最后一个数据位置的reverse_iterator，获取第一个数据前一个位置的 reverse_iterator |

### 4. 增删查改

| vector增删查改        | 接口说明                                               |
| --------------------- | ------------------------------------------------------ |
| `push_back`（重点）   | 尾插                                                   |
| `pop_back `（重点）   | 尾删                                                   |
| `find `               | 查找。（注意这个是算法模块实现，不是vector的成员接口） |
| `insert `             | 在position之前插入`val`                                |
| `erase `              | 删除position位置的数据                                 |
| `swap `               | 交换两个vector的数据空间                               |
| `operator[]` （重点） | 像数组一样访问                                         |

增容机制：`VS`下`P.J.`版本是1.5倍增容；`Linux`下`SGI`版本是2倍增容。每次增容增的多，那么==增容的此数会相对少，扩容不那么频繁，效率高，但是空间浪费也可能更多==。



## 2. ==迭代器失效(重点)==

迭代器就是==像指针一样的东西==，作用就是在使用时能够不用关心底层数据结构，因此迭代器失效实际就是==迭代器底层对应指针所指向的空间被销毁了==，而使用一块已经被释放的空间会造成程序奔溃。

在`string`，`vector`这种==连续空间存储==的容器中，非常容易发生迭代器失效！如：

1. 底层空间改变，如扩容(`resize、reserve、insert、assign、push_back`)等操作，会**导致所有的数据存储到了新的空间，但这时有可能有其他的迭代器指向原来的空间！！**就会导致==野指针==问题。
2. 底层空间没变，但是当前**迭代器指向位置的值已经被删除或者被改变**了！如`erase`，`insert`操作。不会导致野指针问题，但是迭代器指向位置的意义改变了。

不同平台对迭代器失效的检查机制不一样！`VS`下会更严格一些。对于第一种野指针问题，`VS`直接报错！而`Linux`下`g++`并不会报错！

正是因为有迭代器失效的问题，所以`insert`，`erase`这种成员函数都有返回值！==返回插入/删除后迭代器的位置。==

## 3. `vector`模拟实现

### 1. 迭代器

和`string`一样，`vector`的迭代器也是原生指针。

```C++
template<class T>
class vector{
public:
	typedef T* iterator;
  typedef const T* const_iterator;
  // ...
  
private:
  iterator start_;
  iterator finish_;
  iterator end_of_storage_;
};
```

### 2. 扩容时的深浅拷贝问题

`vector`在扩容时会先开好新的空间，然后将旧空间的数据拷贝到新的空间，最后释放旧空间的数据。这里 ==拷贝数据必须是深拷贝！==否则也会导致深浅拷贝问题。
