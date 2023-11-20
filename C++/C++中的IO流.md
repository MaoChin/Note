# `C++`中的I/O流

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

==这个成员函数就使得 `istream` 类型的对象可以强制类型转换成 `bool` 类型！！==进行真假判断。