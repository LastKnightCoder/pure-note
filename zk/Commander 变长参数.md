---
title: Commander之变长参数
created: 2023/2/14 14:12:52
updated: 2023/2/14 14:24:09
tags: [Commander, Node, 变长参数, cli工具]
---

选项后的参数可能是多个值，使用 `<...>` 或 `[...]` 的语法来表示是变长参数

```js
program
  .option('--book <book>')
  .option('--author <author...>')
  .option('--price <price>', 'book price', parseFloat)
```

```shell
$ node demo.js --book "Book Name" --author Alice Bob --price 23.5
{ book: 'Book Name', author: [ 'Alice', 'Bob' ], price: 23.5 }
```