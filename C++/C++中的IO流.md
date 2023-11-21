# `C++`中的I/O流

## 1. `operator bool()`

考虑下面这种使用：

```C++
int main()
{
  string str;
  // 这里返回值为什么可以当作bool类型进行真假判断？？？
  while(cin >> str) // 相当于调用函数 operator>>(cin, str)
  {
    // ...
  }
}
```

首先，这里是调用了重载函数 `operator>>`，该函数的返回值也是 `istream`类型的对象。

![image-20231120213129936](E:\Note\C++\C++中的IO流.assets\image-20231120213129936.png)

然后，`istream`类型的对象有这样一个成员函数 `operator bool`：

![image-20231120213315192](E:\Note\C++\C++中的IO流.assets\image-20231120213315192.png)

==这个成员函数就使得 `istream` 类型的对象可以强制类型转换成 `bool` 类型！！==本质就是调用这个成员函数进行真假判断。换言之，只要在类中加入成员函数 `operator bool()`，该类型就可以强转成`bool`类型(**调用成员函数**)！！(一种语法机制)

```C++
// operator bool() 示例
class A
{
public:
  // 这个函数没有返回值,一般也没有参数
  // 而且一般用 explicit修饰，防止隐式类型转换比如：bool ret = a; a是A对象
  explicit operator bool()
  {
    // ...
    if(/*...*/)
      return true;  // 但是函数内部可以返回 true/false
    else 
      return false;
  }
};
```

拓展来说，不止有`operator bool()`，如果需要，也可以有`operator int()`，`operator double()`.......

这样就可以做到**内置类型转自定义类型**！！！





