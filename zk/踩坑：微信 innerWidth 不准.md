---
title: 踩坑：微信 innerWidth 不准
created: 2023/2/12 12:05:10
updated: 2023/2/12 12:05:10
tags: [踩坑, 微信, innerWidth, JavaScript]
---

在浏览器中通过 `window.innerWidth` 获取屏幕宽度时，有的手机在微信上面获得的数字相比于其它应用获得的数字偏小。

我的手机是 Redmi K40S，在高德上的屏幕大小是 393，在微信上获得的大小是 360。试过了很多办法，微信上都是偏小的。