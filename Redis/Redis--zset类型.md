# `Redis`--`zset`类型

有序集合：元素 不允许重复，但是 是==按照升序/降序== 而有序的 (`zset` 是使用升序来排列的)。

有序比较基准：`zset`里的 每一个元素都会附带一个浮点类型的 `score`，根据这个 `score`，来排序。

这个 `score` 可以重复。`score`和`member`关系就像 `pair`；既可以通过`member`找到`score`，也可以通过`score`找到`member`。

## 基本命令

### 1. `zadd`

**添加/修改元素。**

`NX`：`member` 不存在时才设置。(not exists)

`XX`：`member` 存在时才设置，更新。(exists)

`LT`：只有当`member`对应的新的`score`小于这个`member`对应的原有的`score`时才更新；或者`member`不存在时进行设置。(less than)

`GT`：只有当`member`对应的新的`score`大于这个`member`对应的原有的`score`时才更新；或者`member`不存在时进行设置。(greater than)

`CH`：使返回值 加上 本次操作更新的元素个数。默认情况下返回值只是新添加的元素的个数。(change)

`INCR`：加上这个选项后就类似于 `zincrby` 命令，将特定元素的分数加上 `score`，此时只能对一个`member`进行操作。

```shell
# 时间复杂度O(logN)  N是集合中的元素个数
zadd key [NX|XX] [LT|GT] [CH] [INCR] score member [score member ...]
```

### 2. `zrange`

查看有序集合中的元素详情。

```shell
# [start end] 左闭右闭  最后的选项表示打印出对应的score
zrange key start end [withscores]
```

### 3. `zcard` && `zcount`



### 4. 

### 5. 



## `zset`类型内部编码



## `zset`类型的应用场景

