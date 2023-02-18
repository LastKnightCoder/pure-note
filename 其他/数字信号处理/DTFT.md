---
title: DTFT
author: 熊滔
time: 2021-04-03
category: 数字信号处理
commentid: signal-process:dtft
---

## 离散时间傅里叶变换(DTFT)

我们定义离散时间傅里叶变换为
$$
X(e^{jw})=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}
$$

其实我在书上看到这里的时候不太理解为什么离散傅里叶变换要这么定义，其实书上直接给出这么一个公式有一点马后炮的感觉，我想知道这个公式为什么这么定义，想知道的是一个设计的过程，这么定义为什么能够给出频谱密度，所以接下来谈谈我的理解。

说到频谱密度的话，我们其实对连续傅里叶变换比较了解，并且知道为什么连续傅里叶变换为什么能反映连续信号的频谱密度，所以我打算从连续时间信号进行入手。

考虑离散时间信号$x[n]$是对连续时间信号$x_a(t)$的抽样,抽样的周期为$T_s$,得到抽样信号$\hat{x}_a(t)$,假设连续时间信号的傅里叶变换为$X(j\Omega)$(在接下来的表示中，连续时间信号的频域符号用$\Omega$表示，离散时间信号频域符号用$w$表示)，那么抽样信号$\hat{x}_a(t)$的傅里叶变换为

$$
\hat{x}_a(t)=x_a(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)=\sum_{n=-\infty}^{\infty}x_a(nT_s)\delta(t-nT_s)
$$

由于$\delta(t-nT_s)$的傅里叶变换为$e^{-j\Omega nT_s}$,所以

$$
\hat{X}(j\Omega)=\sum_{n=-\infty}^{\infty}x_a(nT_s)e^{-j\Omega nT_s}
$$

仔细观察这个表达式，虽然从这个表达式中看不出$\hat{X}_a(j\Omega)$与$X(j\Omega)$的关系，但是敏锐的人已经发现了这个表达式与我们所定义的离散时间傅里叶变换之间的联系，如果用$x[n]$替换$x[nT_s]$(这样的替换显然是合理的)，并且令$w=\Omega T_s$,我们就可以得到离散时间傅里叶变换的表达式

$$
X(e^{jw})=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}
$$

我们似乎解决了$DTFT$的由来，但是没有解决为什么$DTFT$能够表示信号的频谱，为了解决这个问题，我们还是要研究一下$\hat{X}(j\Omega)$,由于

$$
\hat{x}_a(t)=x_a(t)\sum_{n=-\infty}^{\infty}\delta(t-nT_s)
$$

而

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403222745.png" alt="image-20210403222744954" style="zoom:50%;" />

这个傅里叶变换不熟悉的去翻阅资料，因为在这里推导的话可能会破坏思路的连续性，所以就不进行推导了。所以得到$\hat{X}(j\Omega)$的另一表达形式

$$
\begin{aligned}
\hat{X}(j\Omega)&=\frac{1}{2\pi}X(j\Omega)*\frac{2\pi}{T_s}\sum_{n=-\infty}^{\infty}\delta(\Omega-n\Omega_s) \\
&=\frac{1}{T_s}\sum_{n=-\infty}^{\infty}X(j(\Omega -n\Omega_s))
\end{aligned}
$$
看到这里就明朗了，从表达式上看，$\hat{X}(j\Omega)$与$X(j\Omega)$的关系为$\hat{X}(j\Omega)$**是**$X(j\Omega)$以$\Omega_s$为周期进行周期延拓。如果$\Omega_s$足够大(如果知道抽样定理，就知道$\Omega_s \geq 2\Omega_m$即可，$\Omega_m$是$x_a(t)$的最高频率)使得$\hat{X}(j\Omega)$没有发生混叠的话，那么$X(j\Omega)$只是$\hat{X}(j\Omega)$的一个周期。

根据
$$
X(e^{jw})=\hat{X}(j\Omega)\vert_{w=\Omega T_s}
$$

所以就可以知道为什么$X(e^{jw})$为什么可以表示信号的频谱。

因为$\hat{X}(j\Omega)$是一个周期信号，根据
$$
X(e^{jw})=\hat{X}(j\Omega)\vert_{w=\Omega T_s}
$$

所以$X(je^{jw})$也是一个周期信号，其周期为$2\pi$,如下证明
$$
X(e^{j(w+2\pi)})=\sum_{n=-\infty}^{\infty}x[n]e^{-j(w+2\pi)n}=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}=X(e^{jw})
$$

在$[-\pi,\pi]$上，$X(e^{jw})$就包含了原模拟频谱的所有信息，所以离散时间傅里叶反变换的公式定义为
$$
x[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{jw})e^{jwn}dw
$$


如果对连续时间信号的抽样及其重建感兴趣的话，可以参考[连续时间信号的抽样及其重建](https://blog.csdn.net/The_last_knight/article/details/84502718)


### 对称性质

在之前我们有定义共轭对称序列$x_{cs}[n]=\dfrac{1}{2}(x[n]+x^{*}[-n])$以及共轭反对称序列$x_{ca}[n]=\frac{1}{2}(x[n]-x^{*}[-n])$，同理，我们定义$X_{cs}(e^{jw})=\dfrac{1}{2}(X(e^{jw})+X^{*}(e^{-jw}))$为$X(e^{jw})$的共轭对称部分，$X_{ca}(e^{jw})=\dfrac{1}{2}(X(e^{jw})-X^{*}(e^{-jw}))$为$X(e^{jw})$的共轭反对称部分。

假设复序列$x[n]$的$DTFT$为$X(e^{jw})$，那么$x^{*}[n]$的$DTFT$为
$$
\sum_{n=-\infty}^{\infty}x^{*}[n]e^{-jwn}=(\sum_{n=-\infty}^{\infty}x[n]e^{-(-jwn)})^{*}=X^{*}(e^{-jw})
$$

$x[-n]$的$DTFT$为
$$
\sum_{n=-\infty}^{\infty}x[-n]e^{-jwn}=\sum_{m=-\infty}^{\infty}x[m]e^{-(-jwm)}=X(e^{-jw})
$$

所以综合以上二者得到$x^{*}[-n]$的$DTFT$为$X^{*}(e^{jw})$

所以该序列实部的$DTFT$为
$$
DTFT[x_{re}[n]]=DTFT[\frac{1}{2}(x[n]+x^{*}[n])]=\frac{1}{2}(X(e^{jw})+X^{*}(e^{-jw}))=X_{cs}(e^{jw})
$$

虚部的$DTFT$为
$$
DTFT[jx_{im}[n]]=DTFT[\frac{1}{2}(x[n]-x^{*}[n])]=\frac{1}{2}(X(e^{jw})-X^{*}(e^{-jw}))=X_{ca}(e^{jw})
$$

共轭对称部分的$DTFT$为
$$
DTFT[x_{cs}[n]]=DTFT[\frac{1}{2}(x[n]+x^{*}[-n])]=\frac{1}{2}(X(e^{jw})+X^{*}(e^{jw}))=X_{re}(e^{jw})
$$

共轭反对称部分的$DTFT$为
$$
DTFT[x_{ca}[n]]=DTFT[\frac{1}{2}(x[n]-x^{*}[-n])]=\frac{1}{2}(X(e^{jw})-X^{*}(e^{jw}))=jX_{im}(e^{jw})
$$

简单的把上面的公式总结一下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223031.png" alt="image-20210403223031482" style="zoom:50%;" />


这就是$DTFT$的一些对称性质。

### 收敛条件

从$DTFT$的表达式看，这是一个无穷级数的求和，所以是有收敛条件的。如果如果信号满足

$$
\sum_{n=-\infty}^{\infty}\vert x[n]\vert < \infty
$$

那么称序列$x[n]$绝对可和，并且由于
$$
\vert X(e^{jw})\vert=\vert \sum_{n=-\infty}^{\infty}x[n]e^{-jwn}\vert \leq \sum_{n=-\infty}^{\infty}\vert x[n] \vert\vert e^{-jwn}\vert<\infty
$$

即如果$x[n]$是绝对可和的话，那么$X(e^{jw})$一定存在，所以$x[n]$绝对可和是离散时间傅里叶$X(e^{jw})$存在的充分条件。这种收敛称为一致收敛。

考虑另一种收敛为均方收敛，有的信号不是绝对可和信号，但是
$$
\sum_{n=-\infty}^{\infty}\vert x[n]\vert^2 < \infty
$$

该种收敛不是一致收敛，所以会产生Gibbs现象。另一信号是既不是绝对可和信号，也不是平方可和信号(比如常数,单位阶跃信号$\mu[n]$)，为了定义其傅里叶变换，引入了狄拉克函数$\delta(t)$,关于狄拉克函数在信号与系统中有详细介绍，这里不多讲。


### 常见DTFT变换对

1. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223214.png" alt="image-20210403223214192" style="zoom:50%;" />


证明：
$$
\sum_{n=-\infty}^{\infty}\delta[n]e^{-jwn}=1
$$

2. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223235.png" alt="image-20210403223235349" style="zoom:50%;" />

证明：由于常数 1 既不是绝对可和序列，也不是平方可和序列，所以其傅里叶变换为带有狄拉克函数，证其傅里叶变换比较困难，我绝对从其反变换入手:
$$
\frac{1}{2\pi}\int_{-\pi}^{\pi}\sum_{k=-\infty}^{\infty}2\pi \delta(w+2\pi k)dw=1
$$

3. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223407.png" alt="image-20210403223407028" style="zoom:50%;" />

证明:$\mu[n]$既不是绝对可和序列，也不是平方可和序列，还是得从另外的方法去证,将$\mu[n]$分解为偶部和奇部，则其偶部为
$$
y_{ev}[n]=\frac{1}{2}(\mu[n]+\mu[-n])=\frac{1}{2}+\frac{1}{2}\delta[n]
$$

其傅里叶变换为
$$
Y_{ev}(e^{jw})=\sum_{k=-\infty}^{\infty}\pi \delta(w+2\pi k)+\frac{1}{2}
$$

其奇部为
$$
y_{od}[n]=\frac{1}{2}(\mu[n]-\mu[-n])=\frac{1}{2}(2\mu[n]-(\mu[n]+\mu[-n]))=\mu[n]-\frac{1}{2}-\frac{1}{2}\delta[n]
$$

所以

$$
\begin{aligned}
&\quad y_{od}[n]-y_{od}[n-1]=\frac{1}{2}(\delta[n]+\delta[n-1]) \\
&\Rightarrow (1-e^{-jw})Y_{od}(e^{jw})=\frac{1}{2}(1+e^{-jw}) \\
&\Rightarrow Y_{od}(e^{jw})=\frac{1}{2}\frac{1+e^{jw}}{1-e^{jw}}=-\frac{1}{2}+\frac{1}{1-e^{jw}}
\end{aligned}
$$

所以

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223721.png" alt="image-20210403223721092" style="zoom:50%;" />

4. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223905.png" alt="image-20210403223905693" style="zoom:50%;" />

证明:

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403223939.png" alt="image-20210403223939372" style="zoom:50%;" />

5. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224013.png" alt="image-20210403224012979" style="zoom:50%;" />

证明：该序列是绝对可和序列，所以可用$DTFT$的定义直接求和
$$
\sum_{n=-\infty}^{\infty}\alpha^{n}\mu[n]e^{-jwn}=\sum_{n=0}^{\infty}(\alpha e^{-jw})^n=\frac{1}{1-\alpha e^{-jw}}
$$

## DTFT变换的性质

### 线性性质

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224141.png" alt="image-20210403224141924" style="zoom:50%;" />

则

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224159.png" alt="image-20210403224159476" style="zoom:50%;" />

### 时移性质

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224232.png" alt="image-20210403224232427" style="zoom:50%;" />

则$x[n-n_0]$的傅里叶变换为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224251.png" alt="image-20210403224251537" style="zoom:50%;" />

### 频移性质

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224319.png" alt="image-20210403224319320" style="zoom:50%;" />

则 $e^{jw_0n}x[n]$ 的傅里叶变换为
$$
\sum_{n=-\infty}^{\infty}e^{jw_0n}x[n]e^{-jwn}=\sum_{n=-\infty}^{\infty}x[n]e^{-j(w-w_0)n}=X(e^{j(w-w_0)})
$$

### 时域反转

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224356.png" alt="image-20210403224356809" style="zoom:50%;" />

则 $x[-n]$ 的傅里叶变换为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224421.png" alt="image-20210403224421329" style="zoom:50%;" />


### 时域微分

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224622.png" alt="image-20210403224622432" style="zoom:50%;" />

由于
$$
x[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{jw})e^{jwn}dw
$$
两边同时对$n$进行微分运算
$$
\frac{dx[n]}{dn}=\frac{1}{2\pi}\int_{-\pi}^{\pi}jwX(e^{jw})e^{jwn}dw
$$
所以

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224650.png" alt="image-20210403224650210" style="zoom:50%;" />

### 频域微分

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224717.png" alt="image-20210403224717176" style="zoom:50%;" />

由
$$
X(e^{jw})=\sum_{n=-\infty}^{\infty}x[n]e^{-jwn}
$$
两边同时对 $w$ 进行微分
$$
\frac{dX(e^{jw})}{dw}=\sum_{n=-\infty}^{\infty}-jnx[n]e^{-jwn}
$$

$$
\Rightarrow \sum_{n=-\infty}^{\infty}nx[n]e^{-jwn}= j\frac{dX(e^{jw})}{dw}
$$

所以

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403224857.png" alt="image-20210403224857064" style="zoom:50%;" />

### 卷积性质

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403225414.png" alt="image-20210403225414118" style="zoom:50%;" />

则二者卷积的$DTFT$为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403225533.png" alt="image-20210403225533838" style="zoom:50%;" />

### 调制定理

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403225603.png" alt="image-20210403225603263" style="zoom:50%;" />

则 $x[n]y[n]$ 的 $DTFT$ 为

$$
\begin{aligned}
\sum_{n=-\infty}^{\infty}(x[n]y[n])e^{-jwn} &=\sum_{n=-\infty}^{\infty}x[n]\frac{1}{2\pi}\int_{-\pi}^{\pi}Y(e^{j\theta})e^{j\theta n}d\theta e^{-jwn} \\
&=\frac{1}{2\pi}\int_{-\pi}^{\pi}\sum_{n=-\infty}^{\infty}x[n]^{-j(w-\theta)n}Y(e^{j\theta})d\theta \\
&=\frac{1}{2\pi}\int_{-\pi}^{\pi}Y(e^{j\theta})X(e^{j(w-\theta)})d\theta
\end{aligned}
$$
### Parseval定理

设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210403225631.png" alt="image-20210403225631592" style="zoom:50%;" />

则

$$
\begin{aligned}
\sum_{n=-\infty}^{\infty}x[n]y^{*}[n]&=\sum_{n=-\infty}^{\infty}x[n](\frac{1}{2\pi}\int_{-\pi}^{\pi}Y(e^{jw})e^{jwn}dw)^{*} \\
&=\frac{1}{2\pi}\int_{-\pi}^{\pi}x[n]e^{-jwn}Y^{*}(e^{jw})dw \\
&=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{jw})Y^{*}(e^{jw})dw
\end{aligned}
$$
得到Parseval定理

$$
\sum_{n=-\infty}^{\infty}x[n]y^{*}[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{jw})Y^{*}(e^{jw})dw
$$
如果$y[n]=x[n]$,那么
$$
\sum_{n=-\infty}^{\infty}\vert x[n] \vert^2=\frac{1}{2\pi}\int_{-\pi}^{\pi}\vert X(e^{jw})\vert^2dw
$$

即序列$x[n]$的能量，可以通过对$\vert X(e^{jw})\vert^2$的积分求得，所以称$\vert X(e^{jw})\vert^2$为序列$x[n]$的能量谱密度。