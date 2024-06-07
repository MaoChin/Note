# Spring 日志

日志框架：`Slf4j`

直接使用就可以：

```java
private static Logger logger = LoggerFactory.getLogger(LoggerController.class);

logger.info("--------------要输出日志的内容----------------");

// 使用 lombok 第三方库中的注解可以简化使用！
@Slf4j  
```

SLF4J不同于其他日志框架，它不是一个真正的日志实现，而是一个抽象层，对日志框架制定的一种规范/标准/接口。所有SLF4J并不能独立使用，需要和具体的日志框架配合使用。  

![image-20240530110220133](E:\Note\Java\JavaEE进阶\Spring 日志.assets\image-20240530110220133.png)

==门面模式==（Facade Pattern）又称为外观模式，提供了一个统一的接口，用来访问子系统中的一群接口。其主要特征是定义了一个高层接口，让子系统更容易使用。  

Slf4j 就是这个门面，在底层 `SpringBoot` 默认使用的是 `logback` 这个日志实现。其实就是加了一个抽象层，做到了解耦。

![image-20240530110626898](E:\Note\Java\JavaEE进阶\Spring 日志.assets\image-20240530110626898.png)

### 日志相关配置

```yaml
# 日志级别配置  fatal>error>warn>info>debug>trace
logging:
  level:
  	root: debug 
# 日志持久化配置
logging:
  file:
  	name: logger/springboot.log
# 日志文件分割	
logging:
  logback:
    rollingpolicy:
      max-file-size: 1KB
        file-name-pattern: ${LOG_FILE}.%d{yyyy-MM-dd}.%i	
```

