# Git 分支管理策略

对于 Git 分支管理的策略，应该秉持可以使得版本库的演进保持简洁，主干清晰，各个分支各司其职、井井有条。

## 主分支 Master

代码库应该有一个、且仅有一个主分支。所有提供给用户使用的正式版本，都在这个主分支上发布，Git 主分支的名字，默认叫做 Master。

## 开发分支 Develop

主分支只用来分布重大版本，日常开发应该在另一条分支上完成。我们把开发用的分支，叫做 Develop

如果想正式对外发布，就在 Master 分支上，对 Develop 分支进行"合并"（merge）

创建 Develop 分支

```
$ git checkout -b develop master
```

将 Develop 分支发布到 Master 分支

```
# 切换到 Master 分支
$ git checkout master

# 同步 Master 分支
$ git pull --rebase origin master

# 对 Develop 分支进行合并
$ git merge --no-ff develop

# 推送 Master 分支到远端
git push origin master
```

--no-ff 参数是什么意思：
* 默认情况下，Git 执行"快进式合并"（fast-farward merge），会直接将 Master 分支指向 Develop 分支。
* 使用 --no-ff 参数后，会执行正常合并，在 Master 分支上生成一个新节点。为了保证版本演进的清晰，我们希望采用这种做法。

## 临时性分支

除了常设分支（Master, Develop）以外，还有一些临时性分支，用于应对一些特定目的的版本开发。

临时性分支主要有三种：
* 功能（feature）分支
* 预发布（release）分支
* 修补bug（fixbug）分支

这三种分支都属于临时性分支，使用完以后就删除，使得代码库的常设分支始终只有 Master 和 Develop

### 功能分支

为了开发某种特定功能，而从 Develop 分支上面分出来的。开发完成后，要再并入Develop，分支名字采用 feature-x 形式

创建一个功能分支

```
# 切换到 Develop
$ git checkout develop

# 同步 Develop
$ git pull --rebase origin develop

# 新建功能分支
$ git checkout -b feature-x
```

开发完成后，将功能分支合并到develop分支

```
# 切换到 Develop
$ git checkout develop

# 同步 Develop
$ git pull --rebase origin develop

# 合并分支到 Develop
$ git merge --no-ff feature-x

# 推送 Develop 到远端
$ git push origin develop

# 删除功能分支
$ git branch -d feature-x
```

### 修补 Bug 分支

项目正式发布上线以后，难免会出现 Bug，这时就需要创建一个分支，进行 Bug 修补。

修补 Bug 分支是从 Master 分支上面分出来的。修补结束以后，再合并进 Master 和 Develop 分支，它的命名采用 fixbug-x 形式。

创建一个修补 Bug 分支

```
# 切换到 Master
$ git checkout master

# 同步 Master
$ git pull --rebase origin master

# 新建 Bug 分支
$ git checkout -b fixbug-x
```

修补结束后，合并到 Master 分支
```
# 切换到 Master
$ git checkout master

# 同步 Master
$ git pull --rebase origin master

# 合并分支到 Master
$ git merge --no-ff fixbug-x

# 对合并生成的新节点，做一个标签
$ git tag -a 1.2

# 推送到远端
$ git push origin master
```

再合并到 Develop 分支
```
# 切换到 Develop
$ git checkout develop

# 同步 Develop
$ git pull --rebase origin develop

# 合并分支到 Develop
$ git merge --no-ff fixbug-x

# 推送到远端
$ git push origin develop
```

最后，删除修补 Bug 分支
```
$ git branch -d feature-x
```

### 预发布分支

预发布分支是指发布正式版本之前（即合并到 Master 分支之前），我们需要有一个预发布的版本进行回归测试。

预发布分支是从 Develop 分支上面分出来的，预发布结束后必须合并进 Develop 和 Master 分支。它的命名采用 release-x 形式

创建一个预发布分支
```
# 切换到 Develop
$ git checkout develop

# 同步 Develop
$ git pull --rebase origin develop

# 创建预发布分支
$ git checkout -b release-x
```

确认没有问题后，合并到 Master 分支
```
# 切换到 Master
$ git checkout master

# 同步 Master
$ git pull --rebase origin master

# 合并分支到 Master
$ git merge --no-ff release-x

# 对合并生成的新节点，做一个标签
$ git tag -a 1.0

# 推送到远端
$ git push origin master
```

再合并到 Develop 分支
```
# 切换到 Develop
$ git checkout develop

# 同步 Develop
$ git pull --rebase origin develop

# 合并分支到 Develop
$ git merge --no-ff release-x

# 推送到远端
$ git push origin develop
```

最后，删除预发布分支
```
$ git branch -d release-x
```

参考：

http://www.ruanyifeng.com/blog/2012/07/git.html
