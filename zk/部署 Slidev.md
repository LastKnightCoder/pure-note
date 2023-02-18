---
title: 部署 Slidev
created: 2023/2/12 12:26:04
updated: 2023/2/12 12:26:04
tags: [Slidev, Github Actions, 部署, 使用技巧]
---

## 方法一：手动部署

修改 `npm scripts`

```json
"build": "slidev build --base /${repo}/"
```

将上面的 `${repo}` 替换为仓库名称。

提交代码：

```shell
git init
git add .
git commit -m "~~~"
git remote add origin ${repoUrl}
git push -u origin master
```

提交 build 后的代码到 gh-pages

```shell
npm run build
cd dist
git init
git add .
git commit -m "~~~"
git remote add origin ${repoUrl}
git checkout -b gh-pages
git push -u origin gh-pages
```

## 方法二：Github Action

```yaml
name: Build and Deploy
on:
  push:
    branches:
      - master
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v2.3.1

      - name: Set Node Version
        uses: actions/setup-node@v1
        with:
          node-version: '16.x'

      - name: Install and Build 🔧
        run: |
          npm install
          npm run build
        env:
          NODE_OPTIONS: '--max_old_space_size=4096'

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@4.1.3
        with:
          BRANCH: gh-pages # The branch the action should deploy to.
          FOLDER: dist # The folder the action should deploy.
```