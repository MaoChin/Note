# `Makefile`  &&  `CMake`

自动化编译工具。

`Makefile`手写比较麻烦，所以出现了 `CMake`。`CMake`可以自动化生成`Makefile`文件。(就像C语言和汇编的关系)

```shell
cmake # 根据CMake文件得到Makefile文件
make  # 根据Makefile 编译
make install # 将make得到的动静态库自动拷贝到系统目录下
```

一些详细信息在目录：`E:\GithubInstall\Makefile\GNC-Tutorial` 中！



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

伪目标总是被执行的，可以避免 目标的取名和当前路径下的文件或文件夹重名而导致命令不被执行。











