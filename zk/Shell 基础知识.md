---
tags: [shell]
---

1. 执行多条命令

    使用分号隔开：
    
    ```bash
    date; who
    ```
    
2. Shell脚本

   ```shell
   #!/bin/bash
   date
   who
   ```

   添加执行权限

   ```bash
   chmod u+x test.sh
   # 通过相对路径或绝对路径的方式引用
   ./test.sh
   ```

3. 输出消息

   ```shell
   echo Hello World
   echo "Hello World"
   echo 'Hello World'
   ```
   
   > `""` 中的特殊符号具有意义，如 `$` `!`
   >
   > `''` 中的内容会被原样输出，不会被解析
   
   - `echo -n`：不换行输出
   
4. 变量

   1. 环境变量

      ```bash
      # 显示环境变量
      set
      ```

      加上 `$` 引用环境变量

      ```bash
      echo $HOME
      ```

   2. 用户变量

      ```bash
      var1=hello
      var2=11
      ```

      > **等号两侧不能有空格！**

      以示区分，用户自定义变量使用小写，与环境变量（全大写）进行区分，以免混淆。

      ```shell
      #!/bin/bash
      name=Alice
      echo "Hello $name" # Hello Alice
      ```

   3. 命令替换

      将命令输出赋值给变量

      - <code>``</code>
      -  `$()`

      ```shell
      test=`date`
      # or
      test = $(date) # 较常用
      ```

5. 重定向

   1. 输出重定向：`command > outputfile`

      ```bash
      echo "Hello World" > hello
      cat hello
      ```

      `>` 会覆盖原文件内容，`>>` 向文件追加内容。

      ```bash
      echo "Hello World" >> hello
      cat hello
      ```

   2. 输入重定向

      `command < inputfile`

      ```bash
      wc < hello
      ```

      > wc：统计文本
      >
      > 1. 文本行数
      > 2. 文本词数
      > 3. 文本字节数

      内联输入重定向（`<<`），不指定文件，在命令行直接指定输入内容

      ```bash
      xt in ~/shell λ wc << EOF
      heredoc> Hello World
      heredoc> Hi
      heredoc> EOF
       2  3 15
      ```

6. 管道

   将一个命令的输出作为一个命令的输入，`command1 | command2`

   > 不是依次执行，而是会**同时运行**两个命令，**在第一个命令产生输出的同时，输出会被立即送给第二个命令**。

   ```shell
   rpm -qa | sort | more
   ```

7. 数学运算

   1. expr

      ```bash
      expr 1 + 5
      expr 1 \* 5
      ```

      <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3/image-20211218095227305.png" style="zoom:50%;" />
      
    2. 使用方括号

       ```bash
       var1=$[1 + 5]
       var2=$[1 * 5]
       ```
   
       > Bash Shell 只支持整数运算，`10 / 3 → 3`
   
   3. 浮点运算：`bc`
   
      ```bash
      xt in ~/shell λ bc
      bc 1.07.1
      Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
      This is free software with ABSOLUTELY NO WARRANTY.
      For details type `warranty'.
      12 * 1.3
      15.6
      10 / 3
      3
      scale=2
      10/3
      3.33
      ```
   
      ```shell
      var=$(echo "scale=2; 10 / 3" | bc)
      var=$(bc << EOF
      scale = 2
      a = 1.2
      b = 2.3
      a + b
      EOF
      )
      ```
   
8. 退出脚本

   1. 查看状态码

      ```bash
      echo $?
      ```

      成功结束的命令的退出状态码是0，非 0  表示错误

      <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3/image-20211218100505630.png" style="zoom:50%;" />

   2. `exit`

      ```shell
      exit 5 # 0 ~ 255
      exit 300 # -> 44
      ```
