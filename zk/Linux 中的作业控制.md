---
title: Linux 中的作业控制
created: 2023/2/16 00:36:40
updated: 2023/2/16 00:37:14
tags: [Shell, job, 作业控制]
---

作业控制：启动、停止、终止、恢复作业。

`jobs` 查看作业，<kbd>Ctrl + Z</kbd> 停止作业。

```shell
#!/bin/zsh

count=3
while [ $count -gt 0 ]
do
	echo "Hello $count."
	sleep 2
	count=$[ $count - 1 ]
done
```

```bash
xt in ~/shell/sig λ ./05.sh
Hello 3.
Hello 2.
^Z
[1]  + 1759 suspended  ./05.sh

xt in ~/shell/sig λ ./05.sh > jobsout &
[2] 1880

xt in ~/shell/sig λ jobs
[1]  + suspended  ./05.sh
[2]  - running    ./05.sh > jobsout
```

`jobs` 中的加号会被当做默认作业，使用作业控制命令时，若未指定作业号，该作业就是操作对象；带减号会成为下一个默认作业。

任何时候只有一个带加号和一个带减号的作业。

`bg + 作业号` 以后台模式重启一个作业；`fg + 作业号` 以前台重启作业。

```` note
在 `zsh` 中，要在作业号前加上 `%`

```bash
fg %1
```
````

调整优先级。优先级从 -20（最高优先级）~19（最低优先级），默认 Bash Shell 以优先级 0  启动进程。

`nice -n level` 指定优先级级别

```bash
nice -n 10 ./04.sh
# -n 10 可直接指定为 -10
nice -10 ./04.sh
```

普通用户只能通过 `nice` 降低优先级

`renice` 改变正在运行的任务优先级，通过指定 `PID` 来修改优先级

```bash
renice -n level -p pid
```

`renice` 的限制：

1. 只能对属于你的进程进行 `renice`
2. 只能通过 `renice` 降低进程的优先级
3. `root` 用户没有限制，superman