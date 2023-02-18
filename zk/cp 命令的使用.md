---
title: Linux：cp 命令的使用
created: 2023/2/12 12:32:29
updated: 2023/2/12 12:32:40
tags: [Linux, cp, 复制, 文件, 命令]
---

## 基本用法

`cp` 命令用来复制文件，基本用法

```bash
cp source destination
```

## 复制文件并命名

```bash
# 当前文件夹下内容
~ » ls -l
total 0
-rw-r--r-- 1 xt xt   75 Nov 20 18:34 hello.c
-rw-r--r-- 1 xt xt    0 Nov 20 18:35 hello.md
drwxr-xr-x 1 xt xt 4096 Nov 20 18:51 test
```

```bash
# 复制 hello.md 到当前文件夹下，并命名为 hello2.md
~ » cp hello.md hello2.md
~ » ls
hello.c  hello.md  hello2.md  test
```

````warning
如果当前文件夹下已经存在 `hello2.md`，则会覆盖该文件，**且不会给出任何提示**，我们可以为 `cp` 命令添加 `-i` 选项，如果已经存在同名的文件，它会询问你是否覆盖

```bash
# 输入 y 表示覆盖，输入 n 表示不覆盖
~ » cp -i hello.md hello2.md
cp: overwrite 'hello2.md'? y
```
````

## 复制文件到指定目录

**复制单个文件：**

```bash {1-2,5-6}
# 复制 /etc 文件夹下的 passwd 文件到当前目录
~ » cp /etc/passwd .
~ » ls
hello.c  hello.md  hello2.md  passwd  test
# 复制 hello.md 到当前目录下的 test 文件夹
~ » cp hello.md test
~ » tree
.
├── hello.c
├── hello.md
├── hello2.md
├── passwd
└── test
    └── hello.md

1 directory, 5 files
```

**复制多个文件：**

```bash {1-2}
# 复制多个文件 hello.md, hello2.md 到 test 文件夹
~ » cp hello.c hello2.md test
~ » tree
.
├── hello.c
├── hello.md
├── hello2.md
├── passwd
└── test
    ├── hello.c
    ├── hello.md
    └── hello2.md

1 directory, 7 files
```

**使用通配符复制多个文件：**

```bash
# 复制 hello.md hello2.md ... 到 test 文件夹
cp hello* test
```

````tip
同样你可以添加 `-i` 选项，在覆盖文件之前进行询问。

```shell
~ » cp -i /etc/passwd .
cp: overwrite './passwd'? n
```

将文件移动到某个目录时，也可以为文件重命名

```shell
# 将 /etc/passwd 移动到当前文件夹，并重命名为 password
~ » cp /etc/passwd ./password
~ » ls
hello.c  hello.md  hello2.md  passwd  password  test
```
````

## 复制目录

复制目录时需要添加 `-r` 选项

```shell
cp -r dir1 dir2
```

上述命令有两种情况：

- `dir2` 不存在，则新建 `dir2` 文件夹，并将 **`dir1` 文件夹下的所有文件复制到 `dir2` 下**，注意这里不是复制文件夹，而是复制文件夹下的所有文件

  ```bash {10-11}
  # 当前目录下只有 src 文件夹，src 文件夹下有三个文件
  ~ » tree
  .
  └── src
      ├── hello.c
      ├── hello.md
      └── hello2.md

  1 directory, 3 files
  # 因为 dst 文件夹不存在，所以会新建 dst 文件夹
  # 并把 src 下的所有文件复制到 dst 文件夹下
  ~ » cp -r src dst
  ~ » tree
  .
  ├── dst
  │   ├── hello.c
  │   ├── hello.md
  │   └── hello2.md
  └── src
      ├── hello.c
      ├── hello.md
      └── hello2.md

  2 directories, 6 files
  ```
- `dir2` 已存在，且为文件夹，则将 **`dir1` 文件夹**复制到 `dir2` 下

  ```bash {11-12}
  # dst 文件夹已存在，其中没有任何内容
  ~ » tree
  .
  ├── dst
  └── src
      ├── hello.c
      ├── hello.md
      └── hello2.md

  2 directories, 3 files
  # dst 文件夹已存在，所以会将 src 这个文件夹复制到 dst 文件夹中
  ~ » cp -r src dst
  ~ » tree
  .
  ├── dst
  │   └── src
  │       ├── hello.c
  │       ├── hello.md
  │       └── hello2.md
  └── src
      ├── hello.c
      ├── hello.md
      └── hello2.md

  3 directories, 6 files
  ```