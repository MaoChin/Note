# `Redis`初识























## 启动

在启动`Redis`客户端时加上`--raw`就可以使其尝试翻译二进制数据。

```shell
redis-cli --raw
```



## 命令标签(权限相关)

`ACL catagories`：`access control list catagories`

`Redis6`以后引入，给每个命令都打上标签，这样`root`用户就可以根据标签给每个用户配置其只能使用某些标签下的命令！！

