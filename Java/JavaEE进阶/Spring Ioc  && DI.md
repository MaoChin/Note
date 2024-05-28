# Spring IoC  && DI

==什么是Spring：Spring是包含了众多工具方法的IoC容器。==

==Spring的两大核心思想：IoC，AOP。==

## 1. IoC && DI

`Inversion of Control` (控制反转)  

`Dependency Injection` (依赖注入)

IoC是一种思想，可以通过DI来实现IoC。

当需要某个对象时，传统开发模式中需要自己通过new创建对象，现在不需要再进行创
建，把==创建对象的任务==交给容器（控制反转），程序中只需要依赖注入(Dependency Injection，DI)就可以了。这个容器称为：IoC容器。Spring是⼀个IoC容器，所以有时Spring也称为Spring容器。  

一般涉及多层类的包含关系时就可以使用 IoC 的思想。（Controller -》service -》dao）

```java
// @Controller 底层也有 @Component 注解！！
@Component   // 告诉 spring 把这个对象存一下
@Autowired   // 从 spring 中取出这个对象，后续可以直接使用里面的方法！！
```

## 2. IoC 相关注解













