# `kcp`源码阅读

## 1. 总体使用

1. `ikcp_create`
2. `ikcp_release`

队列 `queue` 操作。

## 2. 数据发送

1. `ikcp_setoutput`
2. `ikcp_send`
3. `ikcp_flush`

![image-20240301121325620](D:\MyNote\网络\kcp\kcp源码阅读.assets\image-20240301121325620.png)

## 3. 数据接收

1. `ikcp_input`
2. `ikcp_recv`

![image-20240301121304181](D:\MyNote\网络\kcp\kcp源码阅读.assets\image-20240301121304181.png)

## 4. 数据重传

## 5. 拥塞控制

