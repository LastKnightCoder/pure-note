---
title: 如何在多个脚本中使用函数
created: 2023/2/16 00:33:39
updated: 2023/2/16 00:33:52
tags: [source, Shell]
---

如何在多个脚本中使用函数？

```shell
xt in ~/shell/func λ cat math.sh
#!/bin/zsh

add() {
    echo $[ $1 + $2 ]
}

sub() {
    echo $[ $1 - $2 ]
}

mul() {
    echo $[ $1 * $2 ]
}

div() {
echo $[ $1 / $2 ]
}
```

如何在其他脚本中使用这些函数，`source ./math.sh`，`source` 不会在子 shell 执行 math.sh，而是在当前 shell

```shell
xt in ~/shell/func λ cat 09.sh
#!/bin/zsh

source ./math.sh

res=$(add 1 2)
echo "1 + 2 = $res"

res=$(mul 3 4)
echo "3 * 4 = $res"

xt in ~/shell/func λ ./09.sh
1 + 2 = 3
3 * 4 = 12
```

```tip
`source` 可以简写为 `.`，`. ./math.sh`。
```
