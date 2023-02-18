---
title: cd 命令的使用
created: 2023/2/16 00:27:26
updated: 2023/2/16 00:27:26
tags: [cd, Linux命令]
---

`cd` 表示 change directory，它的作用是用来切换工作目录的，它的基本使用如下

```bash
cd dir
```

`dir` 是一个路径，它可以是相对路径，也可以是绝对路径

```bash
# 切换到 /tmp 目录
~ » cd /tmp
/tmp » ls
math  tmpscfytyzt  tmux-1000
# 切换到工作目录下的 math 目录
/tmp » cd math
/tmp/math » ls
add  add.c
```

在 Linux 中有几个特别的目录，`~` 表示用户的家目录，因此通过 `cd ~` 可以切换到家目录，或者直接使用 `cd` 命令，不指定任何的路径也可以直接切换到家目录。

```bash
# 切换到家目录
cd ~
# 不指定任何路径，也表示切换到家目录
cd
```

还有两个特殊的目录，`.` 表示当前目录，`..` 表示上一级目录，在每个目录中都有这两个特殊目录，这两个目录都是隐藏目录，可以通过 `ls -a` 命令查看隐藏文件

```bash
~ » ls -a
.              .cache      .local           .sudo_as_admin_successful       hello
..             .conda      .motd_shown      .viminfo                        hello.c
.Xauthority    .condarc    .oh-my-zsh       .zcompdump                      math
.bash_history  .config     .profile         .zcompdump-DESKTOP-PUSQM6M-5.8
.bash_logout   .landscape  .python_history  .zsh_history
.bashrc        .lesshst    .ssh             .zshrc
```

一般我们在进行移动，拷贝操作的情况下使用 `.`，将一些文件移动或拷贝到当前目录下；我们一般使用 `cd ..` 来回到上级目录。

```` info
`cd` 命令还有一个特殊的用法，`cd -` 可以回到上次所在的目录

```bash
# 当前目录为家目录
~ » ls
hello  hello.c  math
# 切换到 /tmp 目录
~ » cd /tmp
# 回到上次所在的目录，也就是家目录
/tmp » cd -
~
~ » ls
hello  hello.c  math
```
````