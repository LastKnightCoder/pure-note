---
title: Linux 中的相对路径与绝对路径
created: 2023/2/16 00:26:49
updated: 2023/2/16 00:27:06
tags: [Linux, 路径]
---

很多的命令都接收一个路径作为参数，例如 `ls` 命令可以接收一个路径，查看该路径下包含的文件及文件夹。路径可以分为两种，绝对路径和相对路径，我们知道 Linux 的系统是一个树型结构，树的根就是根目录，它包含一系列的目录，这些目录又包含了更多的目录

```bash
/
├── bin -> usr/bin
├── boot
├── dev
│   ├── block
│   ├── fd -> /proc/self/fd
│   ├── pts
│   └── shm -> /run/shm
├── etc
│   ├── NetworkManager
│   ├── PackageKit
│   ├── UPower
│   └── *****
├── home
│   └── xt
├── lib -> usr/lib
├── lib32 -> usr/lib32
├── lib64 -> usr/lib64
├── libx32 -> usr/libx32
├── media
├── mnt
│   ├── c
│   ├── d
│   └── g
├── opt
├── proc
│   ├── 1
│   ├── 775
│   └── *****
├── root [error opening dir]
├── run
│   ├── WSL
│   ├── lock
│   ├── mount
│   └── *****
├── sbin -> usr/sbin
├── snap
│   ├── bin
│   ├── core18
│   ├── lxd
│   └── snapd
├── srv
├── sys
│   ├── block
│   ├── bus
│   ├── class
│   └── *****
├── tmp
│   ├── math
│   └── tmux-1000
├── usr
│   ├── bin
│   ├── config
│   ├── games
│   └── *****
└── var
    ├── backups
    ├── cache
    ├── crash
    └── *****
```

所谓的绝对路径就是从根目录 `/` 开始表示一个路径，例如 `/home/xt` 这样的格式便为绝对路径，它表示根目录下的 `home` 目录下的 `xt` 目录，绝对路径始终以 `/` 开头。

当我们登录到系统中时，会自动的进入当前用户的家目录，对于用户名为 `username` 的用户，它的家目录为 `/home/username`，我们把当前所在的目录称为工作目录，而另一种表示路径的方法是相对路径，是相对于工作路径的表示。

我们当前位于 `home/xt` 目录下，其中包含的文件及文件夹如下

```bash
.
├── hello
├── hello.c
└── math
    ├── add
    └── add.c
```

我们查看 `math` 文件夹中包含的内容便可以使用 `ls -l math`

```bash
~ » ls -l math
total 20
-rwxr-xr-x 1 xt xt 16800 Nov 20 11:08 add
-rw-r--r-- 1 xt xt   126 Nov 20 11:08 add.c
```

其中 `math` 就是相对路径，它是相对于工作目录的路径，而工作目录为 `/home/xt`，所以 `ls -l math` 就相当于 `ls -l /home/xt/math` 这个绝对路径。
