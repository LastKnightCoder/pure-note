---
title: Shell 中的 while 循环
created: 2023/2/16 00:21:44
updated: 2023/2/16 00:21:44
tags: [shell, while]
---

```shell
while command
do
	commands
done
```

```bash
xt in ~/shell/for λ cat 06.sh
#!/bin/zsh
i=10
while [ $i -gt 0 ]
do
	echo $i
	i=$[ $i - 1 ]
done

xt in ~/shell/for λ ./06.sh
10
9
8
7
6
5
4
3
2
1
```