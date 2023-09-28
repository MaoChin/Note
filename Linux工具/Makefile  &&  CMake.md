# `Makefile`  &&  `CMake`

自动化编译工具。

`Makefile`手写比较麻烦，所以出现了 `CMake`。`CMake`可以自动化生成`Makefile`文件。(就像C语言和汇编的关系)

```shell
cmake # 根据CMake文件得到Makefile文件
make  # 根据Makefile 编译
make install # 将make得到的动静态库自动拷贝到系统目录下
```

