# `proto3`语法

## 1. 字段规则

主要有两种规则，分别对应两个关键字：`singular`和`repeated`

`singular`：表示`message`中可以包含该字段零次或一次（不超过一次）。proto3语法中，字段**默认**使用该规则。  

`repeated`：表示`message`中可以包含该字段任意多次（包括零次），其中重复值的顺序会被保留。可以理解为定义了一个**数组**。  

## 2. 消息类型的定义与使用

1. 在一个`.proto`文件中可以定义多个`message`，一个`message`里就是一个作用域，所以不同的`message`里字段编号可以重复。
2. 一个`message`也可以当作一个类型使用。
3. 在一个`.proto`文件中可以导入其他的`.proto`文件，这样就可以使用其他文件中的`message`了。

```protobuf
syntax = "proto3";
package = "aaa";
// 引入其他的.proto文件
import "xxx.proto"
// ...
```

## 3. `enum`类型

## 4. `Any`类型

## 5. `oneof`类型

## 6. `map`类型

## 7. 默认值

## 8. 更新消息

## 9. `option`选项





