---
title: Shell 中函数的创建与使用
created: 2023/2/16 00:29:08
updated: 2023/2/16 00:29:34
tags: [shell, 函数]
---

函数，代码的复用。格式：

```shell
function name {
  commands
}
```

```shell
name() {
  commands
}
```

使用：

```shell
name
```

举个例子：

```shell
#!/bin/zsh

function echo_hello_3_times {
  echo "Hello World."
  echo "Hello World."
  echo "Hello World."
}


echo "call function:\n "
echo_hello_3_times
```

```bash
xt in ~/shell/func λ ./01.sh
call function:

Hello World.
Hello World.
Hello World.
```

```note
1. 在使用函数之前一定要先定义函数，否则会报错
2. 函数名唯一，新定义的函数会覆盖之前的函数
```