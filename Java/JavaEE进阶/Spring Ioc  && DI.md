# Spring IoC  && DI

==什么是Spring：Spring是包含了众多工具方法的IoC容器。==

==Spring的两大核心思想：IoC，AOP。==

Spring 容器可以存取对象，“对象”在 Spring 范围中称为 ==“bean”。==

## 1. IoC && DI

`Inversion of Control` (控制反转)  

`Dependency Injection` (依赖注入)

IoC是一种思想，可以通过DI来实现IoC。

当需要某个对象时，传统开发模式中需要自己通过new创建对象，现在不需要再进行创
建，把==创建对象的任务==交给容器（控制反转），程序中只需要依赖注入(Dependency Injection，DI)就可以了。这个容器称为：IoC容器。Spring是⼀个IoC容器，所以有时Spring也称为Spring容器。  

一般涉及多层类的包含关系时就可以使用 IoC 的思想。（Controller -》service -》dao）

```java
// @Controller 底层也有 @Component 注解！！
@Component   // 告诉 spring 把这个对象 存 一下
@Autowired   // 从 spring 中 取 出这个对象，后续可以直接使用里面的方法！！
```

## 2. IoC 相关注解 （存）

1. 类注解：（默认bean的名称：小驼峰，若开头两个字母大写，就用原始的类名）
   1. @Controller（**独有**：入口，可以被外部访问）
   2. @Service
   3. @Repository （对应Dao层）
   4. @Component（其他组件，**根注解**）
   5. @Configuration（配置相关）

2. 方法注解：@Bean （需要和类注解配合使用！！）
3. @ComponentScan  指定存取对象时的扫描路径，默认为当前路径。



方法注解的使用场景：

1.  使用外部包里的类，没办法添加类注解  
2. 一个类需要多个对象，比如多个数据源  

## 3. DI 相关注解 （取）

### 1. 属性注入

直接通过 @Autowired 实现。 @Autowired 直接==修饰属性==。

通过类型进行匹配，但是同一个类型有多个对象时会先通过属性名称进行匹配，匹配不到就报错。

这种方式**无法注入 final 修饰的属性**，因为final修饰的属性只能通过 构造函数或者显式初始化。

### 2. 构造方法注入

 @Autowired ==修饰构造函数==，尤其是有多个构造函数时，必须要用  @Autowired 修饰其中一个！！

### 3. Setter注入

 @Autowired 通过==修饰属性的 set 方法==注入。

### 4. @Autowired 的问题

@Autowired 是通过属性类型区分的，所以当同一个类型有多个对象时会无法区分要注入哪一个（会先通过属性名称进行匹配，匹配不到就报错）！！解决：

```java
1. 直接把修饰的属性名称改成多个对象中的其中一个就可以。
2. @Primary       // 修饰存的对象，标识默认的注入对象
3. @Qualifier     // 修饰“取”的对象，标识要注入的对象名
4. @Resource      // 这个直接修饰“取”的对象，可以标识注入的对象名
```

==@Autowird与@Resource的区别==

1.  @Autowired是spring框架提供的注解，而@Resource是JDK提供的注解
2. @Autowired默认是按照==类型注入==，而@Resource是按照==名称注入==，相比于@Autowired来说，@Resource支持更多的参数设置，例如name设置，根据名称获取Bean等。  









