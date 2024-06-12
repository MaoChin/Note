# Spring原理

## 1. Bean 的作用域和生命周期

### 1. 作用域

默认情况下，Spring容器中的Bean都是单例的，这种行为模式，我们就称之为Bean的作用域。

也就是说，Bean的作用域是指Bean在Spring框架中的某种行为模式。    

Spring中Bean的6种作用域：

1. singleton：单例作用域

2. prototype：原型作用域（多例作用域）
3. request：请求作用域
4. session：会话作用域
5. Application: 全局作用域
6. websocket：HTTPWebSocket作用域

| 作用域      | 说明                                                      |
| ----------- | --------------------------------------------------------- |
| singleton   | 每个SpringIoC容器内同名称的bean只有一个实例(单例)(默认)   |
| prototype   | 每次使用该bean时会创建新的实例(非单例)                    |
| request     | 每个HTTP请求生命周期内,创建新的实例(web环境中,了解)       |
| session     | 每个HTTPSession生命周期内,创建新的实例(web环境中,了解)    |
| application | 每个ServletContext生命周期内,创建新的实例(web环境中,了解) |
| websocket   | 每个WebSocket生命周期内,创建新的实例(web环境中,了解)      |

### 2. 生命周期

Bean的生命周期分为以下5个部分:    

1. 实例化(为Bean分配内存空间)  
2. 属性赋值(Bean注入和装配，比如 @AutoWired )
3. 初始化 （执行各种通知；执行初始化方法）
4. 使用Bean
5. 销毁Bean      

![image-20240611170924118](E:\Note\Java\JavaEE进阶\Spring原理.assets\image-20240611170924118.png)

## 2. SpringBoot 自动配置

SpringBoot的自动配置就是当Spring容器启动后，一些配置类，bean对象等就自动存入到了IoC容器中，不需要我们手动去声明，从而简化了开发，省去了繁琐的配置操作。  