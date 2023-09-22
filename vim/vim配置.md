# `vim`配置

## 一.  `.vimrc` 文件

`vimrc`文件是`vim`的环境设置文件。

整体的`vim`的设置是在 `/etc/vimrc` 文件中。

不建议修改`/etc/vimrc `文件，每个用户可以在用户根目录中设置`vim`，新建` ~/.vimrc`，在这个文件里配置自己的`vim`。

## 二.  .`viminfo`文件

`~/.viminfo `文件是系统自动生成。

在vim中操作的行为，`vim`会自动记录下来，保存在` ~/.viminfo `文件中。
这样为了方便下次处理。如：`vim`打开文件时，光标会自动在上次离开的位置显示。
原来搜索过的字符串，新打开文件时自动高亮显示等。

## 三.  `plug`安装

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