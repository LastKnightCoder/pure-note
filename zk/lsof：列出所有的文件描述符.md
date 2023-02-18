---
title: lsof：列出所有的文件描述符
created: 2023/2/16 00:24:23
updated: 2023/2/16 00:24:23
tags: [lsof, 文件描述符]
---

`lsof` ：

- -p 指定进程 id
- -d 指定文件描述符
- -a and 运算符

```bash
# $$ 当前进程
xt in ~/shell/redir λ lsof -a -p $$ -d 0,1,2,3
COMMAND PID USER   FD   TYPE DEVICE SIZE             NODE NAME
zsh       9   xt    1u   CHR    4,1      7599824371328025 /dev/tty1
zsh       9   xt    2u   CHR    4,1      7599824371328025 /dev/tty1
zsh       9   xt    0u   CHR    4,1      7599824371328025 /dev/tty1
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image-20211224213632328.png" style="zoom:50%;" />

```shell
xt in ~/shell/redir λ ./07.sh
COMMAND PID USER   FD   TYPE DEVICE SIZE              NODE NAME
07.sh   783   xt    0u   CHR    4,1       7599824371328025 /dev/tty1
07.sh   783   xt    1u   CHR    4,1       7599824371328025 /dev/tty1
07.sh   783   xt    2u   CHR    4,1       7599824371328025 /dev/tty1
07.sh   783   xt    3r   REG    0,2   16  7036874418635432 /home/xt/shell/redir/names
07.sh   783   xt    4w   REG    0,2    0 28428972647778939 /home/xt/shell/redir/out
```