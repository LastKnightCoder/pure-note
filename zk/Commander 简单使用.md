---
title: Commander的简单使用
created: 2023/2/13 17:42:59
updated: 2023/2/13 18:22:08
tags: [Commander, 命令行, npm包, demo]
---

下载：

```shell
npm install commander
```

首先明白，Commander 的作用是什么，是解析命令行参数，让我们能够以易读且方便的拿到我们想要的选项和参数，而不用手动解析字符串。

举个🌰：

```js
const { program } = require('commander');

// 定义要解析的选项
program.option('-n, --name', '这是对选项的说明', '这是默认值');

// 解析命令行参数
program.parse();

// 获取被解析的选项
const options = program.opts();
console.log(options);
```

```shell
$ node demo.js --name
{ name: true }
```

