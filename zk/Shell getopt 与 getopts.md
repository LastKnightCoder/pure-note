---
title: getopt与getopts的使用
created: 2023/2/15 23:01:49
updated: 2023/2/15 23:01:59
tags: [getopt, getopts]
---

## getopt

`getopt `可以格式化命令行选项，`getopt optstring parameters`，`optstring` 定义选项字符串，带有参数的选项后面加上 `:`，如 `ab:cd`

```bash
xt in ~/shell/read λ getopt ab:cd -a -b alice -cd t1 t2 t3
 -a -b alice -c -d -- t1 t2 t3
xt in ~/shell/read λ getopt ab:cd -a -b alice -cd -e bob t1 t2 t3
getopt: invalid option -- 'e'
 -a -b alice -c -d -- bob t1 t2 t3
 # -q 不显示报错信息
 xt in ~/shell/read λ getopt -q ab:cd -a -b alice -cd -e bob t1 t2 t3
 -a -b 'alice' -c -d -- 'bob' 't1' 't2' 't3'
```

在脚本中，利用 `getopt` 格式化的参数替换命令行参数

```shell
# set -- 可以将选项替换为命令行参数
set -- $(getopt -q ab:cd "$@")
```

`getopt` 使用空格分隔参数

```bash
# 无法将 "t3 t4" 视为一个参数
getopt ab:cd -ab t1 -cd t2 "t3 t4" t5
```

## getopts

`getopts` 用于 shell 脚本中，它一次处理一个命令行选项，当处理完所有选项后，会返回大于 0 的退出码

```bash
getopts optstring var
```

`optstring` 的定义同 `getopt`

```shell
#!/bin/zsh
while getopts :ab:cd var
do
	case "$var" in
		a)
			echo "$var opt";;
		b)
			echo "$var opt with param $OPTARG";;
		c)
			echo "$var opt";;
		d)
			echo "$var opt";;
		*)
			echo "unknown $var";;
	esac
done

shift $[ $OPTIND - 1 ]

for param in "$@"
do
	echo "param: $param"
done
```

1. 如果选项带有参数，可以通过 `$OPTARG` 全局变量取得
2. `getopts` 读取到的选项**没有** `-`
2. `getopts` 会自动去掉 `--`，不用手动处理 `--`
3. 处理完**选项**后 `getopts` 会自动退出，接下来可以处理参数
4. 通过 `$OPTIND` 获得 `getopts` 处理到哪个位置了，调用 `shift`，然后处理参数
4. 参数中可以包含空格 `"t3 t4"`
4. 未定义的选项，如 `e`，统一处理为 `?`
4. 不显示错误信息，在 `optstring` 前加上 `:`，如 `:ab:cd`

```bash
xt in ~/shell/read λ ./getopts.sh -ab t1 -cde t1 "t2 t3"
a opt
b opt with param t1
c opt
d opt
unknown ?
param: t1
param: t2 t3
```