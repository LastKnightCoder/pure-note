---
title: Shell 中的 until 循环
created: 2023/2/16 00:22:02
updated: 2023/2/16 00:22:02
tags: [shell, until]
---

```shell
until command
do
	commands
done
```

```shell
#!/bin/zsh
i=10
until [ $i -lt 0 ]
do
	echo $i
	i=$[ $i - 1 ]
done
```