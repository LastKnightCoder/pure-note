---
title: bizp2 压缩与解压缩
created: 2023/2/16 00:20:18
updated: 2023/2/16 00:20:18
tags: [bzip2, Linux命令]
---

`gzip` 的升级版本，增加选项 `-k`(keep)，后缀名 `.bz2`

- `-k`：保留原文件

	```bash
	bzip2 -k math.sh
	```

	```bash
	# -j：使用 bzip2 压缩
	tar -jcvf shell.tar.bz2 shell
	```

	```bash
	# 解压缩
	bzip2 -d math.sh.bz2
	# 或
	bunzip2 math.sh.bz2
	```