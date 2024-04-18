# `Makefile`使用

## 1. 基本格式

```makefile
targets : prerequisties
[tab键]command
```

- target：目标文件，可以是 OjectFile，也可以是执行文件，还可以是一个标签（Label），对于标签这种特性，在后续的“伪目标”章节中会有叙述。
- prerequisite：要生成那个 target 所需要的文件或是目标。
- command：是 make 需要执行的命令，

## 2. @的作用

```makefile
debug
	echo hello
```

`make debug`后显示：

```shell
echo hello 
hello
```

若不想打印具体的命令，则在`Makefile`命令前加 @

```makefile
debug
	@echo hello
```

## 3. 伪目标

```makefile
.PHONY:clean
clean:
	rm -rf ...
```

伪目标==总是被执行的==，可以避免 目标的取名和当前路径下的文件或文件夹重名而导致命令不被执行。

## 4. 变量的定义和使用

#### 1. 系统常量

可以用 `make -p`查看，非常多！

```makefile
AS: as   汇编器
CC: cc   c编译器
CPP: cpp   预处理器
CXX: g++
RM: rm -f
```

使用系统常量的好处：跨平台！！

#### 2. 自定义变量

```makefile
GCC = gcc
CPP := src/main.cpp 
OBJ := objs/main.o
TARGET := calc

# ()和{}都可以 
$(obj) : ${cpp}
	@g++ -c $(cpp) -o $(obj)

compile : $(obj)

# make compile命令即可
# make clean, make compile, make debug 很常用
```

- `$@`: 目标(target)的完整名称
- `$<`: 第一个依赖文件（`prerequisties`）的名称
- `$^`: 所有的依赖文件（`prerequisties`），以空格分开，不包含重复的依赖文件

## 5. 运算符

1. =   简单赋值，变量赋值后也可以更改
2. :=  立即赋值，变量赋值后无法更改
3. ?=  尝试赋值，变量已经定义了就步进行任何操作，变量未定义时才进行赋值。
4. +=  累加
5.  \    续行
6.  `*`   通配符
7.  %   通配符，并将匹配到的字符串作为变量使用。

## 6. 常用函数

使用方式：(或者用 {} 也可以)

```makefile
$(funcname argument argument ...)
```

1. `shell`  可以在`makefile`里使用`bash`命令
2. `subst`  字符串替换
3. `patsubst`   模式字符串替换，就是可以使用通配符进行描述

   



