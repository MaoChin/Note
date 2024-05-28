# `Java`初识

## 1. 环境安装

`JDK` （`JDK-1.8或者JDK-21`），`IDEA`（集成开发环境）

## 2. 编译流程

```shell
# 每个类都会产生一个字节码文件。
javac xxx.java  # 编译源代码得到 xxx.class 的字节码文件
java xxx        # 运行字节码文件
```

==一个`java`文件只能有一个`public`修饰的类，且该类名必须和文件名相同。==

## 3. 数据类型

基本数据类型+引用数据类型。（引用数据类型的默认值是null）

基本数据类型：整形（byte 一个字节，short，int，long)，浮点型（float，double），`boolean`，字符类型（char  两个字节）

引用数据类型：字符串（String），数组，类（class），接口

类型转换（隐式类型转换，强制类型转换）。

数值提升（小于4字节的整形参与运算时会提升至4字节）。

（null）小写

##### 基本数据类型的包装类

针对每一个基本数据类型都有一个对应的==包装类==，如 int -》Integer。这个包装类在开发在还是很有用的！！

## 4. 运算符

算数运算符，关系运算符，逻辑运算符（`&&，||，!`），位运算符（`&，|， ^，~`），移位运算符（<<，>>，>>>），条件运算符。

+=，-=等复合运算符遇到 需要强制类型转换时 会自动进行转换。

移位运算符中多了一个 >>> 表示无符号右移，就是右移时左边统统补0。没有 <<< 运算符。

## 5. 逻辑控制

顺序，分支（if，switch），循环。

## 6. 方法（函数）

类似C的函数，使代码解耦。 

函数实参形参的关系，==传值调用，传引用调用==。（`Java`的引用很弱，引用类型）

函数重载，只关注 参数个数/类型/顺序，返回值不影响重载。和C++一样，底层也是通过修改函数名实现的。

递归函数。

## 7. 数组（引用类型）

遍历时也有类似C++的范围for。

```java
// arr 这个变量是一个引用变量（在JVM stack 中），存储的是堆区的地址，真正的数据在堆上
int[] arr = new int[]{1,2,3};
// 类似浅拷贝，arr1和arr指向堆区的同一块空间
int[] arr1 = arr;
// 空指针，不指向任何对象
int[] arr2 = null;
// 二维数组
int[][] arr3 = {{1,2,3}, {3,4,5}};
// arr3.length;    ===行数
// arr3[i].length; ===每一行的列数
```

当堆区的数据没有引用指向的时候就会自动被回收。

数组作为函数参数，返回值。

数组方法类：`Arrays`

```java
// Arrays 的一些方法
Arrays.equals();
Arrays.fill();
Arrays.sort();
Arrays.toString();
Arrays.deepToString();  // 可以转换二维数组
// 这两个拷贝的方法底层是 System.arraycopy
// public static native void arraycopy() 
// native 方法是C/C++实现的，快！！
Arrays.copyOf();
Arrays.copyOfRange();
Arrays.equals();
Arrays.equals();
```

## 8. `JVM` 内存分区

![image-20240515215733600](E:\Note\Java\Java语法\Java初识.assets\image-20240515215733600.png)

程序计数器 (PC Register): 只是一个很小的空间, 保存下一条执行的指令的地址 。

虚拟机栈(JVM Stack): 重点是存储**局部变量表**(当然也有其他信息). 我们创建的 `int[] arr `这样的存储地址的引用就是在这里保存。

本地方法栈(Native Method Stack): 本地方法栈与虚拟机栈的作用类似。只不过保存的内容是Native方法的局部变量。在有些版本的 JVM 实现中(例如HotSpot)， 本地方法栈和虚拟机栈是一起的。

堆(Heap): JVM所管理的最大内存区域。使用 new 创建的对象都是在堆上保存 (例如`new int[]{1, 2, 3}` ) 。所有线程共享。

方法区(Method Area): 用于存储已被虚拟机加载的==类信息、常量、静态变量、即时编译器编译后的代码==等数据。方法编译出的的字节码就是保存在这个区域。所有线程共享。

运行时常量池(Runtime Constant Pool): 是方法区的一部分, 存放字面量(字符串常量)与符号引用。(**注意** 从 JDK1.7 开始, 运行时常量池在堆上)

## 9. 包

```java
// 导入包，本质就是导入Arrays这个类
import java.util.Arrays;
// 使用 import static 可以导入包中的静态的方法和字段
import static java.lang.System.*;
```

