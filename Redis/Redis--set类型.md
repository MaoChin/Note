# `Redis`--`set`类型

保存多个`string`类型。但是**不允许重复元素。**会自动去重。

元素是无序的：[1,2,3]和[3,2,1]是同一个集合。

## 基本命令

### 1. `sadd`  &&  `spop`

注意`spop`是==随机删除元素。==

```shell
sadd key member [member1 ...]

# 从集合中随机删除 count个元素！！
spop key [count]
```

### 2.  `smembers`  && `sismember`

```shell
# 列出 key 中的所有 member
smembers key
# 判断 member是否在key里
sismember key member 
```

还有一个 `srandmember` 随机返回集合里的一个元素。

### 3. `scard`

查看集合中元素个数。

```shell
scard key
```

### 4. `smove`

将一个集合里的一个元素移动到另一个集合里。

```shell
smove source destination member
```

### 5. `srem`

删除集合中指定元素。

```shelll
srem key member [member ...]
```

### 6. `sinter`  &&  `sinterstore`

求多个集合的==交集。==

```shell
sinter key1 [key2 key3 ...]

# 这个命令把返回的结果另保存到 dest 这个集合中。
sinterstore destination key1 [key2 key3 ...]
```

### 7.  `sunion`  &&  `sunionstore`

求多个集合的==并集。==

```shell
sunion key1 [key2 key3 ...]

# 这个命令把返回的结果另保存到 dest 这个集合中。
sunionstore destination key1 [key2 key3 ...]
```

### 8.  `sdiff`  &&  `sdiffstore`

求多个集合的==差集。==

```shell
sdiff key1 [key2 key3 ...]

# 这个命令把返回的结果另保存到 dest 这个集合中。
sdiffstore destination key1 [key2 key3 ...]
```

## `string`类型内部编码

有 `intset` 和 `hashset`两个。当集合中元素都是整数且元素个数较少时使用 `intset`，更节省内存。

其他情况下是 `hashset` 。

## `string`类型的应用场景

### 1. 保存用户 “标签“(tag)

不能重复，容易计算 交集/并集/差集 都是集合类型的优点。

### 2. 统计 `UV`

互联网公司通过 `PV` (`page view`) 和 UV (`user view`)  指标来衡量其产品的 用户量/用户规模。

用户只要访问一次服务器，就会产生一次`PV`；而一个用户多次访问 只会产生一个`UV`。

所以可以利用集合的 ”元素不允许重复” 的属性统计 `UV`。
