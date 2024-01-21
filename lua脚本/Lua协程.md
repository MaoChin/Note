# `Lua`协程

## 1. 简介

`lua5.0`之后支持协程。

对比协程与线程：

线程：==`CPU`调度的基本单位==，多个线程共享进程的资源如地址空间等；但线程也相对独立，有自己的线程栈和寄存器。线程切换任收系统控制。

协程：也相对独立，==有自己的上下文(寄存器)==，但是其**切换由自己控制**，由当前协程控制如何切换。可以把协程看成可以自己决定调度的线程。

## 2. 协程库

`lua`的协程库：`coroutine`：

```lua
-- 创建协程，传入一个函数，返回协程句柄,新创建的协程是挂起态的
coroutine.create(func); 

-- 将挂起的协程唤醒，开始执行当时设置的方法，执行完成后返回bool值和协程函数返回值
-- 第一个参数是协程句柄，后面的参数是传给协程对应函数的
-- 注意这个函数会阻塞等待func函数调用！！直到其调用返回
coroutine.resume(co, ...);
-- 获取正在运行的协程
coroutine.running();
-- 获取协程的状态(running, suspended(挂起), dead(结束))
coroutine.status(co);
-- 挂起传入的协程
coroutine.yield(co);
-- 判断协程是否处于 yield(挂起) 状态
coroutine.isyieldable(co);
-- 关闭协程，传入句柄，返回bool
coroutine.close(co);

-- 用func创建一个新协程
coroutine.wrap(func);
```

## 3. 协程切换















