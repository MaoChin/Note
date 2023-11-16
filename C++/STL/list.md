# list

带头双向循环链表。

## 1. `list`常用接口

### 1. 默认成员函数

| 构造函数（ (constructor)）                                  | 说明                                    |
| ----------------------------------------------------------- | --------------------------------------- |
| `list (size_type n, const value_type& val = value_type()) ` | 构造的`list`中包含n个值为`val`的元素    |
| `list()`                                                    | 构造空的`list`                          |
| `list (const list& x) `                                     | 拷贝构造函数                            |
| `list (InputIterator first, InputIterator last) `           | 用`[first, last)`区间中的元素构造`list` |

### 2. 容量操作

| 函数声明 | 接口说明                                    |
| -------- | ------------------------------------------- |
| `empty ` | 检测list是否为空，是返回true，否则返回false |
| `size `  | 返回list中有效节点的个数                    |

### 3. 访问及遍历操作

| 函数声明        | 接口说明                                                     |
| --------------- | ------------------------------------------------------------ |
| `begin + end `  | 返回第一个元素的迭代器+返回最后一个元素下一个位置的迭代器    |
| `rbegin + rend` | 返回第一个元素的`reverse_iterator`,即end位置，返回最后一个元素下一个位置的` reverse_iterator`,即begin位置 |
| `front`         | 返回`list`的第一个节点中值的引用                             |
| `back`          | 返回`list`的最后一个节点中值的引用                           |

### 4. 增删改查

| 函数声明     | 接口说明                                  |
| ------------ | ----------------------------------------- |
| `push_front` | 在list首元素前插入值为`val`的元素         |
| `pop_front`  | 删除list中第一个元素                      |
| `push_back`  | 在list尾部插入值为`val`的元素             |
| `pop_back`   | 删除list中最后一个元素                    |
| `insert`     | 在list position 位置中插入值为`val`的元素 |
| `erase`      | 删除list position位置的元素               |
| `swap`       | 交换两个list中的元素                      |
| `clear`      | 清空list中的有效元素                      |

## 2. `list`迭代器失效

`list`每个节点并==不是连续存储==的，所以不会涉及到扩容操作，也就==不会导致因底层空间改变而造成的野指针问题(第一类迭代器失效)。==

但是当删除节点时还是会导致第二类迭代器失效：==删除元素时指向该元素的迭代器失效(野指针)！==



## 3. `list`模拟实现

### 1. 迭代器

`list`的迭代器就不是原生指针了！！而是自己封装的自定义类型，然后使用运算符重载完成各种操作，使得迭代器的使用==像指针一样==。

```C++
// 后面两个参数做到了进一层的抽象，实现了代码的复用！牛逼！
template<class T, class Ref, class Ptr> 
struct __list_iterator
{
	typedef list_node<T> Node;
  typedef __list_iterator<T, Ref, Ptr> self;
  // 成员就是指向节点的指针！
  Node* node_;
  
  __list_iterator(Node* node)
    :node_(node)
  {}
  
  // 重载 * 运算符(解引用)
  Ref operator*()
  {
  	return node_->data_;  
  }
  // 重载 -> 运算符 (这个运算符返回对象的地址，使用时应该再加一个->,但是为了可读性，可以不加后面的->,编译器会自动添加)
  Ptr operator->()
  {
    return &(operator*());
  } 
  // 前置++
  self& operator++()
  {
    node_ = node_->next_;
    rerurn *this;
  }
  // 后置++
  self operator++(int)
  {
    self tmp(*this);
    node_ = node_->next_;
    return tmp;
  }
  // 前置--，后置--
  // 重载 ==, != 
  bool operator==(const self& it) const
  {
    return node_ == it.node_;
  }
  bool operator!=(const self& it) const
  {
    return !(this == it); 
  }  
};

template<class T>
class list
{
public:
  // 迭代器是自己实现的类！！不是原生指针！
  typedef __list_iterator<T, T&, T*> iterator;
  // 通过添加两个模板参数，达到灵活控制const和非const的区别！
  typedef __list_iterator<T, const T&, const T*> const_iterator;
  
  const_iterator begin() const
  {
    // ...
  }
  const_iterator end() const
  {
    // ...
  }
  iterator begin()
  {
    // ...
  }
  iterator end()
  {
    // ...
  }
  
private:
  // ...
};
```

###### ==反向迭代器==

反向迭代器是一种**适配器模式**！！它的底层就是正向迭代器，==通过对正向迭代器的包装适配得到反向迭代器。==实现了代码复用！

因为反向迭代器除了 `++, --`操作，其他基本和正向迭代器一模一样！并且牛逼的是，这个反向迭代器**并不是每一个容器都要自己封装适配正向迭代器，而是在一个文件中(`stl_iterator.h`)统一实现**，这样只要给我正向迭代器我就能适配出对应的反向迭代器，而不去关心是哪一个容器的迭代器(==泛型编程==)！

```C++
// 反向迭代器---适配器
// 和正向迭代器一样，这里传三个模板参数也是为了控制const和非const。
// 但是SGI版本的STL通过 迭代器萃取 的技术使得模板参数只给一个正向迭代器也可以！
template<class Iterator, class Ref, class Ptr>
struct ReverseIterator
{
  Iterator _it;
  typedef ReverseIterator<Iterator, Ref, Ptr> Self;
  
  ReverseIterator(Iterator it)
    :_it(it)
  {}
  Ref operator*()
  {
    // 为了对称，反向迭代器的begin()是正向迭代器的end()，反向迭代器的end()是
    // 正向迭代器的begin(),但是正常来说应该错一个！所以这里 返回 *(--tmp)
    Iterator tmp(_it);
    return *(--tmp);
  }
  Ptr operator->()
  {
    return &(operator*());
  }
  Self& operator++()
  {
    // ++ 就是 --
    --_it;
    return *this;
  }
    Self& operator--()
  {
    // -- 就是 ++
    ++_it;
    return *this;
  }
  bool operator==(const self& it) const
  {
    return _it == it._it;
  }
  bool operator!=(const self& it) const
  {
    return !(this == it);
  }
};

template<class T>
class list
{
public:
  // 迭代器是自己实现的类！！不是原生指针！
  typedef __list_iterator<T, T&, T*> iterator;
  // 通过添加两个模板参数，达到灵活控制const和非const的区别！
  typedef __list_iterator<T, const T&, const T*> const_iterator;
  
  // 反向迭代器
  typedef ReverseIterator<iterator, T&, T*> reverse_iterator;
  typedef ReverseIterator<const_iterator, const T&, const T*>
     const_reverse_iterator; 
  
  const_reverse_iterator rbegin() const
  {
    // 正向的end()就是反向的 begin()，错了一位
    // 只是在SGI版本是这样的，其他版本可能不一样
    return const_reverse_iterator(end());
  }
  const_reverse_iterator rend() const
  {
    // 正向的begin()就是反向的end()
    return const_reverse_iterator(begin());
  }
  reverse_iterator rbegin()
  {
    return reverse_iterator(end());
  }
  reverse_iterator rend()
  {
    return reverse_iterator(begin());
  }
  
private:
  // ...
};
```

###### ==迭代器萃取(`traits`)==

萃取是`STL`中的一个复杂的编程技法！例如使用迭代器萃取使得反向迭代器的模板参数只给一个正向迭代器即可---主要是针对`vector`和`string`这种使用原生指针作为迭代器的容器，使用**模板的偏特化**对他们进行特化处理，使得可以得到对应的指针和引用类型。

## 4. `list`和`vector`的比较











 
