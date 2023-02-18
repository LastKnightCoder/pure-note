---
title: pip 国内镜像源配置
created: 2023/2/12 14:20:57
updated: 2023/2/12 14:20:57
tags: [pip, 国内镜像, 配置]
---

包在国外服务器，下载速度慢，下载位于国内镜像源上的包。通过 `pip` 下载包时通过 `-i` 选项指定镜像源地址

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple
```

每次下载包时都要指定太麻烦，将其写入配置文件，一劳永逸。

Linux：在 `~/.pip/pip.conf` 文件中配置：

```ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

Windows：在 `~/pip/pip.ini` 文件中配置：

```ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```