---
title: zip压缩与解压缩
created: 2023/2/16 00:19:51
updated: 2023/2/16 00:19:51
tags: [zip, Linux命令]
---

```bash
zip [-r] 压缩后的文件名 文件或目录
```

- `-r`：压缩目录

```ad-note
压缩比并没有 `gzip` 高，所以不常用。
```

```bash
# 解压缩
unzip 压缩文件
```