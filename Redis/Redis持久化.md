# `Redis`持久化(重点)

`redis`数据存储在内存中(==更快==)，关机/重启数据会丢失---不具有持久性。

要使得`redis`的数据具有持久性，就需要对数据进行持久化，也就是要存到磁盘，重启时再从磁盘读取数据。

## `RDB`持久化机制(`Redis DataBase`)

==定期备份机制。==定期把`redis`中的所有数据写入到硬盘中，生成一个“快照”。

### 1. 基本介绍

定期备份方式有两种：

1. 手动触发“快照”。用户通过客户端执行命令(`save`，`bgsave`)进行备份。

   (1). `save` ：阻塞式的进行备份，进程自己停下现在所有的活进行备份，生成“快照”，**会阻塞客户端命令！！**所以一般不用。

   (2). `bgsave `：(background save) **后台备份，不会影响`redis`服务器处理客户端的其他请求。**具体是通过多进程的方式实现的---创建子进程，==子进程内存中的数据是拷贝父进程的，并且文件描述符表也是一样的，所以完全可以独立的进行备份！！==(写时拷贝！！并且一般父进程也不会有大的改动，也就是不会真正拷贝太多，所以效率也很高！！)

   ![image-20230929164840145](E:\Note\Redis\Redis持久化.assets\image-20230929164840145.png)

2. 自动触发“快照”。在配置文件中设置备份的周期，而后自动进行备份。在`redis`服务器**正常关机/重启，主从复制**时也会自动触发`RDB`快照。但是服务器异常退出就不会自动触发`RDB`快照了。

```shell
# 配置文件中
# save 900s 至少一次操作  表示在900s后并且至少有1次操作才会自动触发快照
save 900 1
save 300 10
...

# 关闭自动触发快照
save ""
```

==生成`RDB`快照成本还是比较高的，不能太频繁！！！==这就会导致备份的快照和实际内存中的数据大概率会有偏差。

### 2. `RDB`文件(二进制文件)

默认保存在`/var/lib/redis`目录下，可以通过配置文件(`/etc/redis/redis.conf`)更改。(修改配置文件后要重启服务才能生效)	

默认会对`RDB`文件进行压缩处理，虽然会消耗`CPU`资源，但是压缩后体积更小，节省硬盘并且==方便进行网络传输。==

还可以通过`redis-check-rdb`程序对`RDB`文件进行校验，查看文件是否损坏。(`/usr/bin/redis-check-rdb`) 若`RDB`文件损坏，则重启得到的结果时未定义的！！

![image-20230929194503208](E:\Note\Redis\Redis持久化.assets\image-20230929194503208.png)

```shell
redis-check-rdb /var/lib/redis/dump.rdb  # 校验RDB文件
```

`RDB`文件只有一个，当其已经存在时新生成的`RDB`文件会替换掉原来的。

当`redis`服务异常挂了可以查看`redis`日志：`/var/log/redis`目录下。

### 3. `RDB`持久化的优缺点

优点：

1. `RDB`文件是压缩的二进制文件，代表`redis`在某个时间点的快照，==适合定期全量备份。==
2. 重启时加载`RDB`文件恢复更快！！

缺点：

1. 不能实时备份，两次快照之间的数据可能会丢失。
2. 不同`redis`版本的`RDB`文件可能不兼容。

## `AOF`持久化机制(`Append Only File`)

==实时备份机制。==

### 1. 基本介绍

有点像`MySQL`的`binlog`，把用户的所有`redis`操作命令记录下来保存到磁盘中，并定期进行重写压缩。恢复时只需依次执行这些命令即可。(默认关闭`AOF`)

开启`AOF`持久化：

```shell
# 在 /etc/redis/redis.conf 文件中
appendonly yes  # 默认是 no
```

`AOF`文件也是保存在`/var/lib/redis`目录下，和`RDB`一样。==当`RDB`和`AOF`持久化策略都开启时以`AOF`为主！！==

并不是每一次`redis`操作都要写入到磁盘，那样就太慢了！！而是写入到缓冲区，定期刷到磁盘上，减少I/O操作。而且写到磁盘上也是顺序写入。

![image-20230929203431427](E:\Note\Redis\Redis持久化.assets\image-20230929203431427.png)

**但是这样缓冲区中还没有落盘的数据也是有可能会丢失的！！！鱼和熊掌不可兼得。**缓冲区落盘频率越高，对性能影响越大，但是数据丢失的可能更小；缓冲区落盘频率越低，对性能影响越小，但是数据丢失的可能更大。

`redis`提供了三种缓冲区的刷新策略：(配置文件中 `appendfsync`)

1. `always` 有命令就落盘！！
2. `everysec` 每秒落盘一次。(默认)
3. `no` 交给操作系统进行落盘。

 ### 2. `AOF`文件(文本文件)

就是一个文本文件，记录每次操作命令。

当`AOF`文件内容持续增多时会有重写机制：把同一类命令进行合并整理，删除已经不需要的命令......==因为我们只是需要`redis`内数据的最终状态，中间过程并不关心。==这样可以压缩`AOF`文件的大小，提升重启时读取恢复的速度。

**其实重写时就是把当前`redis`内存中的数据进行“命令化”，然后替换原有的文件，而不是去整理已有的`AOF`文件，显然这样做更简单！！**这就和`RDB`机制差不多了，记录快照。

重写机制也有手动触发和被动触发两种：(也是创建子进程进行重写，不会阻塞`redis`服务)

1. 手动触发：调用 `bgrewriteaof`命令。

2. 自动触发：设置配置文件中的触发时机。

   ```shell 
   auto-aof-rewrite-min-size
   auto-aof-rewrite-percentage
   ```

重写流程：注意父进程仍然在处理客户端请求，会==将新的操作写到两个缓冲里，==一个同步到旧的`AOF`文件，一个同步到新的`AOF`文件(子进程重写完成后)。这样就能做到实时备份了！

## 两种备份方式的比较

**没有好坏之分，只有适合与不适合！！**但是当`RDB`和`AOF`持久化策略都开启时以`AOF`为主！！

其实在`redis`中有结合了两个持久化的特点进行“混合持久化”的倾向！！比如：在`AOF`重写后是按照`RDB`的二进制文本方式进行的重写的！！！然后再把缓冲区的记录按照文本文件的方式追加到`AOF`文件后面。

