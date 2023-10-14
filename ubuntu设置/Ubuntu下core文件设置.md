# `Ubuntu`下`core`文件设置

## 1. 先打开`ulimit`设置

```shell
ulimit -a  # 查看相关文件的设置

ulimit -c unlimited # 打开core文件设置
```

## 2. 修改配置文件

1. **修改`/etc/default/apport`文件，把`enabled` 设置为0。**

`ubuntu`的服务`apport.service`。自动生成崩溃报告，官方为了自动收集错误的。这个玩意会导致`core_pattern`的设置不能一直有效，只要这个服务存在，系统重新启动后就会把`core_pattern`改为一个特定的值，直接导致`coredump`无法生成。

2.  **在文件 `/etc/sysctl.conf` 中添加以下内容：**

```shell
# 设置 core 文件目录
kernel.core_pattern = /var/core/core_%e_%p   
#  是否加上pid
kernel.core_uses_pid = 1  
```

然后 `sudo reboot`。

此时 `cat /proc/sys/kernel/core_pattern` 应该是 `/var/core/core_%e_%p` 。
