---
title: Linux 阻止命令输出
created: 2023/2/16 00:25:28
updated: 2023/2/16 00:25:28
tags: [undefined]
---

将文件描述符重定向到 `/dev/null`，任何输出到 `/dev/null` 的内容都会被直接丢掉

```bash
xt in ~/shell/redir λ ls -al > /dev/null
xt in ~/shell/redir λ
```

如何将输入重定向到 `/dev/null`，则不会有内容输入。