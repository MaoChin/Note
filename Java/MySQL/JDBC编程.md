# `JDBC`编程

JDBC，即`Java Database Connectivity`，`java`数据库连接。是一种用于执行SQL语句的`Java API`，它是Java中的数据库连接规范。这个API由 `java.sql.*,javax.sql.* `包中的一些类和接口组成，它为Java开发人员操作数据库提供了一个标准的API，可以为多种关系数据库提供统一访问。  

简单来说就是一个抽象层，屏蔽了下层各种数据库不同`API`的差异。

需要在中央仓库（或其他平台）下载相关驱动包。（[Maven Repository: com.mysql » mysql-connector-j » 8.0.33 (mvnrepository.com)](https://mvnrepository.com/artifact/com.mysql/mysql-connector-j/8.0.33)）然后把`jar`包放到自己的项目目录并`Add as Library`。

`.jar`包是最常用的发布模式，直接把`jar`包给别人就可以用了！

## 1. `JDBC`使用流程

1. 创建数据库连接`Connection`
2. 创建操作命令`Statement`
3. 使用操作命令来执行`SQL`
4. 处理结果集`ResultSet`
5. 释放资源  

![image-20240521143505890](E:\Note\Java\MySQL\JDBC编程.assets\image-20240521143505890.png)

实际工作中很少直接使用`JDBC`编码！！因为直接使用比较繁琐，且有大量重复的操作，所以又封装了一层框架：`ORM`框架。如：`MyBatis`，`JPA`....... 其本质就是把数据库中的数据转换成了`java`对象，直接操作`java`对象就可以了。