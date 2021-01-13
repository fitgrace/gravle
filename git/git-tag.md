# Git 标签

## 用途

Git 可以给仓库历史中的某一个提交打上标签，以示重要。 比较有代表性的是人们会使用这个功能来标记发布结点（ v1.0 、 v2.0 ...）。

## 标签分类

Git 标签分为两种类型
* 轻量标签：是指向提交对象的引用
* 附注标签：是仓库中的一个独立对象

建议使用附注标签，日后还可以查看标签信息。

## 创建标签

创建轻量标签：创建轻量标签不需要传递参数，直接指定标签名称即可。
```
$ git tag [tag-name]
```

创建附注标签：创建附注标签时，参数 -a 即 annotated 的缩写，指定标签类型，后附标签名。参数 -m 指定标签说明，说明信息会保存在标签对象中。
```
$ git tag -a [tag-name] -m 'release 0.1.0 version'
```

## 查看标签

列出当前仓库的所有标签
```
git tag
```

列出符合模式的标签
```
git tag -l 'v0.1.*'
```

查看标签版本信息
```
git show v0.1.0
```

## 切换标签

切换标签与切换分支命令相同：切换标签后处于一个空的分支上，即：You are in ‘detached HEAD’ state.
```
$ git checkout [tag-name]
```

## 补打标签

给指定的 commit 打标签：打标签不必要在 HEAD 之上，也可在之前的版本上打，这需要你知道某个提交对象的校验和，通过 git log 命令获取。
```
$ git tag -a [tag-name] [commit-id]
```

## 删除标签

误打或需要修改标签时，需要先将标签删除，再打新标签
```
# 删除本地 tag
$ git tag -d [tag-name]

# 删除远程 tag
$ git push origin :refs/tags/[tagName]
```

## 发布标签

将标签提交到 Git 服务器：通常的 git push 不会将标签对象提交到 Git 服务器，我们需要进行显式的操作。
```
git push origin [tab-name]
```

将本地所有标签一次性提交到 Git 服务器
```
$ git push origin --tags
```

## 新建一个分支，指向某个 Tag

```
$ git checkout -b [branch-name] [tag-name]
```
