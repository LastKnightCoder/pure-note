---
title: Shell 中的 for 循环
created: 2023/2/16 00:21:06
updated: 2023/2/16 00:21:06
tags: [for, 循环, shell]
---

1. 语法

    ```shell
    for var in list
    do
      	commands
    done
    ```

2. 例子

    ```shell
    #!/bin/zsh
    # 默认分隔符为【空格|制表符|换行符】
    for name in Alice Bob Candy David Eric Frank
    do
		echo "Hello, my name is $name."
    done
    
    # $name 会一直保持最后的值，也就是 Frank
    ```

    ```bash
    xt in ~/shell/for λ ./01.sh
    Hello, my name is Alice.
    Hello, my name is Bob.
    Hello, my name is Candy.
    Hello, my name is David.
    Hello, my name is Eric.
    Hello, my name is Frank.
    ```

3. 命令替换

    ```shell
    xt in ~/shell/for λ cat names
    Alice Bob
    Candy
    David
    Frank
    
    xt in ~/shell/for λ cat 03.sh
    #!/bin/zsh
    file=names
    for name in $(cat $file)
    do
		echo "Hello, my name is $name."
    done
    
    xt in ~/shell/for λ ./03.sh
    Hello, my name is Alice.
    Hello, my name is Bob.
    Hello, my name is Candy.
    Hello, my name is David.
    Hello, my name is Frank.
    ```

4. 分隔符

   特殊环境变量 `IFS`，默认分隔符为

   - 空格
   - 制表符
   - 换行符

   ```bash
   xt in ~/shell/for λ cat 03.sh
   #!/bin/zsh
   file=names
   # 只识别换行符作为分隔符
   IFS=$'\n'
   for name in $(cat $file)
   do
	   echo "Hello, my name is $name."
   done
   
   xt in ~/shell/for λ ./03.sh
   Hello, my name is Alice Bob.
   Hello, my name is Candy.
   Hello, my name is David.
   Hello, my name is Frank.
   ```

5. 使用通配符读取目录

   ```shell
   #!/bin/zsh
   
   # 可以读取多个目录
   for file in /home/xt/shell/"for"/* /home/xt/shell/"if"/*
   do
	   if [ -d "$file" ]
	   then
		   echo "$file is a directory."
	   elif [ -f "$file" ]
	   then
		   echo "$file is a file."
	   fi
   done
   ```

6. C 语言风格

   ```shell
   for (( variable assignment; condition; iterator process ))
   do
   	commands
   done
   ```

   ```bash
   xt in ~/shell/for λ cat 05.sh
   #!/bin/zsh
   
   for (( i = 1; i < 10; i++ ))
   do
           echo "number$i"
   done
   
   xt in ~/shell/for λ ./05.sh
   number1
   number2
   number3
   number4
   number5
   number6
   number7
   number8
   number9
   ```
