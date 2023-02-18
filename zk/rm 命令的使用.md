---
title: Linux：rm 命令的使用
created: 2023/2/12 12:37:07
updated: 2023/2/12 12:37:10
tags: [Linux, 命令, rm, 删除, 文件, 文件夹]
---

`rm` 用来删除文件和文件夹

```bash {10-11}
~ » tree
.
├── dst
│   ├── hello.c
│   ├── hello.md
│   └── hello2.md
└── src

2 directories, 3 files
# 删除 dst 下的 hello2.md
~ » rm dst/hello2.md
~ » tree
.
├── dst
│   ├── hello.c
│   └── hello.md
└── src

2 directories, 2 files
```

因为 `Linux` 没有回收站，所以删除操作是一个很危险的操作，我们可以为 `rm` 命令添加 `-i` 选项，它会在删除文件之前进行询问

```bash
~ » rm -i dst/hello.md
rm: remove regular empty file 'dst/hello.md'? y
~ » tree
.
├── dst
│   └── hello.c
└── src

2 directories, 1 file
```

当我们删除不存在的文件时，会给出错误提示该文件不存在

```bash
~ » rm dst/hello.md
rm: cannot remove 'dst/hello.md': No such file or directory
```

此时我们可以添加 `-f` 选项，`-f` 表示强制删除，不管其存在与否，当添加该选项以后，就不会显示任何的提示信息，即使该文件不存在，或者添加了 `-i` 选项，都不会有任何的提示信息

```bash
# 即使文件不存在也不会有任何的提示信息
~ » rm -f dst/hello.md
# 即使有 -i 选项，但是因为添加了 -f 选项后也不会询问
~ » rm -if dst/hello.c
~ » tree
.
├── dst
└── src

2 directories, 0 files
```

`rm` 除了可以删除文件外，还可以删除文件夹，不过不能直接删除文件夹，需要添加 `-r` 选项，`-r` 选项表示递归删除的意思，添加该选项后可以删除文件夹下的所有内容(包括该文件夹)

```bash
# 直接删除，删除失败
~ » rm src
rm: cannot remove 'src': Is a directory
# 添加 -r 选项，删除成功
~ » rm -r src
~ » tree
.
└── dst

1 directory, 0 files
```