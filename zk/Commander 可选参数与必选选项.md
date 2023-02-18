---
title: Commander中的可选参数与必选选项
created: 2023/2/14 11:08:41
updated: 2023/2/14 11:22:19
tags: [Commander, 选项, 可选参数, 必选选项]
---

对于选项参数的读取是贪婪的，如果定义了某个选项后需要跟参数，但是实际上没有给参数，那么会把后面的选项读取为参数

```js
const { program } = require('commander');

program
  .option('-m, --man', '是否是男性')
  .option('-h <high>', '身高')
  .option('-a, --age <age>')
  .option('--first_name <name>')

program.parse();

const options = program.opts();
console.log(options);
```

```shell
$ node demo.js -m -h -a 18
{ man: true, h: '-a' }
```

可以看到 `-a` 选项被视为了 `-h` 的参数。如果选项后的参数是可选的，我们应该在选项后使用 `[]` 而不是 `<>`

```js
program
  .option('-m, --man', '是否是男性')
  .option('-h [high]', '身高')
  .option('-a, --age <age>')
  .option('--first_name <name>')
```

```shell
$ node demo.js -m -h -a 18
{ man: true, h: true, age: '18' }
```

有的时候，程序必须接受一些选项才能工作，这时可以通过 `requiredOption()` 来定义必选选项，如果没有传递该选项，将会导致错误，直接退出程序

```js
// 必须传递 --first-name 选项
program
  .option('-m, --man', '是否是男性')
  .option('-h [high]', '身高')
  .option('-a, --age <age>')
  .requiredOption('--first-name <name>')
```

```shell
$ node demo.js -m -h -a 18
error: required option '--first-name <name>' not specified
$ node demo.js -m -h -a 18 --first-name Alice
{ man: true, h: true, age: '18', firstName: 'Alice' }

```