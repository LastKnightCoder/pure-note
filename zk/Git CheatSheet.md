---
title: Git CheatSheet
created: 2023/2/18 19:54:55
updated: 2023/2/18 20:01:18
tags: [undefined]
---

添加与删除远程分支

```shell
# 添加远程分支
git remote add origin https://github.com/user/repo.git
# 删除远程分支
git remote rm origin
```

强制推送

```shell
git push --force
```

放弃本地内容，使用远程仓库的内容

```shell
git fetch --all
git reset --hard origin/master
```
