---
tags: [SVG]
---

## 视口

SVG 包含一个**无限大**的画布，但是视口(viewport)限制了我们只能看到一部分，就像是你在屋子里面通过窗子看外面的风景，外面的世界很大，但是窗子的大小是有限的，所以你只能看到一部分，这里的窗子就是视口的作用，它限制了你能看到的画布的大小。

我们通过 `width` 和 `height` 属性规定视口的大小，如果没有指定这两个属性，默认视口大小为 $300\text{px} \times 150\text{px}$

```html
<svg width="100" height="100">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>
```

<svg width="100" height="100">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>

我们规定了视口的大小为 $100 \times 100$，然后我们在画布上画了一个圆，该圆的圆心为 `(100, 100)`，半径为 `50`，经过简单计算就知道该圆只有左上角在视口中，其他部分在视口之外，我们只能看到左上角的圆。

## viewBox

需要注意的是，我们规定视口大小、圆心坐标和半径时并没有指定单位，对于视口来说，默认单位是 `px`，对于 `svg` 内的形状来说，如果没有指定 `viewBox`，默认也是 `px`。

所以现在有一个问题，`viewBox` 是什么？可以认为它规定了视口的**逻辑大小**，它需要指定四个值 `viewBox="min-x min-y width height"`

- `(min-x, min-y)`：视口显示的最小坐标
- `width, height`：视口的逻辑大小

这四个值通过空格或者逗号分隔开，`(min-x, min-y)` 我们先不理它，稍后我们看它怎么回事，暂且将它设置为 `(0, 0)`

```html
<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="50" cy="50" r="50" fill="#1a66ff" />
</svg>
<svg width="100" height="100" viewBox="0 0 200 200">
  <circle cx="50" cy="50" r="50" fill="#1a66ff" />
</svg>
```

上面我们定义了两个 SVG，视口大小都为 $100 \times 100$，但是两个 `viewBox` 不同，第一个 SVG 定义的视口逻辑大小也为 $100 \times 100$，也就是一个逻辑大小单位等价于 `1px`，而第二个视口逻辑大小为 $200 \times 200$，也就是说一个逻辑大小单位等价于 `0.5px`。

首先我们明确一点，当我们指定了 `viewBox` 之后，`svg` 中的内容用的单位就是逻辑单位而不是像素了。因为在两个 SVG 中，它们逻辑大小单位不同，所以即使定义相同参数的圆，但是在画布上显示的单位是不一样的，如下：

<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="50" cy="50" r="50" fill="#1a66ff" />
</svg>  

<svg width="100" height="100" viewBox="0 0 200 200">
  <circle cx="50" cy="50" r="50" fill="#1a66ff" />
</svg> 

有了 `viewBox`，我们绘制内容时就无需关注视口的大小了，因为视口的逻辑大小由 `viewBox` 确定，当 `viewBox` 不变时，改变视口大小，就可以产生对内容进行缩放，并且 SVG 中各内容的相对位置不会发生改变。

最后讲一下 `min-x, min-y`，它定义了视口能显示的最小坐标（当然坐标的单位是逻辑单位），也就是视口左上角的坐标是多少，之前我们定义的最小坐标是 `(0, 0)`，也就是说视口的左上角的坐标是 `(0, 0)`。

考虑开头的例子

```html
<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>
```

<svg width="100" height="100" viewBox="0 0 100 100" style="outline: 1px solid rgba(128, 128, 128, .5)">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>

因为这个圆只有左上角在视口中，所以只会显示左上四分之一圆，现在我们决定将左上角的坐标设置为 `(50, 50)`，就可以使得整个圆都在视口中了

```html
<svg width="100" height="100" viewBox="50 50 100 100">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>
```

<svg width="100" height="100" viewBox="50 50 100 100">
  <circle cx="100" cy="100" r="50" fill="#1a66ff" />
</svg>

当我们设置 `min-x, min-y` 为非零值时，就相当于对画布进行了平移，一般情况下我们都是设置为 `0 0`。

## preserveAspectRatio

上面我们通过 `viewBox` 指定的逻辑大小与真正的视口大小它们的宽高比是一样的，$100 \times 100$ 的视口大小与对应于 $100 \times 100$ 或 $200 \times 200$ 的逻辑大小，它们的宽高比是相同的.

但是如果它们的宽高比不同呢？例如视口大小是 $200 \times 300$，但是逻辑大小是 $100 \times 100$，那像素与逻辑单位的换算是如何计算的？是按最小的算（高，`100 -> 200`）还是按最大的算（宽，`100 -> 300`）？这个就由 `preserveAspectRatio` 属性规定。

```html
<svg width="300" height="200" viewBox="0 0 100 100" style="border: 1px solid black;">
    <image x="0" y="0" width="100" height="100" xlink:href="xxx.png" />
</svg>
```


<svg width="300" height="200" viewBox="0 0 100 100" style="border: 1px solid black;">
    <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
 

上面我们定义了视口大小为 $300 \times 200$，视口逻辑大小定义为 $100 \times 100$，很明显宽高比是不一样的，默认的换算关系为按最小比例算。

有三种换算方法：

- `meet`：按最小比例算，默认值
- `slice`：按最大比例算
- `none`：不等比缩放，进行拉伸填充视口

<div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 20px;">
<div style="display: flex; justify-content: center;">
<div>
  <svg width="240" height="180" viewBox="0 0 100 100" style="border: 1px solid black;">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>meet</code></center>
</div>
</div>
<div style="display: flex; justify-content: center;">
<div>
<svg width="240" height="180" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid slice" style="border: 1px solid black;">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>slice</code></center>
</div>  
</div>
<div style="display: flex; justify-content: center;">
<div>
<svg width="240" height="180" viewBox="0 0 100 100" preserveAspectRatio="none" style="border: 1px solid black;">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>none</code></center>  
</div>  
</div>
</div>

除此之外还需要指定对齐关系，在每个方向上都有三种对齐方式：

- `min`：左对齐或上对齐
- `mid`：居中对齐
- `max`：右对齐或下对齐

所以组合起来就有 $3 \times 3 = 9$ 种对齐方式（当指定为 `none` 时不用指定对齐方式，因为已经填充完了视口，不需要对齐），默认的对齐方式为 `xMidYMid`。

`preserveAspectRatio` 的写法为 `preserveAspectRatio="对齐方式 缩放方式"`，它的默认值就是 `preserveAspectRatio="xMidYMid meet"`

```html
<svg width="300" height="200" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid meet">
  
</svg>
```

我们看几组例子理解一下

<div style="display: grid; grid-template-columns: repeat(2, 1fr); gap: 20px;">
<div style="display: flex; justify-content: center;">
<div>
  <svg width="240" height="180" viewBox="0 0 100 100" style="border: 1px solid rgba(128, 128, 128, .5);" preserveAspectRatio="xMinYMid meet">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>xMinYMid meet</code></center>
</div>
</div>
  <div style="display: flex; justify-content: center;">
<div>
  <svg width="240" height="180" viewBox="0 0 100 100" style="border: 1px solid rgba(128, 128, 128, .5);" preserveAspectRatio="xMaxYMid meet">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>xMaxYMid meet</code></center>
</div>
</div>
<div style="display: flex; justify-content: center;">
<div>
<svg width="240" height="180" viewBox="0 0 100 100" preserveAspectRatio="xMidYMin slice" style="border: 1px solid rgba(128, 128, 128, .5);">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>xMidYMmin slice</code></center>
</div>  
</div>
<div style="display: flex; justify-content: center;">
<div>
<svg width="240" height="180" viewBox="0 0 100 100" preserveAspectRatio="xMidYMax slice" style="border: 1px solid rgba(128, 128, 128, .5);">
  <image x="0" y="0" width="100" height="100" xlink:href="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/image.1shw90eniffk.png" />
</svg>
<center><code>xMidYMax slice</code></center>
</div>  
</div>
</div>
