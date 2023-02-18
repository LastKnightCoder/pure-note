---
title: Linux中的重定向
created: 2023/2/16 00:23:20
updated: 2023/2/16 00:23:40
tags: [重定向, Linux]
---

## 输入与输出

Linux 使用文件描述符标识文件，非负整数。前三个文件描述符保留（0，1，2）

- 0：标准输入，STDIN
- 1：标准输出，STDOUT
- 2：标准错误，STDERR

标准输入：键盘输入。`<` 会重定向指定文件来替换标准输入，读取文件获得输入，就像是从键盘上输入的。

标准输出：显示器。大多数命令将输出导向标准输出。错误输出和普通输出是分开的，`>` 只能重定向普通输出。

标准错误：默认 STDOUT 和 STDERR 会输出到同一位置，虽然文件描述符不同。

重定向错误 `2>`，**紧紧挨着**。

```bash
xt in ~ λ ls -l dasda
ls: cannot access 'dasda': No such file or directory
xt in ~ λ ls -l dasda 2> err.txt
xt in ~ λ cat err.txt
ls: cannot access 'dasda': No such file or directory
```

同时重定向标准输出和标准错误：

```shell
xt in ~/tmp λ ls
hello.md  qq

# 默认都输出在屏幕
xt in ~/tmp λ ls -l hello.md bb
ls: cannot access 'bb': No such file or directory
-rw-r--r-- 1 xt xt 0 Dec 21 21:24 hello.md

# 标准输出重定向到 stdout，标准错误重定向到 stderr
xt in ~/tmp λ ls -l hello.md bb 1> stdout 2> stderr

xt in ~/tmp λ cat stdout
-rw-r--r-- 1 xt xt 0 Dec 21 21:24 hello.md
xt in ~/tmp λ cat stderr
ls: cannot access 'bb': No such file or directory
```

标准输出和错误都输出在一个文件中，通过 `&>`

```bash
# 都输出到 common 文件
xt in ~/tmp λ ls -l hello.md bb &> common
# 查看文件内容
xt in ~/tmp λ cat common
ls: cannot access 'bb': No such file or directory
-rw-r--r-- 1 xt xt 0 Dec 21 21:24 hello.md
```

## 临时重定向

```bash
# 输出临时重定向到标准错误
echo "This is a error" >&2
```

在文件描述符前面添加 `&` 就是临时重定向

```shell
#!/bin/zsh

echo "This is a error." >&2
echo "This normal output."
```

```bash
xt in ~/shell/redir λ ./01.sh 2> err
This normal output.
xt in ~/shell/redir λ cat err
This is a error.
```

## 永久重定向

`exec` 在 shell 脚本执行期间，重定向到某个特定描述符

```shell
#/bin/zsh

exec 1> stdo
exec 2> stde

echo "This is error." >&2
echo "This is normal output."
```

```bash
xt in ~/shell/redir λ cat stdo
This is normal output.
xt in ~/shell/redir λ cat stde
This is error.
```

## 脚本中重定向输入

```shell
# 从 names 文件读取输入
exec 0< names
while read name
do
	echo "Hello, $name."
done
```

```bash
xt in ~/shell/redir λ cat names
Alice
Bob
Candy
xt in ~/shell/redir λ ./03.sh
Hello, Alice.
Hello, Bob.
Hello, Candy.
```

## 创建重定向

Linux Shell 最多可以有 9 个文件描述符，其他 6 个 3~9 可用于重定向，可将其任一个分配给文件。

```shell
# 创建输出重定向
exec 3> out
echo "Hello World." >&3
echo "Hi." >&3 
```

```shell
# 追加
exec 3>> out
echo "Hello World." >&3
echo "Hi." >&3 
```

重定向文件描述符，然后恢复

```shell
# 3 -> 1 -> stdo
exec 3>&1
# 会输出到标准输出
echo "Hello World." >&3
# 1 -> output
exec 1> output
# Hi. 会被输出到 
echo "Hi."
# 1 -> 3 -> stdo，恢复 1 到标准输出
exec 1>&3
```

创建输入文件描述符

```shell
# 6 <- 0，以便后续恢复
exec 6<&0
exec 0< names

while read name:
do
	echo "Hello, $name."
done

# 恢复
exec 0<&6
```

```bash
xt in ~/shell/redir λ cat names
Alice
Bob
Candy

xt in ~/shell/redir λ ./05.sh
Hello, Alice.
Hello, Bob.
Hello, Candy.
```

创建读写文件描述符，文件描述符可以同时指向输出和输入，需要注意的是

```shell
#!/bin/zsh

exec 3<> names

# 读第一行，并打印
read name <&3
echo "$name."

# 向第二行起始写入,覆盖原内容
# Bob -> ddb，前两个字符 Bo 被 dd 覆盖
echo "dd" >&3
```

```bash
xt in ~/shell/redir λ ./06.sh
Alice.
xt in ~/shell/redir λ cat names
Alice
ddb
Candy
```

关闭描述符

```shell
exec 3>&-
```

关闭之后，不能继续使用，否则会出现 Bad file descriptor.

如果关闭之后又打开，会用一个新的文件替换已有文件，原文件会被覆盖

```shell
# 打开文件描述符
exec 3> out
# 输出内容
echo "The first echo."
# 关闭文件描述符
exec 3>&-

# 重新打开
exec 3> out
# 会覆盖之前的输出
echo "The second echo."
```