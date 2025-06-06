# Qt系统相关

## 1. 事件

**事件是应用程序内部或者外部产生的事情或者动作的统称**。在Qt中使用一个对象来表示一个事件。所有的Qt事件均继承于抽象类`QEvent`。事件是由系统或者Qt平台本身在不同的时刻发出的。如当用户按下鼠标、敲下键盘，或者是窗口需要重新绘制的时候，都会发出一个相应的事件。  

​	![image-20240724092121904](E:\Note\QT\Qt系统相关.assets\image-20240724092121904.png)

信号槽就是事件的进一步封装，但是有些情况下信号槽并不能解决问题（比如没有提供相应的信号），就需要重写事件处理函数！

事件处理一般常用的方法为：==在子类中重写相关的`Event`函数，== （多态）然后使用子类定义控件（提升为...）

**在实际中能使用信号槽就优先使用信号槽，信号槽无法解决问题才考虑使用事件机制。**

### 1.1 按键事件

Qt中的按键事件是通过`QKeyEvent`类来实现的。  

### 1.2 鼠标事件

在Qt中，鼠标事件是用`QMouseEvent`类来实现的。当在窗口中按下鼠标或者移动鼠标时，都会产生鼠标事件。

### 1.3 定时器

Qt中在进行窗口程序的处理过程中，经常要周期性的执行某些操作，或者制作一些动画效果，使用定时器就可以实现。所谓定时器就是在间隔一定时间后，去执行某一个任务。    

### 1.4 事件分发器

事件分发器负责将事件从一个对象传递到另一个对象，直到事件被处理或被取消。

在Qt中，我们发送的事件都是传给了`QObject`对象，更具体点是传给了`QObject`对象的`event()`函数。所有的事件都会进入到这个函数里面，那么我们处理事件就要重写这个`event()`函数。`event()`函数本身不会去处理事件，而是根据事件类型（type值）调用不同的事件处理函数：

![image-20240724092940954](E:\Note\QT\Qt系统相关.assets\image-20240724092940954.png)

## 2. Qt文件

在Qt中，文件读写的类为`QFile`。`QFile`的父类为`QFileDevice`，`QFileDevice`提供了文件交互操作的底层功能。`QFileDevice`的父类是`QIODevice`，`QIODevice`的父类为`QObject`。  

`QIODevice`是Qt中所有输入输出设备（`input/output device`，简称I/O设备）的基础类：

![image-20240724093241908](E:\Note\QT\Qt系统相关.assets\image-20240724093241908.png)

## 3. Qt多线程

在Qt中，多线程的处理是通过`QThread`类来实现。和 事件 的机制差不多，也是自己创建一个`Thread`对象继承`QThread`类，然后**重写`run`函数，利用多态实现**！！

自己创建的线程函数内部不允许操作UI图形界面，一般用数据处理，**UI界面的操作只能由主线程进行，防止线程安全问题！！**

在服务端多线程主要是为了充分利用硬件资源，提升整体性能；但是客户端的多线程主要是为了==提升用户体验==，比如把耗时的I/O操作交给另一个线程（被挂起），这样主线程可以持续提供服务！

#### 线程安全

实现线程互斥和同步常用的类有：

1. 互斥锁：`QMutex`、`QMutexLocker` （使用RAII思想控制锁的释放 ，类似C++中的`lock_guard`） 
2. 条件变量：`QWaitCondition`  
3. 信号量：`QSemaphore`  
4. 读写锁：`QReadLocker`、`QWriteLocker`、`QReadWriteLock`  

## 4. Qt网络

和多线程类似，Qt为了支持跨平台，对网络编程的API也进行了重新封装。

使用网络API时需要引入网络模块，**Qt有很多很多模块，只是在用到某个模块时才引入这个模块**，减少开销！（默认引入了`QtCore`模块，包含了信号槽，常用控件这些内容）

### 4.1 `UDP Socket`    

主要的类有两个：`QUdpSocket` 和 `QNetworkDatagram`   

### 4.2 `TCP Socket`

核心类是两个： `QTcpServer`（负责建立连接） 和 `QTcpSocket` （负责数据交互）

每个客户端服务器的连接都有一个`QTcpSocket`对象，所以==服务端会有大量的`QTcpSocket`对象，在连接断开时别忘了释放==！！（通过函数`deleteLater()`）

### 4.3 `HTTP`

关键类主要是三个.：`QNetworkAccessManager` , `QNetworkRequest` , `QNetworkReply`   

## 5. Qt音视频

### 5.1 Qt音频

在Qt中，音频主要是通过`QSound`类来实现。但是需要注意的是`QSound`类只支持播放`wav`格式的音频文件。也就是说如果想要添加音频效果，那么首先需要将非`wav`格式的音频文件转换为`wav`格式。  

### 5.2 Qt视频

在Qt中，视频播放的功能主要是通过`QMediaPlayer`类和`QVideoWidget`类来实现。  











