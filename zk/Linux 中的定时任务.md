---
title: Linux 中的定时任务
created: 2023/2/16 00:37:48
updated: 2023/2/16 00:38:11
tags: [at, cron, 定时任务, Linux]
---

定时运行作业

## at

```bash
at [-f filename] time
```

`at` 可以指定多种不同的 time 格式

- `10:15`
- `10:15 PM`
- `now、noon、midnight、teatime（4 PM）`
- `MMDDYY、MM/DD/YY、DD.MM.YY`
- `Jul 4`
- 时间增量
  - `+25min`

`atq` 列出有哪些作业在等待

```bash
xt in ~/shell/sig λ atq
1       Sat Dec 25 16:20:00 2021 a xt
2       Sat Dec 25 16:20:00 2021 a xt
3       Sat Dec 25 16:30:00 2021 a xt
4       Sat Dec 25 16:31:00 2021 a xt
5       Sat Dec 25 16:51:00 2021 a xt
```

`atrm` 删除作业

```bash
xt in ~/shell/sig λ atq
4       Sat Dec 25 16:31:00 2021 a xt
5       Sat Dec 25 16:51:00 2021 a xt
6       Sat Dec 25 17:38:00 2021 a xt
xt in ~/shell/sig λ atrm 4
xt in ~/shell/sig λ atq
5       Sat Dec 25 16:51:00 2021 a xt
6       Sat Dec 25 17:38:00 2021 a xt
```

## cron

`cron` 时间表

```plain
min hour dayofmonth month dayofweek command
```

可以指定特定值，也可以是取值范围（1~5），又或是通配符。

```bash
# 每天 10:15 执行命令
15 10 * * * command
# 每周一 16:15 执行命令
15 16 * * 1 command
```

列出已有 `cron` 时间表

```bash
crontab -l
```

通过 `-e` 选项添加条目，选择熟悉的编辑器打开

```bash
xt in ~/shell/sig λ crontab -e
no crontab for xt - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/nano        <---- easiest
  2. /usr/bin/vim.basic
  3. /usr/bin/nvim
  4. /usr/bin/vim.tiny
  5. /usr/bin/emacs
  6. /bin/ed

Choose 1-6 [1]:
```

使用预配置的 `cron` 脚本目录，四个目录

- `hourly`
- `daily`
- `monthly`
- `weekly`

```bash
xt in ~/shell/sig λ tree /etc/cron.*ly
/etc/cron.daily
├── apache2
├── apport
├── apt-compat
├── aptitude
├── bsdmainutils
├── cracklib-runtime
├── dpkg
├── locate
├── logrotate
├── man-db
├── popularity-contest
└── update-notifier-common
/etc/cron.hourly
/etc/cron.monthly
/etc/cron.weekly
├── man-db
└── update-notifier-common

0 directories, 14 files
```

将脚本放在 `hourly` 中，那么每小时就会执行一次。

如果到定时任务执行时系统处于关机状态，则会被错过，`anacron` 可以解决，具体解决没怎么看懂。
