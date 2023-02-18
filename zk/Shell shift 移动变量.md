---
title: 使用shift移动变量，逐个读取参数
created: 2023/2/15 23:00:11
updated: 2023/2/15 23:00:11
tags: [shift, shell]
---

`shift` 可以移动变量，默认移动一步，将 `$2` 移动到 `$1`，`$1` 的值会被丢弃，所以请小心

```shell
#!/bin/zsh

while [ -n "$1" ]
do
	echo "$1"
	shift
done
```

```bash
xt in ~/shell/read λ ./shift.sh Alice Bob Candy David
Alice
Bob
Candy
David
```

`shift n` 一次性移动多个位置

```shell
xt in ~/shell/read λ cat shift2.sh
#!/bin/zsh
while [ -n "$1" ]
do
	echo "$1"
	shift 2
done

xt in ~/shell/read λ ./shift2.sh Alice Bob Candy David
Alice
Candy
```