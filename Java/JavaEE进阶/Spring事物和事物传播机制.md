# Spring事物和事物传播机制

## 1. 事物基本操作

```sql
start transaction
commit
rollback
```

`Spring`有两种方式实现事物：编程式实现（类似直接写SQL语句）和声明式事物（通过注解实现）。

编程式实现通过`Spring`内置的对象方法实现，自己控制commit和rollback，更加灵活。

声明式实现 会==在程序抛出异常（RunTime异常）且没有捕获的时候进行rollback==，否则就自动 `commit`了。（也有手动进行`rollback`的方法！！）	

## 2. 事物传播机制

被 @Transactional  修饰的方法中有调用了其他的方法，在别的方法中又有数据库的操作，这种情况下事物的传播机制。

![image-20240609105628098](E:\Note\Java\JavaEE进阶\Spring事物和事物传播机制.assets\image-20240609105628098.png)





