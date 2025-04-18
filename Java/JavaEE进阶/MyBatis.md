# `MyBatis`

`MyBatis` 是一款优秀的==持久层框架==，用于简化JDBC的开发。其中 持久层指的就是持久化操作的层，通常指 数据访问层(Dao) ，是用来操作数据库的。  `MyBatis`是独立的，和`Spring`没有关系，也就是说用原始的`Servlet`编程也是可以使用`MyBatis`的。	

`MyBatis`有两种使用方式，一是通过注解的方式使用，二是通过编写配置文件`xxx.xml`的方式。主要就是学习 增删改查 ！！！

## 1. 注解方式

```java
@Mapper
@Select
@Insert
@Delete
@Update

// 把表中自增id（auto_increment）赋值给某个字段，方便后续操作
@Option(.....)
// 结果映射
@Results
@Result
@ResultMap    // 映射规则复用
```

其中，查询时会自动根据表中的字段名和 Java 类的属性名进行匹配，对于匹配不上的字段有三种方法：

1. 编写 `sql` 语句时起别名
2. 使用结果映射（@Results，@Result，@ResultMap）
3. 使用配置文件，自动把MySQL字段的 蛇形命名 转换成 驼峰命名（推荐） 

## 2. xml 方式

1. 配置文件
2. 方法声明
3. 编写 `xml` 文件

针对查询也有表中的字段名和 Java 类的属性名不匹配的情况，一样使用配置文件的方式解决。

## 3. `#{} `和 `${}` 的区别（预编译SQL 和 即时SQL）

二者都是取出变量中的内容，但是` #{} `是运行时替换（进行预编译），并且对字符串类型会**自动加上引号**；而 `${}`是编译时替换（即时编译），而且是**直接拼接，不管类型**。

`#{}` 使用的是预编译SQL，通过` ? `占位的方式提前对SQL进行编译，然后把参数填充到SQL语句中。` #{} `会根据参数类型自动拼接引号。（这个自动拼接也有缺点：比如是 desc 等关键字 或者模糊查询时本来就不需要引号！！）
		`	${}` 直接进行字符替换，一起对SQL进行编译。所以如果参数为字符串，需要自己加上引号。  `${}`最大的问题：==SQL注入==

所以==实际中尽量使用 `#{}`==，因为它更安全，而且效率也更高（可以把语法解析，优化和编译后的结果进行缓存，后面就不用再编译了）！！

## 4. 连接池

数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个。  

优点：

1. 减少了网络开销
2. 资源重用
3. 提升了系统的性能 

常用的数据库连接池：

• `C3P0`
		• `DBCP`
		• `Druid`
		• `Hikari`   

## 5. 动态SQL

根据需求不同动态的拼接SQL。通过标签的方式实现。

```xml
<if test="判断条件xxx"> </if>
<trim prefix="xxx"> </trim>
prefix：表示整个语句块，以prefix的值作为前缀（添加指定的）
suffix：表示整个语句块，以suffix的值作为后缀（添加指定的）
prefixOverrides：表示整个语句块 要去除掉的前缀（删除指定的）
suffixOverrides：表示整个语句块 要去除掉的后缀（删除指定的）

<where> </where>
<set> </set>
<foreach collection="xxx" item="xxx" separator="xxx"> </foreach>
collection：绑定方法参数中的集合，如List，Set，Map或数组对象
item：遍历时的每一个对象
open：语句块开头的字符串
close：语句块结束的字符串
separator：每次遍历之间间隔的字符串


<sql id="xxx"> </sql>
<include refid="xxx"> </include>
```







