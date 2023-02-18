---
title: Z变换
author: 熊滔
time: 2021-04-03
category: 数字信号处理
commentid: signal-process:z-transform
---

由于$DTFT$变换是有收敛条件的，并且其收敛条件比较严格，很多信号不能够满足条件，为了有效的分析信号，需要放宽收敛的条件，引入$Z$变换。

## 定义

已知序列的$DTFT$为
$$
X(e^{jw})=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}
$$
当序列$x[n]$不满足收敛条件时，我们让$x[n]$乘以$r^{-n}$使它收敛
$$
\sum_{n=-\infty}^{\infty}x[n]r^{-n}e^{-jwn}
$$
令$z=re^{jw}$得到
$$
X(z)=\sum_{n=-\infty}^{\infty}x[n]z^{-n}
$$
对于所有的$z$上式不一定收敛，所以$Z$变换是有其收敛域，所以在对一个信号进行$Z$变换时，一定要加上它的收敛域，因为对于一些不同的信号，它们的$Z$变换相同，但是它们的收敛域不同。仅仅由$Z$变换的表达式并不能完全的确定原信号，要加上它的收敛域才能完全的确定原信号。

例:求序列$x[n]=\alpha^n\mu[n]$的$Z$变换。
解:
$$
X(z)=\sum_{n=0}^{\infty}\alpha^nz^{-n}=\frac{1}{1-\alpha z^{-1}}
$$
要使上式收敛，则必须满足$\vert\alpha z^{-1}\vert<1$,即收敛域为$\vert z\vert>\vert \alpha\vert$。
所以序列$x[n]=\alpha^n\mu[n]$的$Z$变换为
$$
X(z)=\frac{1}{1-\alpha z^{-1}},\vert z\vert>\vert \alpha\vert
$$

例:求序列$x[n]=-\alpha^n\mu[-n-1]$的$Z$变换。
解:
$$
X(z)=\sum_{n=-\infty}^{-1}-\alpha^nz^{-n}=-\sum_{m=1}^{\infty}(\alpha^{-1}z)^{m}=-\frac{\alpha^{-1}z}{1-\alpha^{-1}z}=\frac{1}{1-\alpha z^{-1}}
$$
要使上式收敛，则需要满足$\vert\alpha^{-1}z\vert<1$,即收敛域为$\vert z\vert < \vert \alpha \vert$
所以序列$x[n]=-\alpha^n\mu[-n-1]$的$Z$变换为
$$
X(z)=\frac{1}{1-\alpha z^{-1}},\vert z\vert < \vert \alpha \vert
$$

由上面两例可知，序列$x[n]=\alpha^n\mu[n]$的$Z$变换的表达式与序列$x[n]=-\alpha^n\mu[-n-1]$的$Z$变换的表达式是一样的，但是它们的收敛域是完全不一样的，如果只给出其$Z$变换的表达式，是不能判断其原信号是什么的。


## $Z$变换的性质

设序列$x[n]$的$Z$变换为$X(z)$,其收敛域为$R_{x-}<\vert z\vert <R_{x+}$，序列 $w[n]$ 的 $Z$ 变换为 $W(z)$，其收敛域为 $R_{w-}<\vert z\vert <R_{w+}$。
### 线性性质

设$y[n]=\alpha x[n]+\beta w[n]$,则其$Z$变换为
$$
\begin{aligned}
Y(z)&=\sum_{n=-\infty}^{\infty}(\alpha x[n]+\beta w[n])z^{-n} \\
&=\alpha\sum_{n=-\infty}^{\infty}x[n]z^{-n}+\beta\sum_{n=-\infty}^{\infty}w[n]z^{-n} \\
&=\alpha X(z)+\beta W(z)
\end{aligned}
$$

其收敛域为 $max\{R_{x-},R_{w-}\} < \vert z \vert < min \{R_{x+},R_{w+}\}$。


### 时移性质

序列 $y[n]=x[n-n_0]$ 的 $Z$ 变换为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403231345.png" alt="image-20210403231345885" style="zoom:50%;" />

除了其收敛域可能包含$0$或者$\infty$,与原收敛域相同。

### 乘以指数序列

序列$y[n]=\alpha^nx[n]$的$Z$变换为

$$
\begin{aligned}
Y(z)&=\sum_{n=-\infty}^{\infty}\alpha^nx[n]z^{-n}\\
&=\sum_{n=-\infty}^{\infty}x[n](z\alpha^{-1})^{-n}\\
&=X(\frac{z}{\alpha})
\end{aligned}
$$
其收敛域为 $\vert \alpha \vert R_{x-}< \vert z\vert < \vert \alpha \vert R_{x+}$

### 反褶

序列$y[n]=x[-n]$的$Z$变换为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403231418.png" alt="image-20210403231418451" style="zoom:50%;" />

其收敛域为 $\dfrac{1}{R_{x+}}<\vert z\vert < \dfrac{1}{R_{x-}}$

### 共轭

序列$y[n]=x^{*}[n]$的$Z$变换为

$$
\begin{aligned}
Y(z)&=\sum_{n=-\infty}^{\infty}x^{*}[n]z^{-n} \\
&=(\sum_{n=-\infty}^{\infty}x[n](z^{*})^{-n})^{*} \\
&=X^{*}(z^{*})
\end{aligned}
$$
其收敛域未发生改变，因为$\vert z\vert = \vert z^{*}\vert$

### 时域微分

由于
$$
X(z)=\sum_{n=-\infty}^{\infty}x[n]z^{-n}
$$
所以
$$
\frac{dX(z)}{dz}=-\sum_{n=-\infty}^{\infty}nx[n]z^{-n-1}\Rightarrow-z\frac{dX(z)}{dz}=\sum_{n=-\infty}^{\infty}nx[n]z^{-n}
$$
所以序列$y[n]=nx[n]$的$Z$变换为
$$
Y(z)=-z\frac{dX(z)}{dz}
$$
其收敛域可能去掉$0$或者$\infty$，其余不变。

### 卷积

序列$y[n]=x[n]*w[n]$的$Z$变换为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403231507.png" alt="image-20210403231507908" style="zoom:50%;" />

其收敛域为
$$
max\{R_{x-},R_{w-}\}<\vert z\vert <min\{R_{x+},R_{w+}\}
$$
有时 $X(z)$ 与 $W(z)$ 的零极点可能会互相抵消，所以收敛域可能会比这个大。
