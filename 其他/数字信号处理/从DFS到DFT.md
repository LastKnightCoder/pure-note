---
title: 从DFS到DFT
author: 熊滔
time: 2021-04-03
category: 数字信号处理
commentid: signal-process:dfs2dft
---

## 周期序列的级数展开

正如连续时间周期信号可以表示为一系列正弦信号的和的形式，周期序列也可以表示为一系列正弦之和的形式，假设序列$\tilde{x}[n]$的周期为$N$,那么它的基频为$\cfrac{2\pi}{N}$,所以有

$$
\tilde{x}[n]=\frac{1}{N}\sum_{k=0}^{N-1}\tilde{X}[k]e^{j\frac{2\pi}{N}kn}
$$

这里与连续时间信号不同的是，不需要无穷多个成谐波关系的复指数,只需要$N$个成谐波关系的复指数。为求傅里叶级数系数$\tilde{X}[k]$,两边同时乘以$e^{-j\frac{2\pi}{N}rn}$,并在$0 \backsim (N-1)$求和

$$
\sum_{n=0}^{N-1}\tilde{x}[n]e^{-j\frac{2\pi}{N}rn}=\sum_{k=0}^{N-1}\tilde{X}[k]\frac{1}{N}\sum_{n=0}^{N-1}e^{j\frac{2\pi}{N}(k-r)n}
$$
由于

$$
\frac{1}{N}\sum_{n=0}^{N-1}e^{j\frac{2\pi}{N}(k-r)n}=
\begin{cases}
1, \quad k - r = mN \\
0, \quad other
\end{cases}
$$

代入上式，得到

$$
\tilde{X}[r]=\sum_{n=0}^{N-1}\tilde{x}[n]e^{-j\frac{2\pi}{N}rn}
$$
从上看出，$\tilde{X}[r]$也是周期序列，周期为$N$。总结一下周期序列的展开及其系数的表示

$$
\color{red} {\tilde{x}[n]=\frac{1}{N}\sum_{k=0}^{N-1}\tilde{X}[k]e^{j\frac{2\pi}{N}kn}}
$$
$$
\color{red} {\tilde{X}[k]=\sum_{n=0}^{N-1}\tilde{x}[n]e^{-j\frac{2\pi}{N}kn} }
$$

## 周期信号的傅里叶变换

将周期信号的傅里叶级数纳入傅里叶变换的框架常常是有益的。但是周期序列不是绝对可和的，也不是平方可和的，所以周期序列的傅里叶变换应该是冲激串的形式。为导出周期序列的傅里叶变换的表达式，不妨先看一下连续周期信号的傅里叶变换。连续周期信号可以将其进行傅里叶级数展开

$$
\tilde{x}(t)=\sum_{n=-\infty}^{\infty}F_ne^{jn\Omega_0 t}
$$
两边同时进行傅里叶变换
$$
\tilde{X}(j\Omega)=\sum_{n=-\infty}^{\infty}2\pi F_n\delta(\Omega -n\Omega_0)
$$
同理，周期序列的傅里叶变换可以写为
$$
\tilde{X}(e^{jw})=\sum_{k=0}^{N-1}\frac{2\pi}{N}\tilde{X}[k]\delta(w-\frac{2\pi k}{N})
$$
为验证其正确性，对上式进行傅里叶反变换
$$
\begin{aligned}
\frac{1}{2\pi}\int_{0-\epsilon}^{2\pi-\epsilon}\tilde{X}(e^{jw})e^{jwn}dw&=\frac{1}{N}\sum_{k=0}^{N-1}\tilde{X}[k]\int_{0-\epsilon}^{2\pi-\epsilon}\delta(w-\frac{2\pi k}{N})e^{jwn}dw\\
&=\frac{1}{N}\sum_{k=0}^{N-1}\tilde{X}[k]e^{j\frac{2\pi k}{N}n} \\
&=\tilde{x}[n]
\end{aligned}
$$
例:周期脉冲串的傅里叶变换
$$
\tilde{p}[n]=\sum_{r=-\infty}^{\infty}\delta[n-rN]
$$
$$
\tilde{P}[k]=\sum_{n=0}^{N-1}\tilde{p}[n]e^{-j\frac{2\pi kn}{N}}=1
$$
那么其傅里叶变换为
$$
\tilde{P}(e^{jw})=\sum_{k=0}^{N-1}\frac{2\pi}{N}\tilde{P}[k]\delta(w-\frac{2\pi k}{N})=\sum_{k=0}^{N-1}\frac{2\pi}{N}\delta(w-\frac{2\pi k}{N})
$$

考虑傅里叶级数系数与其一个周期傅里叶变换的关系,为什么要考虑这个问题，因为在连续时间周期信号其傅里叶级数与其主周期傅里叶变换的关系为:
$$
F_n=\frac{1}{T}X_T(j\Omega)\vert_{\Omega = n\Omega_0}
$$
其傅里叶级数系数是其主周期傅里叶变换在$\Omega =n \Omega_0$时的抽样值除以周期。同理，周期序列与其主周期的傅里叶变换应该也有类似的关系，下面具体探究一下。假设周期序列$\tilde{x}[n]$在主区间$0\backsim N-1$(后若未加说明，主区间均指$0 \backsim N-1$)上为$x[n]$,那么二者之间的关系为

$$
\tilde{x}[n]=\sum_{r=-\infty}^{\infty}x[n-rN]=x[n]*\tilde{p}[n]
$$
$\tilde{p}[n]$即为上例中的周期冲激序列。对上式两边同时进行傅里叶变换

$$
\begin{aligned}
\tilde{X}(e^{jw})&=X(e^{jw})\tilde{P}(e^{jw}) \\
&=X(e^{jw})\sum_{k=0}^{N-1}\frac{2\pi}{N}\delta(w-\frac{2\pi k}{N}) \\
&=\sum_{k=0}^{N-1}\frac{2\pi}{N}X(e^{j\frac{2\pi k}{N}})\delta(w-\frac{2\pi k}{N})
\end{aligned}
$$
与式
$$
\tilde{X}(e^{jw})=\sum_{k=0}^{N-1}\frac{2\pi}{N}\tilde{X}[k]\delta(w-\frac{2\pi k}{N})
$$
进行比较发现
$$
\tilde{X}[k]=X(e^{j\frac{2\pi k}{N}})=X(e^{jw})\vert_{w=\frac{2\pi k}{N}}
$$
所以傅里叶级数系数是其主区间傅里叶变换在 $w=\dfrac{2\pi k}{N}$ 处的抽样值。

## 离散傅里叶变换
我们知道，序列进行傅里叶变换得到的频谱都是连续的，而对信号进行数字处理一般都是计算机或者专门的处理器，但无论是计算机还是专门的处理器，其存储精度及存储容量都是有限的，无法处理连续的信号。

我们对连续信号进行处理时，是将其进行抽样转换为数字信号，为了无损的恢复出原信号，其抽样速率必须满足奈奎斯特抽样定理。那么可不可以对序列的频谱进行抽样处理呢？为了无损的恢复出原信号，抽样又该满足什么样的条件？假设对于一有限长的信号$x[n]$,其长度为$M$,其傅里叶变换为$X(e^{jw})$,现对其频谱的主周期进行均匀的$N$点抽样，得到序列$X[k]$,即

$$
X[k]=X(e^{jw})\vert_{w=\frac{2\pi k}{N}}, \quad k = 0, ... , N-1
$$
这与我们之前对傅里叶级数的讨论十分的相似，如果把$X[k]$看作是傅里叶级数系数，那么由$X[k]$恢复出的信号为

$$
\begin{aligned}
\tilde{x}[n]&=\frac{1}{N}\sum_{k=0}^{N-1}X[k]e^{j\frac{2\pi kn}{N}} \\
&=\frac{1}{N}\sum_{k=0}^{N-1}\sum_{m=-\infty}^{\infty}x[m]e^{-j\frac{2\pi k}{N}m}e^{j\frac{2\pi kn}{N}} \\
&=\sum_{m=-\infty}^{\infty}x[m](\frac{1}{N}\sum_{k=0}^{N-1}e^{j\frac{2\pi k(n-m)}{N}}) \\
&=\sum_{m=-\infty}^{\infty}x[m]\tilde{p}[n-m] \\
&=x[n]*\tilde{p}[n] \\
&=x[n] * \sum_{r=-\infty}^{\infty}\delta[n-rN] \\
&=\sum_{r=-\infty}^{\infty}x[n-rN]
\end{aligned}
$$
恢复出的信号$\tilde{x}[n]$是原信号以周期$N$进行周期延拓得到的结果,如果$N\geq M$,那么周期延拓得到的信号没有发生混叠，那么可以取其$0\backsim N-1$一个周期中前$M$个恢复原来的信号。所以我们可以通过对其频谱进行抽样来处理，并如果满足抽样点数$N$大于等于序列的长度$M$,那么就可以无损的恢复出原来的信号。这个原则叫做频域抽样定理。


稍微总结一下上面的过程，我们首先对序列$x[n]$进行傅里叶变换，然后对其频谱进行抽样得到$X[k]$，然后以$X[k]$作为傅里叶级数的系数得到序列$\tilde{x}[n]$,如果满足频域抽样定理，那么取其主区间的前$M$个即可恢复出原信号。

我们不妨省略对序列进行傅里叶变换，然后对其进行抽样的过程，直接定义一个变换由$x[n]$直接得到$X[k]$,然后省略有$X[k]$得到$\tilde{x}[n]$,然后取其主周期得到$x[n]$的过程，也直接定理一个变换由$X[k]$直接得到$x[n]$。假设有限长序列$x[n]$的长度为$N$,并假设其有值区间为$0 \leq n \leq N-1$,为了满足频域抽样定理，我们不妨设采样的点数为$N$，这样做的好处是由$X[k]$得到$x[n]$时，直接取其主区间即可，不用取其前多少个。为了得到$X[k]$这个表达式，首先得知道$X[k]$是怎么来的，通过上面的介绍我们知道

$$
X[k]=X(e^{jw})\vert_{w=\frac{2\pi k}{N}}, \quad k = 0, ... , N-1
$$
即
$$
X[k]=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}\vert_{w=\frac{2\pi k}{N}}=\sum_{n=0}^{N-1}x[n]e^{-j\frac{2\pi}{N}kn}, \quad 0\leq k \leq N-1
$$
为了得到$x[n]$,以$X[k]$为傅里叶级数系数得到$\tilde{x}[n]$,并取其主区间，所以
$$
x[n]=\frac{1}{N}\sum_{k=0}^{N-1}X[k]e^{j\frac{2\pi kn}{N}}, ]\quad 0 \leq n \leq N-1 
$$

所以我们得到一组变换
$$
\color{red}{X[k]=\sum_{n=0}^{N-1}x[n]e^{-j\frac{2\pi}{N}kn}, \quad 0\leq k \leq N-1}
$$
$$
\color{red}{x[n]=\frac{1}{N}\sum_{k=0}^{N-1}X[k]e^{j\frac{2\pi kn}{N}}, ]\quad 0 \leq n \leq N-1}
$$

我们把上面的变换称为离散傅里叶变换，简称$DFT$。

## DFT与DFS的关系

上面的推导我虽然是从对有限长序列频谱抽样推导得到的DFT，但是其中运用了DFS的理论，所以DFT与DFS之间关系是什么呢？首先引用一下之前得到的结论：

```note
傅里叶级数系数是其主区间傅里叶变换在 $w=\dfrac{2\pi k}{N}$ 处的抽样值。
```

如果把有限长序列$x[n]$以其长度$N$进行周期延拓的话，由上面的结论，DFS得到的$\tilde{X}[k]$是$x[n]$傅里叶变换在 $w=\dfrac{2\pi k}{N}$ 处的抽样值，而 DFT 得到的 $X[k]$ 是在傅里叶变换的主区间抽样得到的，所以 $X[k]$ 是 $\tilde{X}[k]$ 的主区间。明显的，$x[n]$是由 $\tilde{X}[k]$ 得到的 $\tilde{x}[n]$ 的主区间。所以计算DFT的另一方法是，对于一有限长序列 $x[n]$，以其长度 $N$ 进行周期延拓得到 $\tilde{x}[n]$，对 $\tilde{x}[n]$ 进行 DFS 得到 $\tilde{X}[k]$，那么对 $\tilde{X}[k]$ 取其主区间即可得到 $X[k]$ ，同样的，对 $\tilde{X}[k]$ 得到的 $\tilde{x}[n]$，取其主区间即可得到 $x[n]$。