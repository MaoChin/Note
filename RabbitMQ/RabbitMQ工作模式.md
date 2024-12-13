# `RabbitMQ`工作模式

#### 1. `Simple`

![image-20241209170705958](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209170705958.png)

一对一，点对点。这种模式下**`Exchange`也是存在的**，使用的是默认的`Exchange`，作用仅仅是简单的消息转发！！

#### 2. `Work Queue`

![image-20241209170747523](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209170747523.png)

一个生产者，多个消费者，==消息不会重复！！==也就是说，同一条消息，要么发给消费者1，要么发给消费者2。

这种模式下**`Exchange`也是存在的**，使用的是默认的`Exchange`，作用仅仅是简单的消息转发！！

#### 3. `Publish/Subscribe`

![image-20241209170936060](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209170936060.png)

`X`表示交换机（`Exchange`）。

生产者将消息发送到`Exchange`，由交换机将消息按一定的规则路由到一个或多个队列中。`Exchange`只负责转发消息，不具备存储消息的能力，因此如果没有任何队列与`Exchange`绑定，或者没有符合路由规则的队列，那么消息就会丢失。

路由规则：

1. `Fanout`：广播，将消息==交给所有绑定到交换机的队列==(`Publish/Subscribe`模式)  
2. `Direct`：定向，把消息交给符合指定`routingkey`的队列(下述`Routing`模式)  
3. `Topic`：通配符，把消息交给符合`routing pattern`(路由模式)的队列(下述`Topics`模式)  
4. `headers`类型的交换机，很少用。

#### 4. `Routing`

![image-20241209171553247](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209171553247.png)

发布订阅模式是无条件的将所有消息分发给所有消费者，路由模式是`Exchange`根据`Routing Key`的规则，将数据筛选后发给对应的消费者队列。  

`Routing Key`：生产者发布消息时携带的`key`，给`Exchange`使用标识发送给哪一个`Queue`。

`Binding Key`：`Exchange`标识`Queue`的`key`。（`Binding Key`也是`Routing Key`的一种）

![image-20241209173846832](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209173846832.png)

#### 5. `Topics`

![image-20241209171650826](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209171650826.png)

路由模式的升级版，在`routingKey`的基础上，增加了通配符的功能，使之更加灵活。就是使用通配符匹配（有点类似正则表达式）。

#### 6. `RPC`

![image-20241209171830090](E:\Note\RabbitMQ\RabbitMQ工作模式.assets\image-20241209171830090.png)

在`RPC`通信的过程中，没有生产者和消费者，比较像`RPC`远程调用，大概就是通过两个队列实现了一个可回调的过程。  

#### 7. `Publisher Confirms`

`Publisher Confirms`模式是`RabbitMQ`提供的一种**确保消息可靠发送到`RabbitMQ`服务器**的机制。在这种模式下，生产者可以等待`RabbitMQ`服务器的确认，以确保消息已经被服务器接收并处理。（**确认应答，生产者和`broker`之间的可靠传输，并且策略和`TCP`的确认应答很像**）  该确认机制是==异步==的，生产者在发送消息的同时可以等待`broker`返回`ack`。

==消息丢失是消息队列面临的一个重要问题==，消息丢失主要有三种方式：

1. 生产者没有正确将消息发送到`broker`（比如网络等原因）。
2. `broker`没有将消息保存好导致消息丢失（通过持久化机制解决）。
3. `broker`将消息发送给了消费者然后将消息删除，但是消费者并没有收到消息。

`Publisher Confirms`主要是解决上述问题1的，即生产者到`broker`这一段。后面两种问题的解决见特性（一）笔记。

`Publisher Confirms`有两个方式，一是`confirm`确认，二是`return`回退：

##### `confirm`确认

==生产者到`Exchange`阶段。==

生产者在发送消息时设置`ConfirmCallback`的监听，`Exchange`接收成功/失败会发送`Ack/Nack`回来，然后生产者进行回调处理。主要有三种确认策略：

##### 1. `Publishing Messages Individually`(单独确认)  

单条消息的确认应答，等待确认时生产者会阻塞等待（同步的）。

##### 2. `Publishing Messages in Batches`(批量确认)  

类似`TCP`的滑动窗口，批量消息的确认应答，等待确认时生产者会阻塞等待（同步的）。但是是一次等待确认多条消息。

##### 3. `Handling Publisher Confirms Asynchronously`(异步确认)  

生产者会==异步的==监听并处理`broker`返回的`ack/nack` 信息。

`deliveryTag` 表示发送消息的序号，`multiple` 表示是否批量确认。  

##### `return`回退

==`Exchange`到`Queue`的路由阶段。==

当消息无法路由到`Queue`时设置的回调，方便生产者进行相应的处理。（比如没有队列与消息的`Routing Key`匹配或队列不存在等）

#### 8. `SpringBoot`集成`RabbitMQ`



