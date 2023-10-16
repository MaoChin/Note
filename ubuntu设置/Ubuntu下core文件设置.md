# `Ubuntu`下`core`文件设置

## 1. 先打开`ulimit`设置

```shell
ulimit -a  # 查看相关文件的设置

ulimit -c unlimited # 打开core文件设置
```





----

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

---

**==以上设置不靠谱！！搞了我两天！！！==**



[一个解决方案]([core文件去哪里了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/424222735))

对于c程序员来说，core文件是分析内存错误的有用的文件，结合gdb命令，一般情况下（有时候代码编译的时候没有包含debug信息或者栈空间被破坏，会看不到具体的位置信息），可以知道导致core的具体的代码位置。

通常做法是：

```text
gdb 应用程序名 core.xxx
> where
```

但是最近在centos8上排查core问题的时候，发现core文件没有了，通过：

```text
ulimit -c 
```

排查结果是unlimited ，那么core文件哪里去了那。

## **一 是不是真的core了**

首先思路是排查下，程序是被别人kill了，还是真的core dump了，通过命令：

```text
dmesg
或者 tail -f  /var/log/message
```

如果真的程序core dump，可以看到如下的信息：

![img](https://pic4.zhimg.com/80/v2-40e4a7fa4cf26d76a4c44e5bf59a5ddb_1440w.webp)

dmesg默认情况下不会打印这么容易识别的时间，可以通过-H选项来打印 具体的时间，如上图。

好了排查完毕，程序确实core了，但是core没找到那。

## **二 排查是不是core文件大小限制**

虽然刚才用ulimit -c命令进行查看，需要进一步查看程序是不是这个用户启动的，后来干脆在这个用户的.bash_profile文件中添加命令：

```text
ulimit -c unlimited
```

重新登录后，ulimit -c已经是unlimited了，说明设置的没有问题。

## **三 保存位置排查**

查看core_pattern 配置:(如果配置了路径，需要检查保存路径是否有写权限)

```text
cat /proc/sys/kernel/core_pattern
|/usr/lib/systemd/systemd-coredump %P %u %g %s %t %c %h %e
```

发现配置和以前不一样，所以临时改了下，改成：

```text
echo  "core-%e-%p-%t" > /proc/sys/kernel/core_pattern
```

果然再次运行程序可以在可执行文件所在的目录生成core文件。如果我们想让core文件固定产生在一个目录中，可以在上面的配置中加上路径即可。 比如：

```text
echo "/tmp/core-%e-%p-%t" >/proc/sys/kernel/core_pattern
```

> %% – 符号% %p – 进程号 %u – 进程用户id %g – 进程用户组id %s – 生成core文件时收到的信号 %t – 生成core文件的时间戳(seconds since 0:00h, 1 Jan 1970) %h – 主机名 %e – 程序文件名

这样更改只是临时的，要想永久生效可以通过更改配置文件：

```text
vim /etc/sysctl.conf
添加一行：kernel.core_pattern=core-%e-%p-%t
```

执行：`sysctl -p` 让配置立刻生效。 写个测试代码：

```text
#include <stdio.h>
  
int main(int argc ,char * argv[])
{
        printf("hello core\n");
        int * p = NULL;
        *p = 2;
        return 0;
}
```

编译和执行：

```text
gcc 1.c && a.out
```

生成core文件：

```text
-rw------- 1 root root 499712 Oct 20 22:18 core-a.out-252068-1634739501
```

**Linux 手册中可能没有core文件原因：**

1. core 文件所在的路径没有写权限。
2. core文件已经存在，而且有超过一个硬链接，链接到这个文件。
3. 磁盘满了或者inode使用完了，或者文件系统以只读方式挂载，或者此用户使用的空间已经达到了限额。
4. ulimit -c 设置为0，所以没有core。
5. 可执行权限没有可读权限，所以无法镜像其内存。
6. 正在执行的程序被设置了set-user-id 或（set-group-id）导致拥有程序的用户id或用户组id和执行时候的不同。
7. proc/sys/kernel/core_pattern 和/proc/sys/kernel/core_uses_pid 被设置为0，/proc/sys/kernel/core_uses_pid为1标识需要在core文件有加.pid的后缀，设置为0时不加。

## **四 原来配置怎么回事**

centos8的默认配置又是什么那？

```text
|/usr/lib/systemd/systemd-coredump %P %u %g %s %t %c %h %e
```

看下手册说明:

```text
 Since kernel 2.6.19, Linux supports an alternate syntax for the
       /proc/sys/kernel/core_pattern file.  If the first character of
       this file is a pipe symbol (|), then the remainder of the line is
       interpreted as the command-line for a user-space program (or
       script) that is to be executed.
```

即从2.6.19内核版本以后，core的设置可以通过管道传递给用户的程序，这个程序必须写绝对路径，以空格分割后面传递的参数。后面用到的%P和前面的是一样的，标识进程号。

注意：

1. 这个程序以root用户和root用户组作为执行的用户和用户组，但是不会带来特殊权限绕过。

简单来说，core通过管道的方式被/usr/lib/systemd/systemd-coredump 程序处理了。默认情况下core被以LZ4压缩的格式放置在/var/lib/systemd/coredump/ 目录中；特意排查下没有core的主机确实如此，如下：

![img](https://pic3.zhimg.com/80/v2-1c4767fcc9b62e3e66f6b63ac895ac8a_1440w.webp)

简单点，可以通过命令查看最近5个core文件：

```text
coredumpctl list | tail -5
```

![img](https://pic2.zhimg.com/80/v2-578b3cd82e37e5f796b0bf53735dc4f9_1440w.webp)

说明：

> The information shown for each core dump includes the date and time of the dump, the PID, UID, and GID of the dumping process, the signal number that caused the core dump, and the pathname of the executable that was being run by the dumped process

对于我们习惯core在当前文件产生，而且是非压缩的来说，这种压缩方式还挺难用，不过linux系统提供了一个命令，可以把core文件根据pid转存到当前目录，如下：

```text
coredumpctl dump 92362 -o core
```

把pid为92362 的进程的core文件转存到本目录的core。 如果你要说我就喜欢原来的方式，也可以通过如下命令进行更改：



==这个命令不靠谱！==

```text
 # echo "kernel.core_pattern=core.%p" > \
                          /etc/sysctl.d/50-coredump.conf
#/lib/systemd/systemd-sysctl 
```

临时的可以如下改下：

```text
sysctl -w kernel.core_pattern="%e-%s.core"
```

和我们上面的改法没有实质的差别。
