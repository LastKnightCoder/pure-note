---
title: 读取用户输入中的特殊参数
created: 2023/2/15 22:40:29
updated: 2023/2/15 22:40:29
tags: [$#, $@, $*, shell]
---

1. `$#`：参数个数

   获得最后一个参数

   ```shell
   # 错误写法
   ${$#}
   ```

   不能在 `{}` 里面使用美元符 `$`，得改为 `!`

   ```shell
   # 非常奇怪，但是很有用
   ${!#}
   ```

2. 获得所有参数

   - `$*`：所有参数视作整体
   - `$@`：分开保存，可用 `for` 遍历

   ```shell
   #!/bin/bash
   
   echo $*
   echo $@
   
   for arg in "$*"
   do
	   echo $arg
   done
   
   for arg in "$@"
   do
	   echo $arg
   done
   ```

   ```bash
   xt in ~/shell/read λ ./args.sh 1 2 3 4
   1 2 3 4
   1 2 3 4
   1 2 3 4
   1
   2
   3
   4
   ```

   > 为什么将 `$*` 和 `$@` 放在双引号里面和不放在双引号里面的值不一样
   >
   > ```bash
   > #!/bin/bash
   > 
   > echo $*
   > echo $@
   > 
   > # 没有双引号
   > for arg in $*
   > do
   >     echo $arg
   > done
   > 
   > # 没有双引号
   > for arg in $@
   > do
   >     echo $arg
   > done
   > ```
   >
   > ```bash
   > xt in ~/shell/read λ ./args.sh 1 2 3 4
   > 1 2 3 4
   > 1 2 3 4
   > 1
   > 2
   > 3
   > 4
   > 1
   > 2
   > 3
   > 4
   > ```
   >
   > 此时 `$*` 和 `$@` 没有区别，But Why？