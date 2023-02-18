---
title: WebStorm 中报错：Specify a path to the es;int package
created: 2023/2/12 12:06:51
updated: 2023/2/12 12:06:51
tags: [WebStorm, eslint, 解决办法]
---

WebStorm 提示 

```console
EsLint: Specify a path to the 'eslint' package
```

解决办法：全局下载 `eslint` 然后重启 IDE

```shell
npm install eslint -g
```