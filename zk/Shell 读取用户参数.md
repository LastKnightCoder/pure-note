---
title: shell 读取用户参数
created: 2023/2/15 22:38:57
updated: 2023/2/15 22:38:57
tags: [shell]
---

```bash
./add.sh 10 20
```

如何拿到 `10` 和 `20` 这两个参数。

- `$0`：调用脚本的命令
- `$n`：输入参数
  - `$1`：10
  - `$2`：20

```bash
xt in ~/shell/read λ cat greet.sh
#!/bin/zsh
name=$1
echo "Hello, $name"

xt in ~/shell/read λ ./greet.sh Alice
Hello, Alice
```

参数不止 9 个，`${10}` 读取。

```bash
xt in ~/shell/read λ basename /home/xt/shell/read/greet.sh
greet.sh
xt in ~/shell/read λ basename ./greet.sh
greet.sh
```