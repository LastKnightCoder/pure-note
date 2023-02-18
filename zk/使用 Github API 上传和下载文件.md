---
title: 使用 Github API 上传和下载文件
created: 2023/2/12 12:21:23
updated: 2023/2/12 12:21:23
tags: [Github, API, demo]
---

## 下载包

```bash
pnpm add octokit
```

```js
import { Octokit } from 'octokit'
import * as fs from 'node:fs/promises'
const token = ''

const octokit = new Octokit({
  auth: token
})
```

## 获取仓库中的文件

```js
const repoName = 'upload-test'
const owner = 'LastKnightCoder'

;(async () => {
  const res = await octokit.request(`GET /repos/${owner}/${repoName}/contents/README.md`, {
    owner: owner,
    repo: repoName,
    path: 'README.md'
  })
  const content = await Buffer.from(res.data.content, 'base64').toString()
})()
```

## 上传文件

```js
const repoName = 'upload-test'
const owner = 'LastKnightCoder'
const content = await fs.readFile(filename, 'binary')
const filename = 'book.svg'

;(async () => {
  const res = await octokit.request(`PUT /repos/${owner}/${repoName}/contents/${filename}`, {
    owner: user,
    repo: repoName,
    path: filename,
    message: 'upload svg using github api',
    content: Buffer.from(content, 'binary').toString('base64')
  })
})()
```

