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

## 8. 更新消息

## 9. `option`选项





