---
title: rpm查询
created: 2023/2/12 18:57:57
updated: 2023/2/12 18:57:57
tags: [Linux, rpm]
---

```bash
# 查询包是否安装
rpm -q 包名
# 查询所有已安装的包

# 查询安装包信息
rpm -qi 包名
```

```bash
# 查询包中文件安装位置
rpm -ql 包名
```

```bash
# 查询文件属于哪个包，文件不能是你自己手动创建的
rpm -qf 系统文件名
```

```bash
# 查询包的依赖性
rpm -qR 包名
```