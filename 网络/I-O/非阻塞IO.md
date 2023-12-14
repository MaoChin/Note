# 非阻塞`I/O`

文件描述符默认都是阻塞式的，可以通过系统调用`fcntl`设置文件描述符的等待方式。

```C
int fcntl(int fd, int cmd, ... /* arg */ );
```

```C++
// 获取fd的属性
int flag = fcntl(fd, F_GETFL);
// 设置属性，加上NONBLOCK属性
int ret = fcntl(fd, F_SETFL, flag | O_NONBLOCK);
```

