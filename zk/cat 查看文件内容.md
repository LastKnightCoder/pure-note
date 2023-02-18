---
title: 使用cat查看文件内容
created: 2023/2/15 22:37:50
updated: 2023/2/15 22:37:50
tags: [cat, Linux, 命令]
---

通过 `cat` 命令可以查看文件的内容

```bash
# 查看 hello.c 的文件内容
~ » cat hello.c
#include<stdio.h>
int main() {
    printf("Hello World!");
    return 0;
}
```

我们可以添加 `-n` 选项来显示行号

```bash
# 查看 hello.c 的文件内容，并显示行号
~ » cat -n hello.c
     1  #include<stdio.h>
     2  int main() {
     3      printf("Hello World!");
     4      return 0;
     5  }
```

`cat` 可以查看多个文件，并将其内容依次显示在屏幕上

```bash
~ » cat hello.c hello.md
#include<stdio.h>
int main() {
    printf("Hello World!");
    return 0;
}
## Hello World

This is markdown file!
```

`cat` 的特点是一次性读取文件的所有的内容，然后全部显示在屏幕上，所以对于比较长的文件，我们比较难以阅读，需要滚动屏幕才可以读取到前面的内容。


```` info
有一个与 `cat` 相似的命令是 `tac`，`cat` 反着写就是它了，它的作用也是查看文件，不过是倒序输出的

```bash
~ » tac hello.c
}
    return 0;
    printf("Hello World!");
int main() {
#include<stdio.h>
```

可以通过 `man tac` 了解该命令的更多信息，该命令在实际中并不常用，了解即可。
````