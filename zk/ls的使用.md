---
title: ls的使用
created: 2023/2/15 22:20:33
updated: 2023/2/15 22:20:33
tags: [ls, Linux]
---

`ls` 命令的作用是查看指定目录下包含的所有文件及目录。

## 基本用法

```shell
# 查看当前目录包含的文件及目录
~ » ls
hello  hello.c  math
```

当前目录包含两个文件 `hello` `hello.c` 和一个目录 `math`。

``` info
上述 `ls` 命令没有指定查看哪个目录的内容，默认就是查看当前所在目录(工作路径)包含的内容。
```

## 查看所有内容

`ls` 不会显示隐藏的文件和目录，在 `Linux` 中，以 `.` 开头的文件名或目录名就认为是隐藏文件(夹)，如果需要查看隐藏内容，可以添加 `-a` 选项

```shell
# 查看当前目录包含的所有内容，包含隐藏目录
~ » ls -a
.              .cache      .local           .sudo_as_admin_successful       hello
..             .conda      .motd_shown      .viminfo                        hello.c
.Xauthority    .condarc    .oh-my-zsh       .zcompdump                      math
.bash_history  .config     .profile         .zcompdump-DESKTOP-PUSQM6M-5.8
.bash_logout   .landscape  .python_history  .zsh_history
.bashrc        .lesshst    .ssh             .zshrc
```

``` tip
上述显示的以 `.` 开头的文件就是隐藏的文件，隐藏文件的作用并不是想让你找不到，我们通过特定的命令还是能看到的，之所以隐藏它们，是因为它们比较重要，一般是一些配置信息，它以这种方式告知我们，除非你对其配置比较了解，否则不要进行随意修改。
```

## 递归查看目录

`ls` 只能查看一个层级的内容，所以当前目录下的 `math` 目录下的文件是查看不了的，如果希望进一步查看所包含目录的里面的内容，那么可以通过 `-R` 选项做到这件事情

```shell
# 递归显示目录
~ » ls -R
.:
hello  hello.c  math

./math:
add  add.c
```

## 查看详细信息

如果希望显示有关文件更多的信息，例如文件权限，文件大小等等，我们可以添加 `-l` 选项显示更多信息

```shell
# 显示文件的详细信息
~ » ls -l
total 32
-rwxr-xr-x 1 xt xt 16696 Nov  9 16:10 hello
-rw-r--r-- 1 xt xt   168 Nov  9 15:51 hello.c
drwxr-xr-x 1 xt xt  4096 Nov  9 16:09 math
```

所显示文件信息依次为：

- 文件类型，比如目录(d)、文件(-)、链接文件(l)、字符型文件(c)、块设备(b)
- 文件权限(rwxr-xr-x)
- 文件硬链接总数(1)
- 文件归属者的用户名(xt)
- 文件归属组的组名(xt)
- 文件大小(16696，以字节为单位)
- 文件的上次修改时间(16:10)
- 文件名或目录名(hello)

``` info
`-l` 中的 `l` 表示的是 `long` 的意思。
```

## 人性化显示文件大小

有关文件大小那一列，我们发现它是以字节为单位，我们可能很难马上反应过来有多大，我们可以添加 `-h` 选项，以更加人性化的方式显示文件的大小

```shell
# 选项没有先后顺序，-h -l 也是可以的
~ » ls -l -h
total 32K
-rwxr-xr-x 1 xt xt  17K Nov  9 16:10 hello
-rw-r--r-- 1 xt xt  168 Nov  9 15:51 hello.c
drwxr-xr-x 1 xt xt 4.0K Nov  9 16:09 math
```

上面文件的大小就带有单位了，没有单位就表示以字节为单位，它会以合适的单位显示文件的大小，方便我们查看。

``` tip
上面我们将选项 `-l -h` 分开书写，我们其实还可以将它们合并在一起，所以上面的命令还可以写为 `ls -lh`，
```

``` info
`-h` 中的 `h` 表示的是 `human` 的意思。
```


## 查看指定文件和目录的信息

在上面我们一直没有指定目录，这种情况下默认就是查看当前所在的工作目录，我们当然可以指定查看具体的某个目录

```shell
# 查看根目录下的文件
~ » ls -l /
total 1364
lrwxrwxrwx  1 root root       7 Aug 20 05:39 bin -> usr/bin
drwxr-xr-x  1 root root    4096 Aug 20 05:52 boot
drwxr-xr-x  1 root root    4096 Nov  9 14:20 dev
drwxr-xr-x  1 root root    4096 Nov  9 14:20 etc
drwxr-xr-x  1 root root    4096 Oct  3 10:53 home
-rwxr-xr-x  1 root root 1392928 Oct  8 12:48 init
lrwxrwxrwx  1 root root       7 Aug 20 05:39 lib -> usr/lib
lrwxrwxrwx  1 root root       9 Aug 20 05:39 lib32 -> usr/lib32
lrwxrwxrwx  1 root root       9 Aug 20 05:39 lib64 -> usr/lib64
lrwxrwxrwx  1 root root      10 Aug 20 05:39 libx32 -> usr/libx32
drwxr-xr-x  1 root root    4096 Aug 20 05:40 media
drwxr-xr-x  1 root root    4096 Oct  3 10:51 mnt
drwxr-xr-x  1 root root    4096 Aug 20 05:40 opt
dr-xr-xr-x  9 root root       0 Nov  9 14:20 proc
drwx------  1 root root    4096 Nov  8 14:46 root
drwxr-xr-x  1 root root    4096 Nov  9 14:20 run
lrwxrwxrwx  1 root root       8 Aug 20 05:39 sbin -> usr/sbin
drwxr-xr-x  1 root root    4096 Aug 20 05:43 snap
drwxr-xr-x  1 root root    4096 Aug 20 05:40 srv
dr-xr-xr-x 12 root root       0 Nov  9 14:20 sys
drwxrwxrwt  1 root root    4096 Nov  9 16:10 tmp
drwxr-xr-x  1 root root    4096 Aug 20 05:42 usr
drwxr-xr-x  1 root root    4096 Aug 20 05:43 var
```

也可以指定特定的文件，查看特定文件的信息

```shell
~ » ls -l hello.c
-rw-r--r-- 1 xt xt 168 Nov  9 15:51 hello.c
```

但是我们怎么查看目录的具体信息，因为我们指定某个目录，会直接查看该目录下的包含的文件(夹)，而不是显示目录的具体信息

```shell
~ » ls -l /home
total 0
drwxr-xr-x 1 xt xt 4096 Nov  9 19:31 xt
```

我们想查看 `/home` 目录的具体信息，但是通过 `ls -l /home` 命令其实是查看 `/home` 目录下有什么，要查看 `/home` 目录的信息，我们需要添加 `-d` 选项

```shell
~ » ls -d -l /home
drwxr-xr-x 1 root root 4096 Oct  3 10:53 /home
```

## 模糊匹配

有的时候我们可能不记得文件或目录的具体名称，这个时候我们可以使用元字符进行模糊匹配：

- `?`：表示任意一个字符
- `*`：表示零个或多个字符

```shell
~ » ls
hello  hello.c  hello.md  math
~ » ls -l hello.*
-rw-r--r-- 1 xt xt 168 Nov  9 15:51 hello.c
-rw-r--r-- 1 xt xt   0 Nov  9 19:50 hello.md
~ » ls -l he?lo*
-rwxr-xr-x 1 xt xt 16696 Nov  9 16:10 hello
-rw-r--r-- 1 xt xt   168 Nov  9 15:51 hello.c
-rw-r--r-- 1 xt xt     0 Nov  9 19:50 hello.md
```

## 总结

总结一下各个选项及功能：

| 选项 | 功能 |
| -- | -- |
| `-a` |  查看所有文件(包括隐藏文件) |
| `-R` | 递归查看目录中的内容 |
| `-l` | 查看文件的详细信息 |
| `-h` | 人性化显示文件大小 |
| `-d` | 查看目录的详细信息 |
