# `Redis`--`list`类型

有点像`C++`的`vector`，可以 ==O(1) 的进行 头插/头删/尾插/尾删==，也可以==通过下标获取对应的元素(O(N))==。可以充当栈和队列，其底层有点像`deque`。

`list`里每个元素都是`string`类型。一个`list` 里的元素可以重复。

元素是有序的：[1,2,3]和[3,2,1] 是不同的列表。

题外话：很多时候同一个词有很多种理解！！如：**栈/堆(数据结构里的，操作系统里的，`JVM`里的)**；**同步(多线程里的互斥与同步，I/O中的同步与异步)**

## 基本命令

### 1. `lpush` && `lpop`

`l`：`left`，从左侧插入删除，也就是头插头删

```shell
# 支持头插多个元素，依次头插，最后头插的元素在最前面。key不存在会创建一个
lpush key element [element ...]

# count 表示删除几个元素 redis6.2以后支持
lpop key [count]
```

### 2. `rpush` && `rpop`

`r`：`right`，从右侧插入删除，尾插尾删

```shell
# 支持尾插多个元素，依次尾插，最后尾插的元素在最后面。key不存在会创建一个
rpush key element [element ...]

rpop key [count]
```

### 3. `lrange`

`l`：`list`

查看某一范围的元素，支持负数。(-1表示倒数第一个元素......)

如果下标非法，就尽可能的满足要求，并不会出错。(更像`python`)

```shell
# [begin, end] 下标从0开始
lrange key begin end
```

### 4. `lpushx` && `rpushx` 

**`key`存在时才插入，否则不会自己创建**，直接返回。

```shell
lpushx key element [element ...]
rpushx key element [element ...]
```

### 5.  `lindex`

通过下标获取元素。O(N)  N是list中的元素个数。

```shell 
lindex key index
```

### 6. `linsert`

在指定位置插入元素。O(N)

```shell 
# 在指定元素 pivot 前/后 插入元素element
# 当元素 pivot 有多个时，就从左向右找第一个 pivot 进行插入 
linsert key begin|after pivot element
```

### 7.  `llen`

获取`list`的元素个数。

```shell
llen key
```

### 8. `lrem`

删除元素。

```shell
# count == 0 删除所有的element
# count > 0 从左向右删除count个 element
# count < 0 从右向左删除count个 element
lrem key count element
```

### 9. `ltrim`

按范围删除元素。

```shell 
# 只保留[start, end] (左闭右闭) 区间的元素，把两边的删除
ltrim key start end
```

### 10.  `lset`

根据==下标==修改元素。O(N)

下标越界就报错。

```shell
# 将index下标处的元素修改成 element
lset key index element
```

### 11. `blpop`  &&  `brpop` (少用)

阻塞式的`pop` 。**当 `pop`时该`list`是空的，就会阻塞等待一段时间(可以自己设置)。**

主要用来作为 消息队列 使用。

```shell
# 可以对多个key进行pop，从第一个key开始找,一旦有一个list里有元素可以pop就返回,后面的key不会进行删除

# 若多个客户端都阻塞等待一个key，那么当key有数据时最先调用的那个客户端会获得pop权，解除阻塞

# timeout 为阻塞时间，单位是秒，redis6以后支持设置成小数。
blpop key1 [key2 ...] timeout
brpop key1 [key2 ...] timeout
```

## `list`类型内部编码

`redis4`之前是 `ziplist`和`linkedlist`两类。`ziplist`节省空间，但是操作效率低，当数据较少时使用。

`redis4`之后合并了，只有一个 `quicklist`，这整体是一个`linkedlist`，其中每一个元素是一个 `ziplist`。保证每个`ziplist`存储元素不多，再整体连接起来。

## `list`类型的应用场景

### 1. 作为消息队列(作为 ==阻塞式生产者消费者模型==)

在实际中 消息队列 有专业的软件，即使是用`redis`来作为消息队列，也不会使用`list`类型，而是用`stream`类型。

这里借助 `lpush` 和`brpop` (或者反过来) 就可以实现**基于轮询方案的 阻塞式生产者消费者模型。**

当有多个`list`作为交易场所时就可以实现分频道的消息队列。

![image-20230920164827993](E:\Note\Redis\Redis--list类型.assets\image-20230920164827993.png)

### 2. 用作数据列表

相当于`vector`的用处。
