---
title: Linux：mv 命令的使用
created: 2023/2/12 12:36:04
updated: 2023/2/12 12:36:11
tags: [Linux, mv, 命令, 文件, 移动, 重命名]
---

`mv` 用来移动文件或者重命名文件。

## 重命名文件

```bash {9-10}
# src 是文件夹，其余两个为文件
~ » tree
.
├── hello.c
├── hello.md
└── src

1 directory, 2 files
# 重命名 hello.md 为 hello2.md
~ » mv hello.md hello2.md
~ » tree
.
├── hello.c
├── hello2.md
└── src

1 directory, 2 files
```

```` warning
当移动文件或重命名时 `mv hello.md hello2.md`，如果 `hello2.md` 已经存在，则不会有任何的提示，默认原先的文件会被覆盖，我们同样可以添加 `-i` 选项，如果文件已存在的情况下会进行询问是否覆盖

```bash
~ » mv -i hello.md hello2.md
mv: overwrite 'hello2.md'? n
```
````

## 移动文件

**移动单个文件到文件夹：**

```bash {1-2}
# 移动 hello.c 到 src 文件夹下
~ » mv hello.c src
~ » tree
.
├── hello2.md
└── src
    └── hello.c

1 directory, 2 files
```

**移动多个文件到文件夹：**

```bash {3-4}
# 创建新文件 hello.md
~ » touch hello.md
# 移动多个文件到 src 文件夹下
~ » mv hello.md hello2.md src
~ » tree
.
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

1 directory, 3 files
```

**移动文件夹下的所有文件到另一文件夹：**

```bash {11-12}
# src 中有 3 个文件，dst 为空文件夹
~ » tree
.
├── dst
└── src
    ├── hello.c
    ├── hello.md
    └── hello2.md

2 directories, 3 files
# 移动 src 下所有文件到 dst 中
~ » mv src/* dst
~ » tree
.
├── dst
│   ├── hello.c
│   ├── hello.md
│   └── hello2.md
└── src

2 directories, 3 files
```