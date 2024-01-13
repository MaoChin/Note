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

##### `Linux`下二进制文件查看

```shell
# hexdump可以将二进制⽂件转换为ASCII、八进制、十进制、十六进制格式进⾏查看。
# 表示每个字节显示为16进制和相应的ASCII字符
hexdump -C xxx.bin
```

`protobuf`也提供了编译选项(`--decode`)来将二进制文件转换成文本文件，方便阅读！！

```protobuf
// 需要指定命名空间下的message和.proto文件
// 默认是从标准输入读取，所以最后还要重定向输入
protoc --decode=contacts.Contacts contacts.proto < contacts.bin
```

## 3. `enum`类型(常量)

基本和C的一样，==但是必须从0开始(C可以自己设置，默认从0开始)==。另外，`enum`类型可以在`message`外定义，也可以在`message`内定义。

枚举的值必须在32位整数内，且不建议设置负值。

```protobuf
enum xxx{
	A = 0;   // 不设置时，这个是默认值
	B = 1;
	C = 2;
}
```

注意：同级（同层）的枚举类型，各个枚举类型中的常量不能重名。  

## 4. `Any`类型

可以理解为==泛型类型==，可以在`Any`中存储任意消息类型，也可以用`repeated`修饰。

这个类型是`protobuf`中定义好的类型。

`Any`类型的设置可以使用`mutable_...`方法，直接返回`Any`类型的指针，并且已经开辟好了空间，可以直接进行操作。

其他消息类型和`Any`类型的转换：

```C++
class PROTOBUF_EXPORT Any final : public ::PROTOBUF_NAMESPACE_ID::Message {
  // 将其他消息类型转换成Any类型
  bool PackFrom(const ::PROTOBUF_NAMESPACE_ID::Message& message) {
    ...
  }
  // 将Any类型转换回其他消息类型
  bool UnpackTo(::PROTOBUF_NAMESPACE_ID::Message* message) const {
    ...
  }
  // 判断存放的消息类型是否为 typename T
  template<typename T> bool Is() const {
    return _impl_._any_metadata_.Is<T>();
  }
};
```

## 5. `oneof`类型

==有多个可选字段，并且同时只能设置一个字段==！这时使用`oneof`就很合适，还可以节约内存。(有点像C中的`union`)

```protobuf
massage xxx{
	// ...
	
	// 从多个里面选一个，注意字段唯一编号不能和message里的冲突
	oneof other_contact{
		string qq = 10;
		string weichat = 11;
		// ...
	}
}
```

注意事项：

1. `oneof`里的字段不能使用`repeated`修饰。
2. 在设置`oneof`字段中值时，如果将`oneof`中的字段设置多个，那么只会保留最后一次设置的成员，之前设置的`oneof`成员会自动清除。  

## 6. `map`类型(哈希)

类似`C++`的`unordered_map`。

```protobuf
// map<key,value>
map<string, string> xxx = 1;
```

注意事项：

1. `key_type`是除了`float`和`bytes`类型以外的任意标量类型。 `value_type` 可以是任意类型。  
2. `map`字段不可以用`repeated`修饰。
3. map中存入的元素是无序的。(哈希)

## 7. 默认值

反序列化消息时，**如果被反序列化的二进制序列中不包含某个字段**，这时反序列化后对象中相应字段，就会设置为该字段的默认值。这个默认值基本和C++的默认值差不多。

1. 对于枚举，默认值是第⼀个定义的枚举值，必须为0。  
2. 字符串就是空串，数值类型就是0，`bool`类型就是`false`。
3. `repeated`修饰的字段就是空列表。

对于标量数据类值默认值是0的问题：反序列化后不知道这个0是当时二进制字节序列中没有设置，默认生成的；还是二进制字节序列中设置了这个值就是0 ？？？但是这个问题基本不会有啥影响，所以也没啥大问题。

## 8. 更新消息(新增/修改/删除字段)

就是现有的消息类型不能满足需求了，就需要新增/修改/删除消息。需要在不破坏任何现有代码的情况下更新消息类型！

简单总结：

1. ==新增==要保证字段名和字段唯一编号不冲突。
2. 不能==修改==已有字段的字段编号。
3. ==删除==时使用关键字`reserved`。

### 1. 更新规则

1. **禁止修改任何已有字段的字段编号。**
2. 若是移除老字段，要保证不再使用移除字段的字段编号。正确的做法是==保留字段编号（`reserved`）==，以确保该编号将不能被重复使用。不建议直接删除或注释掉字段。
3. 还有很多类型兼容规则，包括标量数据类型和特殊类型。这里就不记录了，需要的话可以查阅资料。

### 2. 保留字段(`reserved`)  

如果通过删除或注释掉字段来更新消息类型，未来的用户在添加新字段时，**有可能会使用以前已经存在，但已经被删除或注释掉的字段编号**。将来在使用该`.proto`的旧版本时的程序会引发很多问题：==数据损坏、隐私错误==等等。  

正确的做法是==使用 `reserved` 将指定字段的编号或名称设置为保留项==，确保这些编号不能被重复使用。

```protobuf
message xxx{
	// 设置保留项，确保这些字段编号不能被重复使用
	reserved 100, 101, 200 to 150;   // 使用字段唯一编号设置
	reserved "name", "age";  				 // 使用字段名设置
}
```

### 3. 未知字段

简单来说就是因为对消息字段的各种更新操作使得反序列化二进制字节序列时得到了很多字段，**但是有些字段已经不再是类对象的属性信息了**！！这种字段数据就叫做未知字段。

未知字段：解析结构良好的`protocol buffer`**已序列化数据中的未识别字段的表示方式**。例如，当旧程序解析带有新字段的数据时，这些新字段就会成为旧程序的未知字段。  

#### 1. 未知字段的获取

![image-20240113152600450](E:\Note\protobuf\proto3语法.assets\image-20240113152600450.png)

主要是`UnknowFieldSet`和`UnknowField`类中。

未知字段可以识别的类型：

```C++
enum Type {
  // 可变长的整型
  TYPE_VARINT,  
  // 32位定长整型
  TYPE_FIXED32,
  // 64位定长整型
  TYPE_FIXED64,
  // string类型
  TYPE_LENGTH_DELIMITED,
  // set类型，还是未知字段的集合
  TYPE_GROUP
};
```

### 4. 前后兼容性

`protobuf`具有前后兼容性。

向前兼容：老模块能够正确识别新模块生成或发出的协议。这时**新模块新增的属性字段会被当作是未知字段。 **

向后兼容：新模块也能够正确识别老模块生成或发出的协议。  

前后兼容的作用：当我们维护一个很庞大的分布式系统时，由于无法同时升级所有模块，为了保证在升级过程中，整个系统能够尽可能不受影响，就需要尽量保证通讯协的“向后兼容”或“向前兼容”。  

## 9. `option`选项

选项能==影响`proto`编译器的某些处理方式==。主要分为文件级选项，消息级选项，字段级选项等。

常用选项列举：

1. `optimize_for`：是一个文件级选项，**设置`protoc`编译器的优化级别**。有三个优化级别：`SPEED(默认的，追求效率，空间大)`，`CODE_SIZE(节省空间，效率低)`，`LITE_RUNTIME(兼具效率高和空间小，但是牺牲了反射功能)`，针对不同的优化级别，编译`.proto`文件后生成的代码会有不同。
2. `allow_alias`：枚举选项，允许将相同的常量分配给不同的枚举常量，用于定义别名。

```protobuf
enum PhoneType {
	// 允许别名
  option allow_alias = true;
  MP = 0;
  TEL = 1;
  // 别名
  LANDLINE = 1; // 若不加 option allow_alias = true; 这一行会编译报错
}
```



​	
