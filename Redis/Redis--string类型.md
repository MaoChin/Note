# `Redis`--`string`类型

类型是针对`value`的，`key`的类型只能是`string`！！

`string`类型是基石，其他类型都是建立在`string`类型上的。

`Redis`的字符串直接按照二进制的方式存储，不会做任何编码转换。存的是啥，取出来的就是啥。 

## 基本命令

### 1. `set`  && `setex`  &&  `psetex` && `setnx`

若`key`存在就覆盖，之前`key`的`TTL`也失效。不存在就创建。

```shell
set key value [EX seconds | PX milliseconds] [NX|XX]

# setex key 10 value
set key value ex 10  # 定义的同时设置过期时间为10s
# psetex key 10 value
set key value px 10  # 定义的同时设置过期时间为10ms
 
# setnx key value
set key value NX     # key不存在才设置，key存在就不设置(返回nil)
# 没有setxx
set key value XX     # key存在才设置，覆盖原来的key
```

### 2. `get`

获取`key`对应的`value`。**只能获取`string`类型对应的`value`！！**

```shell
get key
```

### 3. `mset`

一次设置多个`key`。`Redis`的`client`和`server`通过网络传输。一次设置多个可以减少网络传输的次数，提高效率。(网络I/O还是很慢的)。**`Redis`中很多操作都可以批量进行。**

但是也不要一次设置太多`key`，**会导致`client`处理很长时间而使得其他的命令被阻塞住。**

```shell
mget key value [key1 value1 ...]
```

### 4. `mget`

一次获取多个`value`

```shell
mget key [key1 ...]
```

### 5. `incr` && `incrby`

**对应的`value`必须是整型的字符串。如 ”111“**

若`key`不存在，则将其插入，且`value`是1 (0 + 1) 

`incr`使`value` + 1

`incrby`使`value` + 指定的数

```shell
incr key
incr key 10
```

### 6. `decr` && `decrby`

和上一个基本一样。

`decr`使`value` - 1

`  decrby`使`value` - 指定的数

```shell
decr key
decrby key 10
```

### 7. `incrbyfloat`  (很少用)

**对应的`value`必须是浮点型的字符串。如 ”1.9“**

若`key`不存在，则将其插入，且`value`是 0 + 指定的数

使`value` +/-  指定的浮点数

```shell
incrbyfloat key 0.9   # +0.9
incrbyfloat key -0.1  # -0.1
```

### 8. `append`

字符串追加，返回当前`value`的长度(字节数)。(`UTF-8`下一个汉字三个字节，`unicode`下一个汉字两个字节。)

```shell
# 把value追加到key已有的value后面，key不存在就相当于设置
append key value
```

### 9. `getrange ` && `setrange`

`getrange`获取 [start, end] 区间的子串，其中 -1 代表倒数第一个字符，-2代表倒数第二个字符...

如果`value`里保存的是汉字，那么这样获取子串是未定义的！

```shell
# 获取value下标是 0~3 的子串(左闭右闭)
getrange key 0 3
```

`setrange`从指定偏移量(`offset`)开始，使新的`value`替代原来的`value`(中的一部分)

若`key`不存在，就自动生成一个`value`，把`offset`之前的内容填充成 0x00

```shell
# setrange key offset value 
setrange key 3 "aaa"   # 3 是下标(从0计算)
```

### 10. `strlen`

获取`value`字符串的长度，单位是字节。

在`MySQL`的`varchar(N)`中`N`代表的是字符，而非字节！

```shell
strlen key
```

## `string`类型内部编码

### 1. `int`

适用于8字节的长整型。

### 2. `embstr`

适用于长度较短的字符串。小数的内部编码是字符型！

### 3. `raw`

通用的字符串。

查看内部编码：

```shell 
object encoding key
```

 ## `string`类型的应用场景

### 1. 用作缓存

![image-20230912215821668](E:\Note\Redis\Redis--string类型.assets\image-20230912215821668.png)

**此时随着时间的推移，会不断有要访问的数据不在缓存里，就不断会有数据写到缓存，那缓存中的数据就会越来越多！！解决：**

1. 在把数据写到`Redis`时给这个`key`设置过期时间，过期就自动删除了(惰性删除策略和定期删除策略)。
2. `Redis`在内存不足时也有淘汰策略，释放内存。

### 2. 计数功能

例如视频网站中视频播放次数，点赞次数......

### 3. 存储共享会话(`Session`)

`Cookie`(浏览器存储数据的机制)

`Session`(服务器存储数据的机制)，存储客户端和服务器交互过程中专属于某个客户端的数据。但是很多服务器都是分布式的，这个客户**下次有可能会访问到其他的机器，但是这个这个客户的相关数据在另一个机器上！！**

解决：使用`Redis`将`Session`信息集中管理，集群中所有机器共享这些信息：

![image-20230913172616230](E:\Note\Redis\Redis--string类型.assets\image-20230913172616230.png)

### 4. 手机验证码

借用`Redis`的数据过期机制。



