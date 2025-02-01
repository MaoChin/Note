# `git`初识

工作区：创建`git`仓库的目录。

暂存区（`index/stage`）：`git add`后工作区的修改会加入到暂存区中。

版本库：`git commit`后暂存区的修改会加入到本地的版本库中。最后的`git push`就是把本地`git`仓库的内容同步到远端。

## 1. `.git`目录

`HEAD`：默认就是`./refs/heads/master`

`./refs/heads/master`：保存当前`master`分支的最新`commit id`/当前版本的`id`。

## 2. 版本回退

### 2.1 还没有`add`的代码

对于**仅仅在工作区进行修改的部分**（还没有`add`和`commit`），此时进行回退当然可以使用`git diff`找到修改的部分然后删除掉即可。

但是非常低效！！

此时使用`git checkout -- [filename]`可以直接使文件恢复到上一次`add`或`commit`的情况。

### 2.2 已经`add`，但是没有`commit`的代码

直接使用：

`git reset --mixed HEAD [filename]`。`mixed`表示把==暂存区==的代码进行回退，不会回退工作区，要回退工作区再使用`checkout`即可。

`HEAD`是想要回退到的目的版本`id`。

### 2.3 已经`commit`的代码

直接使用：

`git reset --hard HEAD [filename]`。`hard`表示把==工作区和暂存区==都进行回退。

`HEAD`是想要回退到的目的版本`id`。

另外还有一个`--soft`参数表示==只回退版本库==。

#### 2.3.1 删除文件

删除已经`commit`的文件可以使用`git rm [filename]`然后再`commit`。

其实直接在工作区把文件删除然后再`add`，`commit`也可以。









