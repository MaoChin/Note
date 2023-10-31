# `C++`中的动态内存管理

## 1. 内存分布与虚拟地址空间

![image-20231031195415601](E:\Note\C++\C++中的动态内存管理.assets\image-20231031195415601.png)

每一个加载到内存中的进程，都有一个虚拟地址空间，再经过页表映射到物理内存空间。

## 2. `C`语言的动态内存管理

`malloc`：动态开辟空间，不会初始化。

`calloc`：动态开辟空间+初始化。

`realloc`：堆动态开辟的空间进行重新分配。

`free`：释放动态开辟的空间。

上面这些都是==函数调用。==

## 3. `C++`的动态内存管理

`new`：动态申请内存

`delete`：释放内存

批量动态内存管理：`new []` ，`delete []`

```C++
int main()
{
  int* a = new int;
  int* b = new int[10]; // new 10个int类型的元素
  int* c = new int(10); // 初始化为10
  
  delete a;
  delete[] b;
  delete c;
  return 0;
}
```

==`new`和`delete`是关键字！！==

==`new`在申请自定义类型的空间时，在申请空间后会自动调用其构造函数进行初始化；`delete`在释放自定义类型的空间时，会先调用它的析构函数，再释放空间。==

## 4. `new`和`delete`的实现原理

### 1. `operator new` 和 `operator delete`函数

`new`和`delete`是用户进行动态内存申请和释放的**操作符(关键字)**，而`operator new` 和`operator delete`是系统提供的**全局函数**，`new`在底层调用`operator new`全局函数来动态申请空间，`delete`在底层通过`operator delete`全局函数来释放空间。

实际上，`operator new` 是通过调用`malloc`来申请空间，如果`malloc`申请空间成功就直接返回，否则执行用户提供的空间不足时的应对措施，如果用户提供该措施就继续申请，否则就抛异常。`operator delete` 最终也是通过调用`free`来释放空间的。

**`operator new` 和 `operator delete`函数对于自定义类型不会调用其构造/析构函数。**

### 2. 重载`operator new` 和 `operator delete`函数

一般情况下不需要对 `operator new` 和 `operator delete`进行重载，除非在申请和释放空间的时候有某些特殊的需求。比如：在使用`new`和`delete`申请和释放空间时，打印一些日志信息。

### 3. `new`和`delete`的实现原理

`new`：先调用 `operator new`申请空间(`operator new`又调用`malloc` )，**然后对于自定义类型会调用它的构造函数初始化。**

`delete`：**对于自定义类型先调用其析构函数释放资源**，然后调用 `operator delete`函数释放空间(`operator delete`又调用 `free`)。

`new [N]`：先调用 `operator new[]`申请空间(`operator new[]`调用`operator new` )，然后对于自定义类型会调用`N`次它的构造函数初始化。

`delete []`：对于自定义类型先调用`N`次其析构函数释放资源，然后调用 `operator delete[]`函数释放空间(`operator delete[]`调用 `operator delete`)。

## 5. `placement-new`

`placement-new`是指在已分配的动态内存空间中调用构造函数初始化一个对象。  一般是配合内存池使用，因为内存池申请的内存并没有初始化，所以使用`placement-new`去初始化自定义类型。

使用方式：

```C++
class T
{
public:
  T(int a)
    :a_(a)
  {}
  ~T()
  {}

private:
  int a_;
};
int main()
{
  // 正常 new, delete
  T* t = new T(10);
  delete t;
  
  // 只申请空间，并没有初始化！
  T* t1 = (T*)malloc(sizeof(T));
  assert(t1);
  // 使用 placement-new 初始化 t1
  new(t1)T(20);
  t1->~T();
  free(t1);
  
  return 0;
}
```

## 6. `malloc/free` 和`new/delete`的区别

1. `malloc/new`是**函数调用**，而`new/delete`是**关键字**。
2. `new`申请空间可以进行初始化。(不过`calloc`也可以)
3. `malloc`要传参指定需要的空间大小，而`new`只需要给类型就可以。
4. `malloc`的返回值是 `void*`，要进行**强转**，而`new`不需要，**直接返回对应类型的指针**。
5. `malloc`申请空间失败返回`NULL`，而**`new`失败抛异常**。
6. 对于自定义类型的区别！！

异常捕获：

```C++
int main()
{
  try
  {
    // 10G
    int* p = new int[1024*1024*1024*10];
    // ...
  }
  catch(const exception& e)  //  捕获异常信息
  {
		cout << e.what() << endl;  // 打印异常信息
  }
  return 0;
}
```

## 7. 内存泄漏

一般又两类内存泄漏：

1. 动态申请的内存空间没有释放。
2. 申请的==系统资源没有释放==！如文件描述符`fd`，网络套接字`socketfd`等。

避免内存泄漏：

1. 良好的代码规范。
2. 使用`RAII`来管理资源。(智能指针)
3. 使用内存泄漏检测工具。

