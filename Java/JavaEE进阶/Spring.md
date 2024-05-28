# Spring

**理解 Spring，Spring MVC，SpringBoot 之间的关系。**

`spring`一般指`spring`全家桶，包括`Spring MVC`（专门写`Web`程序的框架）等，是为了简化`Java`开发搞的，而`SpringBoot`是为了简化`spring`使用，可以方便的创建`Spring MVC`等项目。

## 1. 一个`Spring MVC` 项目的目录 

![image-20240526221543495](E:\Note\Java\JavaEE进阶\Spring.assets\image-20240526221543495.png)

通过观察项目启动日志可以知道`Spring MVC` 内置了`Tomcat`和`Servlet`，也就是说`Spring MVC`是基于`Servlet`的。

使用了`springboot`后就不需要再自己写 `servlet`的代码了！！而且`springboot`还可以帮助我们管理第三方包。 

## 2. `Spring Web MVC`

`MVC`：`model`，`view`，`controller`

![image-20240527145216738](E:\Note\Java\JavaEE进阶\Spring.assets\image-20240527145216738.png)

`SpringBoot` 可以认为 是创建`SpringMVC`项目的一种方式。

主体：==建立连接，请求，响应==

## 3. `Spring MVC`中常见注解

这里的注解简单来说就是告诉`spring MVC`以什么方式管理使用代码。

```java
@Controller  返回的是view，就是前端页面文件(html....)
// 但是随着前后端分离，后端基本上不负责前端页面了，所以就不需要直接返回页面
// 而是返回前端所需要的数据，这时就新搞了个 @RestController
// 通过看源码可以知道，@RestController就是包含了@Controller和@ResponseBody
@RestController
```

##### 请求

这些注解使用详情见文档以及源码。

```java
@RequestMapping   (建立连接/建立路由映射，支持所有请求方法！！)
@RequestParam     (参数重命名，传递集合)
@RequestPart      (上传文件)
@RequestBody      (可用于转换请求正文，如json)
@RequestHeader    (获取指定的头部，也可以使用servlet原生的方式！！)
@PathVariable     (获取URL中参数)

@CookieValue      (获取cookie值，也可以使用servlet原生的方式！！)
@SessionAttribute (获取session中指定的值，也可以使用servlet原生的方式！！)
```

##### 响应

```java
@ResponseBody     (返回值是前端需要的数据)
```

当返回值是 String 这些类型时，返回值的 content-type 就是 test/html

而当返回值是对象或者map类型时，返回值的 content-type 会自动设为 application/json

除了自动识别外，如果想要自己设置 返回值的  content-type，是通过@RequestMapping 注解中的变量设置的。

## 4. 应用分层

![image-20240528155238206](E:\Note\Java\JavaEE进阶\Spring.assets\image-20240528155238206.png)

一种主流分层：三层

1. 表现层  （controller）
2. 逻辑处理层  （service）
3. 数据层    (dao)

