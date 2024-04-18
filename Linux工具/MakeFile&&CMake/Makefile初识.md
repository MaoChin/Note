# `Makefile`初识

自动化编译工具。

`Makefile`手写比较麻烦，所以出现了 `CMake`。`CMake`可以自动化生成`Makefile`文件。(就像C语言和汇编的关系)

```shell
cmake # 根据CMake文件得到Makefile文件
make  # 根据Makefile 编译
make install # 将make得到的动静态库自动拷贝到系统目录下
```

一些详细信息在目录：`E:\GithubInstall\Makefile\GNC-Tutorial` 中！



在使用 `Makefile`时都是编译和链接分开进行，尤其是有很多源文件的大型项目，分开编译，最后进行链接，这样改动某些文件时重新编译就只需编译那些改动的文件，而其他的文件无需编译，提高效率。