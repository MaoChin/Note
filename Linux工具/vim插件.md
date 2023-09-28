# `vim`插件

## 1.  `plug`安装

`vim-plug`是一款插件管理工具，可以用它安装，更新，卸载插件。

```shell
mkdir ~/.vim
cd ~/.vim

# ~/.vim/plugged/ 存放从vim-plug官方下载下来的插件

# ~/.vim/plugin/ 存放的是每次启动vim都会被运行一次的插件，也就是说只要你想在vim启动时就运行的插件就放在这个目录下

# ~/.vim/syntax/ 语法描述脚本。我们放有关文本（比如c语言）语法相关的插件

# ~/.vim/colors/ 是用来存放vim配色方案的

# ~/.vim/doc/ 为插件放置文档的地方。例如:help的时候可以用到

# ~/.vim/autoload/ 它是一个非常重要的目录，尽管听起来比实际复杂。简而言之，它里面放置的是当你真正需要的时候才被自动加载运行的文件，而不是在vim启动时就加载
mkdir plugged plugin syntax colors doc autoload
```

下载 `plug.vim` 文件在`~/.vim/autoload`目录中。

## 2. `YouCompleteMe`

`PlugInstall`后在`YouCompleteMe`目录下执行：

```shell
git submodule update --init --recursive

cd  ~/.vim/plugged/YouCompleteMe/third_party/ycmd/third_party
# 有个mrab-regex可能会git clone失败，自己手动clone一下,用https可能失败！！！
# git clone https://github.com/mrabarnett/mrab-regex.git
# 换ssh可以(就在 ....ycmd/third_party 下clone)
git clone git@github.com:mrabarnett/mrab-regex.git

# 不断执行这个，有的包不存在就看提示安装对应的包
python3 run_tests.py
```

有个报错：

`Cloning into 'absl'... fatal: unable to access 'https://github.com/abseil/abseil-cpp/': GnuTLS recv error (-54): Error in the pull function.`

解决：[解决方案](https://blog.csdn.net/weixin_41460054/article/details/129676634)

```shell
git config --global http.postBuffer 524288000
vim ~/.bashrc
# 在这个文件里加入
export GIT_TRACE_PACKET=1
export GIT_TRACE=1
export GIT_CURL_VERBOSE=1
```

```shell
# 最后
python3 install.py --clangd-completer --verbose
# 不行就多试几次！！！ 不加后面的选项试试。然后就可以了！！
python3 install.py --clangd-completer
```

