## if-then

```bash
if command
then
	commands
fi
```

`if` 语句后的命令的退出码为 `0` 则位于 `then` 后面的命令被执行。

```bash
#!/bin/zsh

if pwd
then
        echo "It worked"
fi
```

```shell
#!/bin/zsh

if pcd # false
then
        echo "It worked"
fi
```

## if-then-else

```shell
if command
then
	commands
else
	commands
fi
```

## 嵌套 if

```shell
if command
then
	commands
else
	if command
	then
		commands
	fi
fi
```

```shell
if command
then
	commands
elif command
then
	commands
fi
```

## test

`if-then` 不能测试退出码之外的条件，`test` 可以提供帮助。

```bash
test condition
```

`condition` 成立，退出码为 `0`，否则非`0`。

```shell
if test condition
then
	commands
fi
```

```bash
xt in ~/shell/if λ test "Hello"
xt in ~/shell/if λ echo $?
0
xt in ~/shell/if λ test ""
xt in ~/shell/if λ echo $?
1
```

另一种测试方法

```shell
# condotion 与方括号之前需要加上一个空格
if [ condition ]
then 
	commands
fi
```

1. 数值比较

   <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image-20211219123722563.png" alt="image-20211219123722563" style="zoom:50%;" />

   ```bash
   xt in ~/shell/if λ test 1 -lt 2
   xt in ~/shell/if λ echo $?
   0
   xt in ~/shell/if λ test 1 -gt 2
   xt in ~/shell/if λ echo $?
   1
   ```

   > Bash Shell 只能处理整数

2.  字符串比较

    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master//image-20211219124130472.png" alt="image-20211219124130472" style="zoom:50%;" />

     ```bash
     xt in ~/shell/if λ test 'Hello' = "Hello"
     xt in ~/shell/if λ echo $?
     0
     xt in ~/shell/if λ test 'Hello' != "Hello"
     xt in ~/shell/if λ echo $?
     1
     ```

     ```bash
     xt in ~/shell/if λ test -n ''
     xt in ~/shell/if λ echo $?
     1
     xt in ~/shell/if λ test -n 'Hi'
     xt in ~/shell/if λ echo $?
     0
     ```

    >   注意：大于号和小于号要进行转义 `\>` `\<`，否则会被认为是重定向。

3. 文件比较

     <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master//image-20211219125034145.png" alt="image-20211219125034145" style="zoom:50%;" />

     ```bash
     xt in ~/shell/if λ test -d /home/xt
     xt in ~/shell/if λ echo $?
     0
     xt in ~/shell/if λ test -d /home/coder
     xt in ~/shell/if λ echo $?
     1
     ```
  
     ```shell
     #!/bin/zsh
     
     homedir=/home/xt
     
     if [ -d $homedir ]
     then
     	cd $homedir
     	ls -l
     fi
     ```

     ```shell
     #!/bin/zsh
     
     if [ -r /etc/shadow ]
     then
     	cat /etc/shadow
     else
     	echo '/etc/shadow is not readable!'
     fi
     ```

## 复合条件

- `&&`
- `||`

	```shell
	if [ -d /home ] && [ -d /home/xt ]
	then
		cd /home/xt
		ls -l
	fi
	```

## 高级特性

1. 双括号

   可以使用高级数学表达式，`(( expression ))`

   <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master//image-20211219130915454.png" alt="image-20211219130915454" style="zoom:50%;" />

   ```shell
   if (( 10 ** 2 > 90 ))
   then
   	echo "10 square greater than 90"
   fi
   ```

2. 双方括号

   字符串比较，`[[ expression ]]`

   ```shell
   if [[ $user == x* ]]
   then
   	...
   fi
   ```

## case

```shell
case var in
	pattern1 | pattern2) commands1;;
	pattern3) commands2;;
	*) default commands;;
esac
```

```shell
case $user in
	xt)
		echo "user xt";;
	*)
		echo "other user";;
esac
```

