---
title: Shell 在后台运行脚本
created: 2023/2/16 00:36:07
updated: 2023/2/16 00:36:07
tags: [Shell]
---

脚本在后台运行，在命令后加个 `&`

```shell
#!/bin/zsh

count=5
while [ "$count" -gt 0 ]
do
	echo "number$count"
	count=$[ $count - 1 ]
	sleep 1
done
```

```bash
xt in ~/shell/sig λ ./04.sh &
[1] 1568
number5
xt in ~/shell/sig λ number4
number3
number2
number1

[1]  + 1568 done       ./04.sh
```

虽然程序在后台运行，但是输出还是在命令行，后台运行程序会输出

```bash
[1] 1568
```

`[1]` 是作业号，`1568` 是进程号，程序运行结束后输出

```bash
[1]  + 1568 done       ./04.sh
```

如果退出命令行时有后台运行的脚本，则会进行提示，不能退出，再次退出可以进行退出。

如果希望退出命令行后还能在后台执行，使用 `nohup` 命令

```bash
nphup ./04.sh &
```

输出会重定向到 `nohup.out` 文件中

```bash
xt in ~/shell/sig λ nohup ./04.sh &
[1] 1695
xt in ~/shell/sig λ nohup: ignoring input and appending output to 'nohup.out'

[1]  + 1695 done       nohup ./04.sh
xt in ~/shell/sig λ cat nohup.out
number5
number4
number3
number2
number1
```