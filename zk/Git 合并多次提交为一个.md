---
title: Git 合并多次提交为一个
created: 2023/2/16 23:19:59
updated: 2023/2/16 23:42:44
tags: [git, rebase, 使用技巧]
---

在工作中经常会频繁的 commit 代码，但是过多的提交信息，让人很难发现我们到底做了什么功能，最好是在最终提交之前，能把我们的 commit 合并为一个，那么问题是如何进行合并呢。

首先新建一个仓库，创建一个 `hello.txt`，然后进行提交

```shell
$ git init
$ touch hello.txt
$ git add .
$ git commit -m "init"
$ git log --oneline
f743f33 (HEAD -> master) init
```

下面我们要开发一个新功能，切换到 `feature` 分支，并创建了三个提交，每次提交都是向 `hello.txt` 中添加一行

```shell
$ git checkout -b feature
$ git log --oneline
0f5f898 (HEAD -> feature) test: c
6d4e731 fix: b
bad5b39 feat: a
f743f33 (master) init
```

我们想把最近三次的提交合并为一个提交，首先找到我们想合并为一个提交的前一个提交的 commit id，即 init 的 commit id f743f33，然后执行 `git rebase -i f743f33`

```shell
$ git rebase -i f743f33
```

接着会出现打开一个 vim 编辑器，内容如下

```shell
pick bad5b39 feat: a
pick 6d4e731 fix: b
pick 0f5f898 test: c

# Rebase f743f33..0f5f898 onto f743f33 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
```

前面三行是我们的三个提交，下面都是操作说明，其中有一行表示

```shell
# s, squash <commit> = use commit, but meld into previous commit
```

我们把要与前面的 commit 合并的提交由 `pick` 改为 `s`，即

```shell
pick bad5b39 feat: a
s 6d4e731 fix: b
s 0f5f898 test: c
```

然后保存退出，接下来会又会打开一个 vim 编辑器，让你写合并为一个提交后的 commit 信息

```shell
# This is a combination of 3 commits.
# This is the 1st commit message:

feat: a

# This is the commit message #2:

fix: b

# This is the commit message #3:

test: c
```

简单的把上面的内容改为

```shell
feat: feature
```

然后保存退出，这次再次查看提交信息，发现那三个合并为一个了

```shell
$ git log --oneline
5a92146 (HEAD -> feature) feat: feature
f743f33 (master) init
```

如果多个 commit 之间有冲突，我们需要先解决冲突，然后运行下面的代码继续

```shell
git add .
git rebase --continue
```

如果要终止合并，可以运行

```shell
git rebase --abort
```