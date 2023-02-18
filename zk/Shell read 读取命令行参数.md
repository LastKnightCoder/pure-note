---
title: 使用read读取命令行参数
created: 2023/2/15 23:02:37
updated: 2023/2/15 23:17:15
tags: [read, Linux, 命令行]
---

`read` 命令用以读取用户输入，然后将其放入一个变量中

- `-p`：提示消息
- `-t`：过期时间，时间内未输入，退出码非 0

```shell
#!/bin/bash

read -p "Please input your name: " name
echo "Hello, $name."
```

```bash
xt in ~/shell/read λ ./read.sh
Please input your name: Alice
Hello, Alice.
```

当我使用 `zsh` 执行时，会报错

```shell
#!/bin/zsh

read -p "Please input your name: " name
echo "Hello, $name."
```

```bash
xt in ~/shell/read λ ./read.sh
./read.sh:read:3: -p: no coprocess
Hello, .
```

在 `zsh` 中  `-p` 意思是 `Input is read from the coprocess.`，正确方法，在提示字符串中前加上 `?`

```shell
#!/bin/zsh

read "?Please input your name: " name
echo "Hello, $name."
```

```bash
xt in ~/shell/read λ ./read_zsh.sh
Please input your name: Alice
Hello, Alice.
```

- `-s`：隐藏输入，例如输入密码的时候

```shell
#!/bin/zsh

read -s "?Pleasr input your password: " password
echo
echo "Your password is $password."
```

```bash
xt in ~/shell/read λ ./read_password.sh
Pleasr input your password:
Your password is hello.
```

没有使用变量接收，可以通过 `REPLY` 环境变量读取

```shell
#!/bin/zsh

read "?your name? "
echo "your name is $REPLY."
```

```bash
xt in ~/shell/read λ ./reply.sh
your name? Alice
your name is Alice.
```

