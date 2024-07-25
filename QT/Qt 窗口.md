# Qt 窗口

Qt窗口是通过`QMainWindow`类来实现的。  

`QMainWindow` 是一个为用户提供主窗口程序的类，继承自`QWidget`类，并且提供了一个预定义的布局：包含一个菜单栏（`menu bar`）、多个工具栏(`tool bars`)、多个浮动窗口（铆接部件）(`dock widgets`)、一个状态栏(`status bar`)和一个中心部件(`central widget`)，它是许多应用程序的基础：  

![image-20240722173311923](E:\Note\QT\Qt 窗口.assets\image-20240722173311923.png)

## 1. 菜单栏

Qt 中的菜单栏是通过`QMenuBar`这个类来实现的。**一个主窗口最多只有一个菜单栏**。位于主窗口顶部、主窗口标题栏下面。  

菜单栏（`QMenuBar`）-》菜单（`QMenu`）-》菜单项（`QAction`）/ 子菜单

```C++
// 方式一：若QMenuBar已经存在就直接返回；否则创建一个新的
QMenuBar* menuBar = this->menuBar();
// 方式二：直接创建新的QMenuBar
QMenuBar* menuBar = new QMenuBar();
// 如果使用方式二创建，则此时可能会发生内存泄漏！！！
// 可能窗口中已经有menuBar了，新创建的就会替换掉旧的，那个旧的就无法释放了！！
this->setMenuBar(menuBar);
```

## 2. 工具栏

工具栏是应用程序中**集成各种功能 实现快捷键使用**的一个区域。可以有多个，也可以没有，它并不是应用程序中必须存在的组件。在Qt中，工具栏是通过`QToolBar`类来实现的。  

## 3. 状态栏

**状态栏是应用程序中输出简要信息的区域**。一般位于主窗口的最底部，一个窗口中最多只能有一个状态栏。在Qt中，状态栏是通过`QStatusBar`类来实现的。  

## 4. 浮动窗口

在Qt中，浮动窗口也称之为铆接部件。浮动窗口是通过`QDockWidget`类来实现浮动的功能。浮动窗口一般是位于核心部件的周围，可以有多个。 

往`QDockWidget`里添加控件要先创建一个`QWidget`，将控件放在`QWidget`里，然后再把这个`QWidget`放到`QDockWidget`中。

## 5. 对话框

对话框是GUI程序中不可或缺的组成部分。一些不适合在主窗口实现的功能组件可以设置在对话框中。对话框通常是一个顶层窗口，出现在程序最上层，**用于实现短期任务或者简洁的用户交互。**

Qt常用的内置对话框有：

1. `QMessageBox`（消息框）  
2. `QColorDialog`（颜色对话框）
3. `QFileDialog`（文件对话框）
4. `QFontDialog`（字体对话框）
5. `QInputDialog`（输入对话框）

对话框分为**模态对话框**和**非模态对话框**。

```C++
// 一般要给对话框设置这个属性，当对话框关闭的时候自动释放资源！！
dialog->setAttribute(Qt::WA_DeleteOnClose);
```

### 5.1 模态对话框

模态对话框指的是：显示后无法与父窗口进行交互，==必须先操作对话框==，是一种阻塞式的对话框。  

一般比较重要的决策需要使用模态对话框。

```C++
dialog->exec();
```

### 5.2 非模态对话框

  非模态对话框显示后独立存在，可以同时与父窗口进行交互，==不是必须要先操作对话框==，是一种非阻塞式对话框。  

不是很重要的决策可以使用非模态对话框。

```c++
dialog->show();
```

### 5.3 Qt内置对话框

Qt提供了多种可复用的对话框类型，即Qt标准对话框。Qt标准对话框全部继承于`QDialog`类。常用标准对话框如下：  

![image-20240723093432221](E:\Note\QT\Qt 窗口.assets\image-20240723093432221.png)

内置对话框的使用一般都可以通过类中的==静态函数==实现。比如：

1. `QMessageBox:warning()`，`QMessageBox:infomation()`
2. `QColorDialog:getColor()`
3. `QFileDialog:getOpenFileName()`，`QFileDialog:getSaveFileName()`
4. `QFontDialog:getFont()`
5. `QInputDialog:getInt()`，`QInputDialog:getItem()`













