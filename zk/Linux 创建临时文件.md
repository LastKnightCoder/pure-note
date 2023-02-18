---
title: Linux 创建临时文件
created: 2023/2/16 00:25:52
updated: 2023/2/16 00:25:52
tags: [mktemp]
---

1. `mktemp filename.XXXXXX`
   - XXXXXX 是模板，会被随机成六个字符
2. `mktemp -t filename.XXXXXX`
   - 在 `tmp` 目录创建文件
3. `mktemp -d dirname.XXXXXX`
   - 创建目录