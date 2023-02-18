---
title: npm 发包流程
created: 2023/2/12 12:04:04
updated: 2023/2/12 12:04:04
tags: [npm, 发包, 流程]
---

如何发布 npm 包。

1. 需要有 npm 账号，前往[官网](https://www.npmjs.com/) 注册账号
2. 确定你的 registry
	  ```shell
	  npm config get registry // 先康康你的是否是npm官方的源，不是请切回
	  npm config set registry https://registry.npmjs.org
	  ```
3. 在命令行登录，输入 `npm adduser` 登录，输入注册时的用户名，密码以及邮箱，注意接收邮箱的验证码
4. 修改 `package.json`
5. `npm publish` 发布