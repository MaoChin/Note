# `svn`多仓库与权限控制

## 1. 多仓库

在实际中可能会同时开发多个项目，这时服务端监管可以通过==监管总目录==来达到监管目录下所有仓库的目的。

这样访问时就可以指定目录进行访问，如：

```shell
# 服务端监管svnserver总目录
svnserve -d -r D:\svn_learning\svnserver

# 访问test项目
svn://localhost/test
# 访问test1项目
svn://localhost/test1
```

## 2. 权限控制

在仓库的`conf`下保存仓库的配置文件，其中：

1. `authz`文件是授权文件，告知哪些用户具有哪些权限：

![image-20240226224229872](E:\Note\版本控制工具\svn\svn多仓库与权限控制.assets\image-20240226224229872.png)

2. `passwd`文件是认证文件，标识当前svn系统中 对于某个仓库 有哪些用户以及对应的密码：

![image-20240226224119763](E:\Note\版本控制工具\svn\svn多仓库与权限控制.assets\image-20240226224119763.png)

3. `svnserver.conf`文件是通用配置文件，默认情况下上面两个配置是关闭的，需要在`svnserver.conf`文件中配置开启：

   ![image-20240226224613720](E:\Note\版本控制工具\svn\svn多仓库与权限控制.assets\image-20240226224613720.png)

​		