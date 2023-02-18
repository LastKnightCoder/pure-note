---
title: ffmpeg：剪切视频与合并视频
created: 2023/2/12 12:08:29
updated: 2023/2/12 12:08:29
tags: [ffmpeg, 视频处理, 剪切视频, 合并视频]
---

剪切视频

```shell
ffmpeg -ss 00:00:00 -t 00:00:30 -i test.mp4 -vcodec copy -acodec copy output.mp4
* -ss 指定从什么时间开始
* -t 指定需要截取多长时间
* -i 指定输入文件
```

合并视频

```shell
//截取从头开始的30s
ffmpeg -ss 00:00:00 -t 00:00:30 -i keyoutput.mp4 -vcodec copy -acodec copy split.mp4
//截取从30s开始的30s
ffmpeg -ss 00:00:30 -t 00:00:30 -i keyoutput.mp4 -vcodec copy -acodec copy split1.mp4
//进行视频的合并
ffmpeg -f concat -i list.txt -c copy concat.mp4
```

```console
# list.txt
file ./split.mp4
file ./split1.mp4
```