---
title: mkdir命令的使用
created: 2023/2/15 22:17:49
updated: 2023/2/15 22:17:49
tags: [mkdir, Linux, 命令, 文件夹]
---

使用 `mkdir` 可以创建一个空文件夹

```bash
# 当前文件夹下什么也没有
~ » ll
total 0
# 创建一个名为 dir 的空文件夹
~ » mkdir dir
~ » ll
total 0
drwxr-xr-x 1 xt xt 4.0K Dec  2 21:09 dir
# 查看 dir 中的内容，没有任何东西，是个空文件夹
~ » ls dir
```

有的时候我们想创建一个深层次的文件夹，例如 `dir1/dir2/dir3`，表示在 `dir1/dir2/` 下创建 `dir3`，我们尝试使用 `mkdir` 命令进行创建

```bash
~ » mkdir dir1/dir2/dir3
mkdir: cannot create directory ‘dir1/dir2/dir3’: No such file or directory
```

但是失败了，因为不存在 `dir1` 和 `dir1/dir2` 这两个文件夹，所以无法创建 `dir3`，所以我们必须先创建 `dir1` 和 `dir2`，然后才能创建 `dir3`

```bash
~ » mkdir dir1
~ » mkdir dir1/dir2
~ » mkdir dir1/dir2/dir3
~ » tree .
.
├── dir
└── dir1
    └── dir2
        └── dir3

4 directories, 0 files
```

这样做虽然实现了目的，但是很累，我们可以通过 `-p` 选项来创建文件夹，例如

```bash
~ » mkdir -p d1/d2/d3
~ » tree
.
├── d1
│   └── d2
│       └── d3
├── dir
└── dir1
    └── dir2
        └── dir3

7 directories, 0 files
```

添加了 `-p` 选项之后，如果我们要创建的文件夹的父级文件夹不存在，系统则会帮我们创建，也就是说 `mkdir -p d1/d2/d3`，虽然 `d1` 和 `d2` 文件夹不存在，但是系统会帮我们自动创建。