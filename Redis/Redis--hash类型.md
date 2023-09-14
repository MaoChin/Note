# `Redis`--`hash`类型

==最重要的数据结构，没有之一！！==

## 基本命令

### 1. `hset` && `hget`

```shell
# field 对应的value只能是string类型
hset key field value [field value ...]

hget key field
```

### 2. `hexists`

判断`key`里的`field`是否存在。

```shell
hexists key field
```

### 3. `hdel`

删除`key`里的`field`。**(`del` 是直接删除`key`)**

```shell
hdel key field [field ...]
```

### 4. `hkeys` && `hvals  `  && `hgetall`

`hkeys`获取`key`中的所有`field`。

`hvals`获取`key`中的所有`value`。

`hgetall`获取`key`中的所有`field`和`value`。

==(有风险，若`key`中有大量的`field`，会导致`Redis`服务器被阻塞！！ `keys *`命令也是一样的。)==

可以使用`hscan`命令获取所有的`field`，他采用渐进式遍历，就是一次遍历一部分，在执行一次遍历下一部分(==化整为零==)......可以防止`Redis`服务器被阻塞。

```shell
hkeys key
hvals key
hgetall key
```

### 5.  `hmget`

一次查询一个`key`里的多个`field`。**(`hset`可以一次设置多个`field`，但是`hget`只能获取一个`field`)**

```shell
hmget key field1 [field2 ...]
```

### 6. `hlen`

获取`key`中`field`的个数。

```shell 
hlen key
```

### 7. `hsetnx`

在`field`不存在时设置其`field--value`，`key` 中存在相应的`field`时设置失败。

```shell
hsetnx key field value
```

### 8. `hincrby` && `hincrbyfloat`

将`field`对应的`value`加减整数/小数。

```shell
hincrby key field 10
hincrby key field -10
hincrbyfloat key field 0.2
hincrbyfloat key field -0.2
```

### 9. `hstrlen`

获取value字符串的长度，单位：字节。

```shell
hstrlen key field
```

## `hash`类型内部编码

### 1.  `ziplist`

在`field`个数较少 并且每个`value`不长时使用，节省内存。

### 2. `hashtable`

标准的哈希表。

查看内部编码：

```shell 
object encoding key
```

## `hash`类型的应用场景

### 1. 用作缓存

哈希类型存储会==更直观==，在更新操作时也会==更灵活。但是内存使用量会更大。==

如果用`string`类型存储相应的数据就要用到`json`串，修改很麻烦，要转换成哈希类型修改，然后再写成`json`串(但是`json`方式更节省空间)！！而直接使用哈希类型就很灵活。









