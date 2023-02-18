---
title: DFT变换的性质
author: 熊滔
time: 2021-04-03
category: 数字信号处理
commentid: signal-process:dft-xingzhi
---

## 线性性质

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230547.png" alt="image-20210403230547414" style="zoom:50%;" />

## 时移性质

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230610.png" alt="image-20210403230610766" style="zoom:50%;" />

## 频移性质

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230632.png" alt="image-20210403230632645" style="zoom:50%;" />

## 时域反转

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230650.png" alt="image-20210403230650878" style="zoom:50%;" />

## 时域共轭

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230711.png" alt="image-20210403230711539" style="zoom:50%;" />

由上面两个可以推得

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230727.png" alt="image-20210403230727300" style="zoom:50%;" />

## 对称性质

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230745.png" alt="image-20210403230745182" style="zoom:50%;" />

## 卷积性质

假设$x[n],w[n]$都是长度为$N$的有限长序列，它们的DFT分别为$X[k],W[k]$,假设它们的有值区间为$0 \leq n \leq N-1$,那么它们进行圆周卷积的DFT为：

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403230821.png" alt="image-20210403230821142" style="zoom:50%;" />

上式中用到了
$$
\frac{1}{N}\sum_{n=0}^{N-1}W_N^{k-r}=
\begin{cases}
1, k -r = lN , \, l=0,1,...\\\\
0, other
\end{cases}
$$
## Parseval定理

$$
\begin{aligned}
\sum_{n=0}^{N-1}x[n]y^{*}[n]&=\sum_{n=0}^{N-1}x[n](\frac{1}{N}\sum_{k=0}^{N-1}Y[k]W_N^{-kn})^{*}\\
&=\frac{1}{N}\sum_{k=0}^{N-1}Y^{*}[k]\sum_{n=0}^{N-1}x[n]W_N^{kn}\\
&=\frac{1}{N}\sum_{k=0}^{N-1}X[k]Y^{*}[k]
\end{aligned}
$$

特别的，当$x[n]=y[n]$时

$$
\sum_{n=0}^{N-1}\vert x[n]\vert^2=\frac{1}{N}\sum_{k=0}^{N-1}\vert X[k]\vert^2
$$