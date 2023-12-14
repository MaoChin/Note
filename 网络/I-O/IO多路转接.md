# `I/O`多路转接

`select`，`poll`，`epoll`都是解决“等”的问题。通过监视文件描述符的状态变化：可读，可写，异常，等待事件就绪。

这些方案可以一次等待多个文件描述符。

## 1. `select`

```C
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set 		  			*exceptfds, struct timeval *timeout);
```

1. `nfds`：等待的文件描述符中最大的那个 再 +1。(文件描述符就是数组下标)
2. `fd_set`：位图结构。
3. `readfds`：输入输出型参数。输入：需要监视“读”事件就绪的文件描述符；输出：“读”事件已经就绪的文件描述符。

