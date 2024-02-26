# `svn`初识

一款==版本控制工具==，主要解决多人协作开发，远程开发，版本控制等问题。

`svn`和`git`的区别：==集中式==版本控制(`svn`)和==分布式==版本控制(`git`)

## 1. 软件配置管理历史(版本控制)

`SCM`：软件配置管理，就是对软件的源代码进行控制管理。也可以理解成版本控制。

版本控制工具：  

1. `CVS`：元老级工具
2. `VSS`：微软旗下的入门级工具
3. `ClearBase`：`IBM`旗下工具
4. `SVN`：`SubVersion`，替代`CVS`
5. `git`：分布式版本控制

## 2. `svn`特点

1. 简单易用
2. 跨平台
3. 支持版本回退
4. **`C/S`模式**(`client-server`)，服务端：`VisualSVN`，客户端：`TortoiseSVN`

## 3. `VisualSVN`安装配置

安装略。

#### 1. 创建`svn server`

```shell
svnadmin create D:\svn_learning\svnserver\test  # (自己指定路径)
```

创建成功后在对应目录下有六个文件/文件夹：

![image-20240224164726554](D:\MyNote\版本控制工具\svn\svn初识.assets\image-20240224164726554.png)

#### 2. 进行服务端监管

就是服务端监听网络连接，有客户端连接时要进行响应。(==在进行客户端与服务端之间的操作时这个监管要一直打开！！==)

```shell
# -d 设置后台运行
# -r 指定监管目录
svnserve -d -r D:\svn_learning\svnserver\test  # (监管的路径)
```

进行服务端监管后客户端就可以使用 `svn://localhost` 或`ip`地址直接连接对应的项目仓库并进行各种上传下载操作。

（在实际中可能会同时开发多个项目，这时服务端监管可以通过==监管总目录==来达到监管目录下所有仓库的目的。）

#### 3. 权限控制

默认情况下，`svn`服务器不允许匿名用户上传文件(`write`)到服务端，所以要更改==配置文件(`conf`文件夹下)==进行权限管理。

具体做法：更改 `conf/svnserve.conf`文件中 `anon-access = read` 为 `anon-access = write`  并删掉其注释。

## 4. `TortoiseSVN`安装配置

安装略。

#### 1. 客户端连接本机`svnserve`

首先进行服务端监管，而后在自己指定的目录下 右键-》`TortoiseSVN` -》`Repo-browser` -》输入`svn`服务端地址(`svn://localhost`)  -》找到文件夹右键 -》`Checkout`

完成！！













