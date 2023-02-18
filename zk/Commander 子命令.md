---
title: Commander子命令
created: 2023/2/14 14:28:57
updated: 2023/2/14 14:42:15
tags: [Commander, 子命令, Node, cli工具]
---

可以通过 `program.addCommand()` 来添加子命令，每个子命令可以定义它们自己的选项

```js
const { program, Command } = require('commander');

const upload = new Command('upload');
upload
  .option('-f, --file <file>', 'file to upload')
  .action((options) => {
    console.log('upload file', options.file);
  });

const download = new Command('download');
download
  .option('-f, --file <file>', 'file to download')
  .action((options) => {
    console.log('download file', options.file);
  });

program.addCommand(upload);
program.addCommand(download);

program.parse();
```

```shell
$ node demo.js upload --file hello.svg
upload file hello.svg

$ node demo.js download --file hello.svg
download file hello.svg
```

每个子命令也可以添加它们自己的子命令

```js
const { program, Command } = require('commander');

const wechat = new Command('wechat');
wechat.command('login')
  .option('-u, --user-name <userName>', 'user name')
  .option('-p, --password <password>', 'password')
  .action((options) => {
    console.log('wechat preview', options);
  });

wechat.command('logout')
  .action(() => {
    console.log('wechat logout');
  });

program.addCommand(wechat);
program.parse();

```

```shell
$ node demo.js wechat login --user-name Alice --password 123456
wechat preview { userName: 'Alice', password: '123456' }

$ node demo.js wechat logout                                   
wechat logout
```