# `Redis`--`zset`类型

有序集合：元素 不允许重复，但是 是==按照升序/降序== 而有序的 (**`zset` 是使用升序来排列的**)。(序列号也会被保存)

有序比较基准：`zset`里的 每一个元素都会附带一个浮点类型的 `score`，根据这个 `score`，来排序，==当多个元素分数相同时会根据元素的字典序排序==。`score`支持 inf(无穷大) 和 `-inf`(负无穷大)

这个 `score` 可以重复。`score`和`member`关系就像 `pair`；既可以通过`member`找到`score`，也可以通过`score`找到`member`。

## 基本命令

### 1. `zadd`

**添加/修改元素。**

`NX`：`member` 不存在时才设置。(not exists)

`XX`：`member` 存在时才设置，更新。(exists)

`LT`：只有当`member`对应的新的`score`小于这个`member`对应的原有的`score`时才更新；或者`member`不存在时进行设置。(less than)

`GT`：只有当`member`对应的新的`score`大于这个`member`对应的原有的`score`时才更新；或者`member`不存在时进行设置。(greater than)

`CH`：使返回值 加上 本次操作更新的元素个数。**默认情况下`zadd`返回值只是新添加的元素的个数。**(change)

`INCR`：加上这个选项后就类似于 `zincrby` 命令，将特定元素的分数加上 `score`，此时只能对一个`member`进行操作。

```shell
# 时间复杂度O(logN)  N是集合中的元素个数
zadd key [NX|XX] [LT|GT] [CH] [INCR] score member [score member ...]
```

### 2. `zrange`  

根据序列号(下标)查看有序集合中的元素详情。O(log(N) + M) 找到一个元素后要遍历找另一个。

```shell
# [start end] 左闭右闭  最后的选项表示打印出对应的score
zrange key start end [withscores]
```

### 3. `zcard` && `zcount`

```shell
# 获取集合中元素的总个数 O(1)
zcard key

# 获取集合中score在 [min, max](左闭右闭)之间的个数 O(logN)
zcount key min max
# 左开右开！！！
zcount key (min (max
```

### 4. `zpopmax`  &&  `zpopmin`   

```shell 
# O(log(N)*M) 这里是有优化空间的，可以优化成 O(M)

# 删除集合中score最大的count个元素 
zpopmax key [count]
# 删除集合中score最小的count个元素
zpopmin key [count]
```

### 5. `bzpopmax`  &&  `bzpopmin`

```shell
# 阻塞式的删除，有序集合为空的时候就阻塞等待
# 可以同时删除多个元素，有一个执行成功就返回 O(logN)
bzpopmax key [key1 ...] timeout
bzpopmin key [key1 ...] timeout
```

### 6. `zrank`  &&  `zrevrank`

```shell
# 获取member在升序序列中的排名(下标) O(logN)
zrank key member
# 获取member在降序序列中的排名(下标)
zrevrank key member
```

### 7. `zscore`

```shell
# O(1) 这里内部做了特殊处理，付出了空间的代价！
# 根据member获取其分数
zscore key member
```

### 8. `zrem`&& `zremrangebyrank`&&`zremrangebyscore`

```shell
# 删除集合里的特定元素 O(log(N) * M)
zrem key member [member1 ...]

# 删除升序序列(下标)中特定范围的元素 [start, end]  O(log(N) + M)
zremrangebyrank key start end

# 删除score在指定区间的元素 [min, max]  O(log(N) + M)
zremrangebyscore key min max 
# (min, max]
zremrangebyscore key (min max 
```

### 9. `zincrby`

```shell
# 给member对应的score加上increment
zincrby key increment member
```

### 10. `zinter`  && `zinterstore`

计算多个集合的交集。

`zinter` 在 `redis6.2` 之后才有。

```shell 
# weight:表示给每一个集合赋予的权重
# aggregate:表示聚合方式,就是最后保存的是 sum/min/max

ZINTER numkeys key [key ...] [WEIGHTS weight [weight ...]]
  [AGGREGATE <SUM | MIN | MAX>] [WITHSCORES]
  
# 结果存储到dest里；共numkeys个集合参与运算  
ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight
  [weight ...]] [AGGREGATE <SUM | MIN | MAX>]
```

### 11. `zunion`  &&  `zunionstore`

计算多个集合的并集。

`zunion` 在 `redis6.2` 之后才有。

```shell
ZUNION numkeys key [key ...] [WEIGHTS weight [weight ...]]
  [AGGREGATE <SUM | MIN | MAX>] [WITHSCORES]
  
ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight
  [weight ...]] [AGGREGATE <SUM | MIN | MAX>]
```

### 12. `zdiff`  && `zdiffstore`

计算多个集合的差集。

这两个都是在 `redis6.2` 之后才有。

## `zset`类型内部编码

有`ziplist`和`skiplist`两种，当单个元素较小并且总体元素较少时使用`ziplist`作为内部编码实现，更节省空间。否则使用`skiplist`---效率更高。

## `zset`类型的应用场景

### 1. 排行榜系统

如 热搜/天梯榜/成绩榜......

针对需要 结合多个方面综合进行评价的榜单，可以借助集合间操作中的 `weight` 给每个方面赋予权重，然后综合比较。





