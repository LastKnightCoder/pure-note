---
title: gzip 压缩与解压缩
created: 2023/2/16 00:18:55
updated: 2023/2/16 00:18:55
tags: [gzip, Linux命令]
---

```bash
# 压缩文件，文件会被删除
gzip 文件
```

```bash
# 解压缩，file 会被删除
gzip -d 压缩文件
# 或
gunzip 压缩文件
```

```note
`gzip` 只能压缩文件。
```