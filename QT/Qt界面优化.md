# Qt界面优化

## 1. `QSS`

Qt仿照`CSS`的模式，引入了`QSS`，来对Qt中的控件做出样式上的设定，使得编写的界面可以更好看一些。

### 1.1 基本语法

```css
选择器 {
	属性名: 属性值;
}

QPushButton {
	color: red;
}
```

### 1.2 选择器

| 选择器                      | 示例                               | 说明                                                         |
| --------------------------- | ---------------------------------- | ------------------------------------------------------------ |
| 全局选择器                  | *                                  | 选择所有的`widget`                                           |
| 类型选择器(`type selector`) | `QPushButton`                      | 选择所有的`QPushButton`和其子类的控件                        |
| ID选择器                    | `#pushButton_2`                    | 选择 `objectName` 为 `pushButton_2` 的控 件                  |
| 并集选择器                  | `QPushButton,QLineEdit, QComboBox` | 选择`QPushButton,QLineEdit,QComboBox`这 三种控件(即接下来的样式会针对这三种控件都生效) |

伪类选择器(`Pseudo-States`)：根据控件==所处的某个状态进行选择==。使用 `:` 指定。

### 1.2 盒模型(`Box Model`)  

![image-20240726095808333](E:\Note\QT\Qt界面优化.assets\image-20240726095808333.png)

一个遵守盒模型的控件，由四个部分构成：

1. `Content`：具体内容
2. `Padding`：内边距，内容和边框的距离
3. `Border`：边框
4. `Margin`：外边距，不同控件之间的距离

可以通过⼀些QSS属性来设置上述的边距和边框的样式：

| QSS属性        | 说明                                                     |
| -------------- | -------------------------------------------------------- |
| `margin`       | 设置四个方向的外边距，复合属性。                         |
| `padding`      | 设置四个方向的内边距，复合属性。                         |
| `border-style` | 设置边框样式                                             |
| `border-width` | 边框的粗细                                               |
| `border-color` | 边框的颜色                                               |
| `border`       | 复合属性，相当于`border-style+border-width+border-color` |

## 2. 绘图

Qt提供了画图相关的API，可以允许我们在窗口上绘制任意的图形形状，来完成更复杂的界面设计。之前学习的 **”控件“ 本质上也是通过画图的方式画上去的**（算是对画图API的进一步封装）。 

绘图API核心类：

| 类             | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| `QPainter`     | 用来绘图的对象，提供了一系列`drawXXX`方法，可以允许我们绘制各种图形 |
| `QPaintDevice` | 描述了`QPainter`把图形画到哪个对象上。`QWidget`也 是一种`QPaintDevice` (`QWidget`是`QPaintDevice`的子类) |
| `QPen`         | 描述了`QPainter`画出来的线是什么样的（颜色，粗细等）         |
| `QBrush`       | 描述了`QPainter`填充一个区域是什么样的（颜色，样式等）       |

**绘图API的使用，一般不会在`QWidget`的构造函数中使用，而是要放到 `paintEvent` 事件处理函数中。**

`paintEvent`事件触发时机：

1. 控件首次创建 
2. 控件被遮挡，再解除遮挡   
3. 窗口最小化，再恢复
4. 控件大小发生变化
5. 主动调用 `repaint()` 或者 `update()` 方法      

### 2.1 绘制形状

### 2.2 绘制图片

Qt提供了四个类来处理图像数据：`QImage`、`QPixmap`、`QBitmap`和`QPicture`  

1. `QImage`主要用来进行I/O处理，它对I/O处理操作进行了优化，而且可以用来直接访问和操作像素。
2. `QPixmap`主要用来在屏幕上显示图像。
3. `QBitmap`是`QPixmap`的子类，用来处理颜色深度为1的图像，即只能显示黑白两种颜色。  
4. `QPicture`用来记录并重演`QPainter`命令。  
