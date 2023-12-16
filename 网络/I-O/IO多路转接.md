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

### 1. `select`编码特点

1. **每次调用`select`之前都要重置那三个输入输出型参数**；**`select`调用完成之后要对所有合法的`fd`进行遍历检测**，确定是那些`fd`事件就绪。
2. ==需要用户自己维护一个数组来存放所有合法的`fd`==，这样`select`才能进行批量处理。
3. 一旦某个`fd`的事件就绪，那么==这次==对这个`fd`的操作不会被阻塞！

### 2. `select`的优缺点

优点：

1. 对比多进程/多线程，`select`占用资源少，更高效。 

缺点：

1. 每一次调用都要重置那三个输入输出型参数！！麻烦且影响性能。
2. ==能够同时检测的`fd`数量有限==！！(`fd_set`位图是128字节，也就是只有128*8=1024个比特位，即最多只能同时检测1024个`fd`！！)
3. 每一次调用都需要从用户到内核，内核到用户传递参数。这中间的数据拷贝会影响性能。(这是多路转接都有的问题！)
4. ==每一次调用前和调用后都要遍历自己维护的合法`fd`数组，检测所有的`fd`==，效率低下！

## 2. `poll`

```C
int poll(struct pollfd *fds, nfds_t nfds, int timeout);

// pollfd结构
struct pollfd {
  int fd; 				// file descriptor 
  // 输入输出分离
  short events; 	// requested events  用户->内核
  short revents; 	// returned events 	 内核->用户
};
```

1. `fds`：`pollfd`的数组，也就是结构体数组。
2. `nfds`：`fds`数组的长度(元素个数)。
3. `timeout`：单位是毫秒，`timeout`时间内阻塞等待。和`select`几乎一样。
4. 返回值：和`select`一模一样。
5. `events`：`short`2个字节，16比特位，每一位代表不同的事件。比如读事件，写事件，各种异常事件等。通过比特位传递信息，也是位图。



## 3. `epoll`

















