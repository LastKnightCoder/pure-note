---
title: Linux：touch 命令的使用
created: 2023/2/12 12:31:23
updated: 2023/2/12 12:31:23
tags: [Linux, touch, 文件, 命令]
---

`touch` 方法可以新建一个空文件，如果该文件已经存在，则只会更新该文件最后的修改时间为当前时间。

```bash
# 当前目录下只有 hello.c 这个文件
~ » ls -l
total 0
-rw-r--r-- 1 xt xt 75 Nov 20 18:34 hello.c
# 创建 hello.md 这个空白文件
~ » touch hello.md
~ » ls -l
total 0
-rw-r--r-- 1 xt xt 75 Nov 20 18:34 hello.c
-rw-r--r-- 1 xt xt  0 Nov 20 18:34 hello.md
# 过了一分钟，再次执行 touch hello.md
# 因为该文件已经存在，所以只会更新 hello.md 最后的修改时间
~ » touch hello.md
~ » ls -l
total 0
-rw-r--r-- 1 xt xt 75 Nov 20 18:34 hello.c
-rw-r--r-- 1 xt xt  0 Nov 20 18:35 hello.md
```