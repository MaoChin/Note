# `Redis`客户端程序

在实际开发中更多的是用 “专用的”，“定制化的” 客户端程序来操作 `redis`，而不是那个命令行客户端。

`Redis`客户端和服务端是通过网络通信的，这就势必会用到各层网络的协议。`Redis`的应用层协议是自己定制的一个协议(RESP)，传输层是使用的`TCP`。这样客户端和服务器都使用RESP这个应用层协议就可以实现通信了。 

` RESP->redis serialization protocol`

题外话：逆向。正常的顺序都是根据源代码编译出可执行程序，而逆向就是根据可执行程序分析出源代码。

## RESP协议细节

![image-20230925161725398](E:\Note\Redis\Redis客户端程序.assets\image-20230925161725398.png)

## C++客户端--`redis-plus-plus`(第三方)

### 1. 环境安装

```shell 
apt install libhiredis-dev  # 先安装依赖

# 源码编译安装
git clone https://github.com/sewenew/redis-plus-plus.git
cd redis-plus-plus

# 防止编译产生的中间文件污染目录
mkdir build
cd build

# cmake得到Makefile文件
cmake ..
make

# 这⼀步操作需要管理员权限. 如果是⾮ root ⽤⼾, 使⽤ sudo make instal
# 把编译后得到的动静态库拷贝到系统目录下，用到的时候直接就能找到,包含时使用<>就可以
make install 
```

安装成功后,会在`/usr/local/include/` 中多出` sw `(这是作者的名字缩写!!)⽬录，并且内部包含`redis-plus-plus`的⼀系列头⽂件。会在 `/usr/local/lib/ `中多出⼀系列` libredis `库⽂件.  

### 2. 客户端连接`redis`服务器

包含关键头文件：`/usr/local/include/sw/redis++/redis++.h`

库文件：`/usr/local/lib/libredis++.a` 

**(忘了使用`find`命令查找)**  ：`find /usr -name redis*`

```C
#include <sw/redis++/redis++.h>

// 编译的时候也要引入第三方库：redis++的库，hiredis库和pthread库
g++ redis_client.cc -o redis_client -std=c++17 -lpthread -lredis++ -lhiredis
```

测试用`redis-plus-plus`操作`redis`。就是执行之前学习到的那些通用命令和针对各个类型的命令。

可以发现，`redis-plus-plus`的函数名称和`redis`本身命令行的执行命令基本一致。只是处理一下参数和返回值即可。 

几点通用：

```C++
// 1. sw::redis++::OptionalString类型
// value1是sw::redis++::OptionalString 类型，支持无效的取值，就是无效值。是作者自己写的，不支持 << 运算
// Optional这个类型在c++14才引入标准库
auto value1 = redis.get("key1");
if(value1)  // 当get的key不存在时value1有可能为空！无效值
	cout << value1.value() << endl;

// 2. 自己构造尾插/头插迭代器作为输出型参数，在返回多个值时这个非常常见！！！
vector<string> output;
// 设置成尾插迭代器
auto it = back_inserter(output);
// 这里第二个参数就是迭代器，使用back_inserter作为尾插迭代器---解耦合
redis.keys("*", it);

// 3. 与时间相关
// 这样的系统调用与系统强相关！所以在跨平台方面并不好！好的方法是调用提供的库函数
// sleep(3);
std::this_thread::sleep_for(std::chrono::seconds(2)); // 这样可以
// 或者引入命名空间后使用字面值常量！！ c++14
using namespace std::chrono_literals;
std::this_thread::sleep_for(1s);    // 直接 1s 

// 4. 当有多个输入时可以使用{...}这种方式初始化，也可以传迭代器
redis.del({"key1", "key2", "key3"});
// 或者这样
vector<string> in{"key1", "key2", "key3"};
redis.del(in.begin(), in.end());
```

==注意==：在云服务器以及生产环境下，`redis`的6379端口最好不要暴露在公网！！！就是不要关闭这个端口的防火墙。因为这个端口非常容易被骇客攻击。而`redis`作为数据库一般存储很多重要数据，会造成不可估计的损失！！！

但是我们自己也想在本地机器访问远程的`redis`怎么办？？？

**解决：配置`ssh`端口转发，把云服务器的`redis`端口映射到本地主机。**

`ssh`端口转发：`ssh`是一个远程登陆的协议，端口默认是22，这个协议相对安全，不容易被骇客入侵。==端口转发就是指通过`ssh`的22号端口来传递其他端口的数据。==就相当于让22号端口来干活，当中介，把发给6379端口的数据先发给22号端口，再转交给6379端口。

然而有可能`ssh`端口要为多个其他端口进行转发！！就可以进行端口映射：比如把云服务器上的6379号端口映射到我本机的8080端口，这样我只需要访问 127.0.0.1 : 8080；就相当于是在访问 xxx.xx.xx.xx(云服务器IP) : 6379 了。

**这样经过配置后访问远程服务器就相当于是在访问本机端口一样了！！并且没有闭服务器端口的防火墙，确保了安全。**

`ssh`端口转发 == `ssh`端口映射 == `ssh`隧道

具体配置：

![image-20230929102023579](E:\Note\Redis\Redis客户端程序.assets\image-20230929102023579.png)
