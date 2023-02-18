---
title: Shell 中捕获信号
created: 2023/2/16 00:35:31
updated: 2023/2/16 00:35:31
tags: [trap, 信号]
---

```shell
trap commands signal
```

当捕获到 `signal` 信号，执行 `commands`

```shell
#!/bin/zsh

trap "echo 'Sorry, I trap the Ctrl + C signal.'" SIGINT

for (( i = 1; i < 10; i++ ))
do
	echo "number$i"
	sleep 1
done
```

```bash
xt in ~/shell/sig λ ./01.sh
number1
number2
^CSorry, I trap the Ctrl + C signal.
number3
number4
number5
^CSorry, I trap the Ctrl + C signal.
number6
number7
number8
^CSorry, I trap the Ctrl + C signal.
number9
```

捕获脚本退出信号 `EXIT`

```shell
#!/bin/zsh

trap "echo 'Goodbye ...'" EXIT

for (( i = 1; i <= 3; i++ ))
do
	echo "number$i"
	sleep 0.5
done
```

```bash
xt in ~/shell/sig λ ./02.sh
number1
number2
number3
Goodbye ...
```

在脚本不同位置设置捕获（修改捕获）

```shell
#!/bin/zsh

trap "echo 'I set trap.'" SIGINT
for (( i = 1; i <= 3; i++ ))
do
	echo "first loop, number$i"
	sleep 1
done

trap "echo 'I modified trap.'" SIGINT
for (( i = 1; i <= 3; i++ ))
do
	echo "second loop, number$i"
	sleep 1
done
```

```bash
xt in ~/shell/sig λ ./03.sh
first loop, number1
first loop, number2
^CI set trap.
first loop, number3
second loop, number1
second loop, number2
^CI modified trap.
second loop, number3
```

删除捕获

```shell
# 删除信号，双（单）破折号
trap -- SIGINT
trap - SIGINT
```
