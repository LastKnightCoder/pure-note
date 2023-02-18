---
title: Shell 如何为函数传递参数与使用参数
created: 2023/2/16 00:31:37
updated: 2023/2/16 00:32:56
tags: [Shell, 函数]
---

格式 `函数名 params`，然后在函数中通过 `$1, $2` 等进行访问

```note
注意：函数内部的 `$n` 不会影响到外部的。
```

```shell
#!/bin/zsh

rr() {
    echo "param1: $1"
    echo "param2: $2"
}

rr 1 2

# 外部的 $1
echo "$1"
```

```bash
xt in ~/shell/func λ ./04.sh 2

param1: 1
param2: 2
2
```

同样可以在函数里面使用 `$#`，表示参数个数，`$*` 和 `$@`，表示参数列表。

函数内部变量可声明为局部变量，使用 `local`

```shell
#!/bin/zsh

val=10

add() {
    local val=$[ $1 + $2 ]
    echo "$val"
}
res=$(add 1 2)
echo "1 + 2 = $res"

# 外部 val 还是 10，不会受到影响
echo "outer val: $val."
```

```bash
xt in ~/shell/func λ ./05.sh
1 + 2 = 3
outer val: 10.
```

传递数组参数

```shell
#!/bin/zsh

array=(1 2 3 4)

process_data() {
    len=$#
    for (( i = 1; i <= $len; i++ ))
        do
            echo "param$i: $1"
            shift
        done
}

process_data ${array[*]}
```

返回数组

```shell
#!/bin/zsh

function times2 {
    local origin_arr=($(echo "$@"))
    local new_arr=($(echo "$@"))
    local i
    for (( i = 1; i <= $#; i++ ))
    {
        # 数组元素翻倍
        new_arr[$i]=$[ ${origin_arr[$i]} * 2 ]
    }
    # 输出新数组，即返回数组
    echo ${new_arr[*]}
}

arr=(1 2 3 4 5)
# 拿到返回数组
new_arr=$(times2 ${arr[*]})
echo ${new_arr[*]}
```