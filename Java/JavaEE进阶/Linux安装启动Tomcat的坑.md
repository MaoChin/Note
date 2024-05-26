# `Linux`安装启动`Tomcat`的坑

1. `jdk`安装包，安装jdk-8时安装包是 x64的。
2. jdk-17和jdk-21有bug。
3. tomcat-10版本以上不支持jdk-8。
4. tomcat日志：logs/catalina.out 
5. 查看是否启动成功：ps -ajx | grep java