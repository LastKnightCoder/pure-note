---
title: Go包无法下载的解决方案
created: 2023/2/12 14:13:53
updated: 2023/2/12 14:13:53
tags: [Go, 代理, 包下载, 解决方案]
---

Windows 在 PowerShell 中设置

```powershell
$env:GOPROXY = "<https://goproxy.cn,direct>"
```

Linux 直接修改 `.bashrc` 中的环境变量

```bash
GOPROXY="https://goproxy.cn,direct"
```