# Git 使用规范流程

团队协作开发必须有一个规范的工作流程，遵循一个合理、清晰的 Git 使用流程，让大家有效地合作，使得项目井井有条地发展下去。

否则，每个人都提交一堆杂乱无章的 commit，项目很快就会变得难以协调和维护。

## 第一步：新建分支

每次开发新功能，或者解决一个线上 Bug，都应该新建一个单独的分支，开发新功能基于 Develop 分支新建，解决 Bug 基于 Master 分支新建。

```
# 切到主干分支
$ git checkout develop

# 与主干同步
$ git pull --rebase origin develop

# 新建一个开发分支myfeature
$ git checkout -b myfeature
```

## 第二步：提交分支 commit

分支编码完成后，就可以提交 commit 了

```
# all 参数，表示保存所有变化（包括新建、修改和删除）。
# 从 Git 2.0 开始，all 是 git add 的默认参数，所以也可以用 git add . 代替
# 将已跟踪文件中的修改和删除的文件添加到暂存区：git add -u
$ git add -all

# 查看发生变动的文件
$ git status

# 提交信息
$ git commit
```

## 第三步：撰写提交信息

提交 commit 时，必须给出完整扼要的提交信息，下面是一个范本

```
Present-tense summary under 50 characters

* More information about commit (under 72 characters).
* More information about commit (under 72 characters).

http://project.management-system.com/ticket/123
```

第一行是不超过50个字的提要，然后空一行，罗列出改动原因、主要变动、以及需要注意的问题。最后，提供对应的网址（比如Bug ticket）。

如果 commit 注释写错了，只是想改一下注释，只需要：

```
$ git commit --amend
# 此时会进入默认 vim 编辑器，修改注释完毕后保存就好了
```

## 第四步：推送到远程仓库

```
# 同步代码，多人开发，远端可能已经有别人提交的代码
$ git pull --rebase origin myfeature

# 推送到远端
git push origin myfeature
```

参考：

http://www.ruanyifeng.com/blog/2015/08/git-use-process.html
