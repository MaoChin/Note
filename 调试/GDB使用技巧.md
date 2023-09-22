# `GDB`使用技巧

`gcc`编译的时候必须加 `-g` 才能调试！！

```shell
gcc hello.c -o hello -g
# 开始调试
gdb hello
```

## 基本命令

| 命令                 | 含义                                         | 对应单词    |
| -------------------- | -------------------------------------------- | ----------- |
| `b 行数`             | 设置断点                                     | (break)     |
| `r`                  | 开始运行到第一个断点处                       | (run)       |
| `n`                  | 下一步，**不会进入函数内**                   | (next)      |
| `s`                  | 下一步，**遇到函数就进入函数**               | (step)      |
| `c`                  | 运行到下一个断点处                           | (continue)  |
| `q`                  | 退出                                         | (quit)      |
| `l 行数`             | 打印程序源代码(不过一般另开一个窗口看源代码) | (list)      |
| `bt`                 | 查看程序堆栈调用关系                         | (backtrace) |
|                      |                                              |             |
| `p 变量名`           | 打印变量的值，只在这一次打印                 | (print)     |
| `display 变量名`     | 每执行一步都自动打印变量名                   |             |
|                      |                                              |             |
| `set var 变量名=val` | 自己手动设置变量值为 `val`                   |             |
| `set args ...`       | 设置主程序的参数(`argc, argv[]`)             |             |

## `core dump`相关

当程序出现 `segment fault`时一般会有`core`文件，但是一般生产环境设置不产生`core`文件。

```shell
# 查看系统参数
ulimit -a
# 设置产生core文件的最大数量
ulimit -c 10000 
# 对产生core文件的程序使用，可以快速定位 segmentfault 的位置！
gdb hello core.xxx
```

## 调试正在运行的程序

**一般都要查看函数的调用堆栈(`bt`)！！！**

```shell
# 先查看这个运行程序(hello)的 pid
ps -ajx | grep hello
# 根据pid调试，加上 -p 选项 (编译时别忘了 -g 选项)
gdb hello -p pid
# 或者这样 使用 attach
gdb attach pid
```

一旦调试后该运行的程序就会停下，等待调试。然后随着调试运行！！！



在ubuntu下 `gdb -attach pid`  启动失败` ptrace: Operation not permitted`

主要[redhat](https://so.csdn.net/so/search?q=redhat&spm=1001.2101.3001.7020)在fedora22之后的版本中，引入了一种叫做`ptrace scope`的安全机制。这种机制为了防止用户访问当前正在运行的进程的内存和状态，所以在调试程序的过程中导致`gdb`不能正常工作。这种安全机制可以防止恶意软件附加到其他进程中（如SSH或者GPG），读取程序内存，产生安全问题。比如著名的`openssl`的"心脏出血"漏洞。

其解决方法有两种，都需要root权限进行操作

（1）临时方法

将`/proc/sys/kernel/yama/ptrace_scope`虚拟文件的内容设为0。

`echo 0 > /proc/sys/kernel/yama/ptrace_scope`

重启之后失效。

（2）永久解决

编辑`/etc/sysctl.d/10-ptrace.conf`这个文件，若没有，创建之。设置（默认是1）

`kernel.yama.ptrace_scope = 0`

这样下次启动时会生效。

## 调试多进程

父子进程调试：

```shell
# 调试父进程，默认就是调试父进程
set follow-fork-mode parent
# 调试子进程
set follow-fork-mode child
```

设置调试模式：

```shell
# 在调试一个进程的时候，其他进程继续运行(默认就是这个)
set detach-on-fork on
# 在调试一个进程的时候，其他进程挂起等待
set detach-on-fork off
```

查看可以调试的进程和当前正在调试的进程：

```shell
info inferiors
```

切换当前调试的进程：

```shell
# 不是pid，是gdb自己生成的进程num
inferior 进程num
```

## 调试多线程





