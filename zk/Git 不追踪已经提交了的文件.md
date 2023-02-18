---
title: Git 不追踪已经提交了的文件
created: 2023/2/18 17:13:49
updated: 2023/2/18 17:20:24
tags: [git, 使用技巧]
---

如何不追踪某些文件？

如果你还没有追踪这个文件，直接在 `.gitignore` 把他添加进去就行了

``` config
# .gitignore
private.key
```

但是如果不小心追踪了某个不应该追踪的文件，比如密钥，这个时候只改 `.gitignore` 是不行的，需要使用 `git rm`

```shell
git rm -r --cached private.key
```

然后把 `private.key` 添加到 `.gitignore` 中，然后重新提交就不会被追踪了。