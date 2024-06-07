# Spring 配置

配置文件主要是为了解决 ==硬编码==（数据直接嵌入到程序或其他可执行对象的源代码："代码写死"） 带来的问题，把可能会发生改变的信息，放在一个集中的地方，当我们启动某个程序时，应用程序从配置文件中读取数据，并加载运行。  

一般配置文件的格式：`.settings`，`.xml`，`.conf`，`.yml`..... 

`SpringBoot` 配置文件有以下三种：

​	• `application.properties`  （老版本，优先级高！配置冲突时按这个的配置来）

​	•` application.yml        `  （`yml` 就是 `yaml` 的缩写，这两个是一个东西）

​	•` application.yaml  `

==重点是学习这两种配置文件的格式！！==

## 1. `.properties`

`properties`配置文件中会有很多的冗余的信息。

```properties
#配置数据库连接信息
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/testdb?characterEncoding=utf8&
spring.datasource.username=root
spring.datasource.password=root
```

## 2. `.xml` （建议使用）

```yaml
spring:
	datasource:
		url: jdbc:mysql://127.0.0.0:3306/dbname?					characterEncoding=utf8&useSSL=false
		username: root
		password: root

# 集合类型，加个 -
dbtypes:
  name:
    - mysql
    - sqlserver
    - db2
# map 类型
maptypes:
  map:
    k1: kk1
    k2: kk2
    k3: kk3
```

@PostConstruct  注解修饰的方法会在项目初始化完就执行，方便打印日志进行测试！！











