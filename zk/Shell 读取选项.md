---
title: shell读取选项
created: 2023/2/15 23:01:07
updated: 2023/2/15 23:01:07
tags: [shell]
---

```bash
xt in ~/shell/read λ cat readoptions.sh
#!/bin/zsh
while [ -n "$1" ]
do
	case "$1" in
		-a) echo "find -a option";;
		-b) echo "find -b option";;
		*) echo "$1 option not exists";;
	esac
	shift
done

xt in ~/shell/read λ ./readoptions.sh -a -b -c -a
find -a option
find -b option
-c option not exists
find -a option
```

分离选项和参数，Linux 标准方法，使用特殊字符分开，特殊字符 `--`

```shell
xt in ~/shell/read λ cat optandparam.sh
#!/bin/zsh
while [ -n "$1" ]
do
	case "$1" in
		-a) echo "find opt $1";;
		-b) echo "find opt $2";;
		--) shift
			break;;
		*) echo "$1 not defined";;
	esac
	shift
done
while [ -n "$1" ]
do
	echo "param $1"
	shift
done

xt in ~/shell/read λ ./optandparam.sh -a -b -c -d -- t1 t2 t3
find opt -a
find opt -c
-c not defined
-d not defined
param t1
param t2
param t3
```

选项带有参数

```shell
#!/bin/zsh

while [ -n "$1" ]
do
	case "$1" in
		-a)
			echo "find opt $1"
			echo "find $1's param $2"
			shift ;;
		-b) echo "find opt $1";;
		--)
			shift
			break;;
		*) echo "$1 not defined";;
	esac
	shift
done

while [ -n "$1" ]
do
	echo "param $1"
	shift
done
```

```bash
xt in ~/shell/read λ ./optwithparam.sh -a alice -b -c -- t1 t2 t3
find opt -a
find -a's param alice
find opt -b
-c not defined
param t1
param t2
param t3
```