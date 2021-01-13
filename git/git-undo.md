# 如何撤销 Git 操作

Git 版本管理时，往往需要撤销某些操作。

## 撤销工作区的文件修改

如果工作区的某个文件被改乱了，但还没有提交，可以用 git checkout 命令找回本次修改之前的文件

```
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复暂存区的所有文件变化
$ git checkout -f
```

## 从暂存区撤销文件

如果不小心把一个不需要的文件添加到了暂存区，可以用下面的命令撤销
```
# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [filename]

# 删除工作区文件，并且将这次删除放入暂存区，本地文件也删除了
$ git rm [filename]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

## 撤销提交

git reset 的原理是，让最新提交的指针回到以前某个时点，该时点之后的提交都从历史中消失。
```
# 重置暂存区所有文件，但工作区不变
$ git reset HEAD

# 重置暂存区所有文件，与上一次 commit 保持一致，但工作区不变
$ git reset HEAD^

# 回退 hello.php 文件与上一次 commit 保持一致
$ git reset HEAD^ hello.php

# 重置暂存区与工作区，与上一次 commit 保持一致
$ git reset --hard HEAD^

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]
```

## 撤销提交

一种常见的场景是，提交代码以后，你突然意识到这个提交有问题，应该撤销掉，这时执行下面的命令就可以了
```
$ git revert HEAD
```

上面命令的原理是，在当前提交后面，新增一次提交，抵消掉上一次提交导致的所有变化。它不会改变过去的历史，所以是首选方式，没有任何丢失代码的风险。

git revert 命令还有两个参数
* --no-edit：执行时不打开默认编辑器，直接使用 Git 自动生成的提交信息
* --no-commit：只抵消暂存区和工作区的文件变化，不产生新的提交
