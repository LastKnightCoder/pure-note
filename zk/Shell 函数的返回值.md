---
title: Shell 中函数的返回值
created: 2023/2/16 00:30:27
updated: 2023/2/16 00:31:03
tags: [shell, 函数]
---

函数的退出状态码是最后一条执行语句的退出状态码，通过 `$?` 可以拿到

```shell
#!/bin/zsh

list_file() {
    ls
    # this file is not exists 
    ls -l dad
}

list_file
echo "function exit status is $?."
```

```shell
xt in ~/shell/func λ ./02.sh
01.sh  02.sh
ls: cannot access 'dad': No such file or directory
function exit status is 2.
```

return 命令也可以返回特定的退出码

```shell
#!/bin/zsh

rr() {
    val=1
    return $[ $val * 5 ]
}

rr

echo "exit status: $?."
```

```shell
xt in ~/shell/func λ ./03.sh
exit status: 5.
```

获取函数输出

```shell
dd() {
    value=200
    echo $[ $value * 3 ]
}

res=$(dd)
# 600
echo "$res"
```