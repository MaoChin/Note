# `virtualbox`使用

## 1. 网络设置的坑

在默认情况下，`Virtualbox`虚拟机选择的上网方式是：**网络地址转换（`NAT`），**这种方式虚拟机可以上外网，但是主机不能访问虚拟机，如果想要使用`xshell`连接虚拟机是办不到的。

解决：[Virtualbox虚拟机网络配置详解_virtualbox 网络配置-CSDN博客](https://blog.csdn.net/tangyi2008/article/details/89036433)