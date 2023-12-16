# `I/O`多路转接

`select`，`poll`，`epoll`都是==解决“等”的问题==。通过监视文件描述符的状态变化：可读，可写，异常(如连接关闭，文件描述符非法等)，等待事件就绪。

这些方案可以一次等待多个文件描述符。但是这些方案也是要等待的，所以也会涉及到等待策略的问题：阻塞式？轮询？信号驱动？？

## 1. `select`

```C
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set 		  			*exceptfds, struct timeval *timeout);
```

1. `nfds`：等待的文件描述符中最大的那个 再 +1。(文件描述符就是数组下标)
2. `fd_set`：位图结构，比特位的位置代表所有的文件描述符编号，0/1代表是否就绪。
3. `readfds`：输入输出型参数。输入：需要监视“读”事件就绪的文件描述符；输出：“读”事件已经就绪的文件描述符。
4. `writefds`：输入输出型参数。输入：需要监视“写”事件就绪的文件描述符；输出：“写”事件已经就绪的文件描述符。
5. `exceptfds`：输入输出型参数。输入：需要监视“异常”事件就绪的文件描述符；输出：“异常”事件已经就绪的文件描述符。这三个输入输出参数用的同一张位图，所以每次修改会覆盖！==所以每次调用`select`，都要重新设置这三个参数！！==
6. `timeval`：等待策略，设置`deadline`，在`deadline`内阻塞等待，超过`deadline`就`timeout`返回。设置成`nullptr`表示一直阻塞。
7. 返回值：>0 ：有几个`fd`就绪。

​					<0 ：出错

​					==0：`timeout`了

```C
// fd_set 的设置接口
// 用来清除描述词组set中相关fd 的位
void FD_CLR(int fd, fd_set *set); 
// 用来测试描述词组set中相关fd 的位是否为真
int FD_ISSET(int fd, fd_set *set); 
// 用来设置描述词组set中相关fd的位
void FD_SET(int fd, fd_set *set); 
// 用来清除描述词组set的全部位
void FD_ZERO(fd_set *set); 
```

