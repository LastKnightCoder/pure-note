---
title: rmdir的使用
created: 2023/2/15 22:18:41
updated: 2023/2/15 22:18:41
tags: [rmdir, Linux, 命令行]
---

使用 `rmdir` 可以删除一个空文件夹

```bash
~ » rmdir dir
~ » tree
.
├── d1
│   └── d2
│       └── d3
└── dir1
    └── dir2
        └── dir3

6 directories, 0 files
```

但是我们试图删除一个非空的文件夹时，便会报错

```bash
~ » rmdir d1
rmdir: failed to remove 'd1': Directory not empty
```

因为 `d1` 里面还有文件夹存在，所以无法通过 `rmdir` 删除。我们可以通过 `-p` 选项递归删除空文件夹，当指定了该选项后，如果删除该文件夹之后导致其父文件夹为空的话，那么也会删除其父文件夹。


```bash
~ » rmdir -p d1/d2/d3
~ » tree
.
└── dir1
    └── dir2
        └── dir3

3 directories, 0 files
```

当我们删除 `d3` 这个文件夹后，其父文件夹 `d2` 也变为空，因此也会被删除，当 `d2` 被删除后，会导致 `d1` 也变为空，因此 `d1` 也会被删除。

但是如果文件夹中包含文件，`rmdir` 便无法删除，这时我们一般会使用 `rm -rf 文件夹` 来删除，其中 `-r` 选项表示递归删除，删除该目录下及其子目录下的所有内容，`-f` 表示强制删除。

```bash
# 创建一个文件
~ » touch dir1/dir2/dir3/hello.c
~ » tree
.
└── dir1
    └── dir2
        └── dir3
            └── hello.c

3 directories, 1 file
# 试图使用 rmdir 删除，删除失败，因为文件夹里面有文件
~ » rmdir -p dir1/dir2/dir3
rmdir: failed to remove 'dir1/dir2/dir3': Directory not empty
# 使用 rm -rf 删除，会删除该文件夹包含的所有内容以及该文件夹
~ » rm -rf dir1
~ » tree
.

0 directories, 0 files
```

``` danger
有的时候可以在网上看到评论 `rm -rf /*`，该条命令表示删除根目录下的所有东西，执行这条命令之后系统会直接崩溃，天王老子也救不了你，所以这条危险的命令千万不要尝试。
```
