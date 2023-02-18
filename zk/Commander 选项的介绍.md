---
title: Commander选项的基本介绍
created: 2023/2/13 18:28:02
updated: 2023/2/13 19:04:17
tags: [Commander, 选项, npm包]
---

Commander 选项分为短选项和长选项，短选项使用 `-` 开头，接**单个字符**，长选项使用 `--` 开头，后接一个或多个单词（使用 `-` 连接）。

```js
const { program } = require('commander');

// 短选项 -n，长选项 --name
program.option('-n, --name');
program.parse();

const options = program.opts();
console.log(options);
```

```shell
$ node demo.js -n    
{ name: true }
$ node demo.js --name
{ name: true }
```

选项后可跟参数和不跟参数，不跟参数的称为 Boolean 型选项，跟参数的称为带参数选项。对于 Boolean 型选项，如果命令行中指定了选项，那么值就是 `true`，反之就是 `false`。对于带参数选项，首先需要在选项后用 `<>` 声明，`<>` 中的字符随意，但是最好有意义，比如类型或者解释说明。

要获取选项值，如果声明了长选项，则可以通过 `opts().长选项` 获取，如果没有声明长选项，则可以通过 `opts().短选项` 获取。

如果长选项是多个单词，会被转换为驼峰命名。

```js
const { program } = require('commander');

program
  .option('-m, --man', '是否是男性')
  .option('-h <high>', '身高')
  .option('-a, --age <age>')
  .option('--first-name <name>')

program.parse();

const options = program.opts();
console.log(options);
```

```shell
$ node demo.js -m -h 168 --age 18 --first-name Alice
{ man: true, h: '168', age: '18', firstName: 'Alice' }
```

选项的传递有四种方式

```shell
-p80
-p 80
--port 80
--port=80
```
