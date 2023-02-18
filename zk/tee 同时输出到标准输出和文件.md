---
title: tee 同时输出刀标准输出和文件
created: 2023/2/16 00:26:13
updated: 2023/2/16 00:26:13
tags: [tee]
---

`tee` 同时输出到标准输出和文件

```bash
xt in ~/shell/redir λ date | tee datefile
Fri Dec 24 21:50:23 CST 2021
xt in ~/shell/redir λ cat datefile
Fri Dec 24 21:50:23 CST 2021
```

追加到文件，使用 `-a` 选项。