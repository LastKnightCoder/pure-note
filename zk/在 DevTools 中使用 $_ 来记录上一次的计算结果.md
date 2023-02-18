---
title: 在 DevTools 中使用 $_ 来记录上一次的计算结果
created: 2023/2/12 11:46:51
updated: 2023/2/12 12:00:07
tags: [Chrome, DevTools, $_, 使用技巧]
---

有的时候你需要验证一个想法，或者尝试某个 API 的使用，但是你不想新建一个 JavaScript 文件，这个时候你会打开控制台。

在控制台进行计算时，有的时候需要用到上一步的计算结果继续运算，但是我们忘了将其保存在变量中，这时我们可以通过 `$_` 来获得上一次的运行结果

![](https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/devtools-$_.6yqwfpv6yb40.webp)


