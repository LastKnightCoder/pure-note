---
title: tar 归档文件
created: 2023/2/16 00:19:26
updated: 2023/2/16 00:19:26
tags: [tar, Linux命令]
---

打包目录为一个文件，也可以在打包的同时进行压缩

```bash
tar 选项 打包后的文件名 目录
```

- `-c`：打包
- `-v`：显示详细信息
- `-f`：指定文件名

	```bash
	# 将 shell 目录打包为 shell.tar
	tar -cvf shell.tar shell

	# 将打包的文件压缩
	gzip shell.tar
	```

- `-z`：打包的同时使用 gzip 进行压缩

	```bash
	# 打包并压缩
	tar -zcvf shell.tar.gz shell
	```

- `-x`：解包

	```bash
	# 使用 gzip 解压缩，然后解包
	tar -zxvf shell.tar.gz
	```

```note
大部分源代码格式都为 `.tar.gz`，所以解压缩命令挺重要的。
```