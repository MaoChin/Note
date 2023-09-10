# `Git配置`

## 1. 安装

一路默认即可。

## 2. 配置账号信息

```shell
git config --global user.name luckydog
git config --global user.email "13040588581@163.com"
git config --global credential.helper store
```

 ## 3. 配置`SSH`

`git clone` 等命令有两种链接方式：`https`和`ssh`，用`ssh`更方便一些。

先生成公钥。

```shell
ssh-keygen -t rsa -C "13040588581@163.com"	
```

然后一路回车，直到出现公钥。复制到 `github` 特定位置即可。

一般`windows`的防火墙把22端口屏蔽了，所以换一个端口：

打开这个文件，没有就自己创建一个。

```shell
vim ~/.ssh/config
```

然后输入：

```shell
Host github.com
  Hostname ssh.github.com
  Port 443
```

测试：

```
ssh -T git@github.com
// 返回如下就可以了
Hi xxxxx! You've successfully authenticated, but GitHub does not
provide shell access.
```

