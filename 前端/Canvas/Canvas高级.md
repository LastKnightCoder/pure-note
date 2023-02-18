---
title: Canvas高级
author: 熊滔
commentid: canvas:advanced
---

## 阴影

***Canvas*** 中的阴影通过下面四个属性进行设置

- `shadowColor` ：阴影颜色
- `shadowOffsetX`：阴影横向偏移
- `shadowOffsetY`：阴影纵向偏移
- `shadowBlur`：阴影模糊

为图形添加阴影

```js
ctx.shadowColor = 'rgba(0, 0, 0, .5)'
ctx.shadowOffsetX = 10
ctx.shadowOffsetY = 10
ctx.shadowBlur = 5

ctx.fillStyle = '#0eb0c9'
ctx.fillRect(100, 100, 200, 200)
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/66.html" height="420" width="100%"></iframe>

为文字添加阴影

```js
ctx.shadowColor = 'rgba(0, 0, 0, .5)'
ctx.shadowOffsetX = 5
ctx.shadowOffsetY = 5
ctx.shadowBlur = 5

const text = 'Hello World'
ctx.fillStyle = '#0eb0c9'
ctx.textBaseline = 'middle'
ctx.font = 'bold 50px MerriWeather'
ctx.fillText(text, 100, 100)
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/67.html" height="200" width="120%" style="overflow:hidden;"></iframe>

## clip

当我们调用 `stroke()` 和 `fill()` 方法时，会根据当前的路径信息进行绘制图形，而当我们调用 `clip()` 方法时，则会根据路径信息形成一个裁剪区域，接下来绘制的图形只能出现在这个裁剪区域中

```js
ctx.beginPath()
ctx.arc(250, 250, 100, 0, 2 * Math.PI)
ctx.clip()

const loadImage = (src) => {
  const img = new Image()
  img.addEventListener('load', () => {
    const width = img.width
    const height = img.height
    const ratio = width / height
    ctx.drawImage(img, 0, 0, canvas.width, canvas.width / ratio)
  })
  img.src = src
}

loadImage('./car.jpeg')
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/68.html" height="430" width="800px" style="margin: auto;"></iframe>

## globalAlpha

 `globalAlpha` 用来设置全局的透明度

```js
ctx.globalAlpha = 0.3

ctx.fillStyle = '#12a182'
ctx.fillRect(50, 50, 100, 100)

ctx.fillStyle = 'black'
ctx.fillRect(100, 100, 100, 100)
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/69.html" height="250" width="100%"></iframe>

## globalCompositeOperation

`globalCompositeOperation` 规定了当两个形状重合时，最终呈现出来的图像是什么样子。它的属性十分的多，默认值为 `source-over` ，表示后面绘制的图像会压住前面的图形，除此之外还有 `destination-over` 表先绘制的图案在上面。

```js
const draw = composite => {
  ctx.clearRect(0, 0, canvas.width, canvas.height)
  ctx.globalCompositeOperation = composite
  ctx.fillStyle = '#12a182'
  ctx.fillRect(100, 100, 200, 200)
  ctx.fillStyle = '#fed71a'
  ctx.beginPath()
  ctx.arc(300, 200, 100, 0, Math.PI * 2)
  ctx.fill()
}

draw('desination-over')
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/70.html" height="400" width="100%"></iframe>

```note
我们把后绘制的图案称为 `source` ，先绘制的图案称为 `destination` 
```

通过设置 `globalCompositeOperation` 可以组合出一些奇特的形状，其取值多达 26 种，可以自己写代码体验一番。

## isPointInPath

`isPointInPath(x, y)` 可以判断某个点 `(x, y)` 是否在当前路径形成的区域中，这个 `(x, y)` 是指在 `canvas` 中的坐标。下面会随机绘制 10 个小球，当点击画布时，会检测是否点击的点在小球的路径中，如果在则会改变小球的填充颜色

```js
const canvas = document.getElementById('canvas')
const ctx = canvas.getContext('2d')

class Ball {
  constructor(x, y, r, color = '#29b7cb') {
    this.x = x
    this.y = y
    this.r = r
    this.color = color
  }

  draw(ctx) {
    ctx.save()
    ctx.fillStyle = this.color
    ctx.beginPath()
    ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2)
    ctx.fill()
    ctx.restore()
  }
}

const balls = []
for (let i = 0; i < 10; i++) {
  balls.push(new Ball(Math.random() * 500, Math.random() * 500, Math.random() * 40 + 10))
  balls[i].draw(ctx)
}

canvas.addEventListener('click', event => {
  const x = event.x - canvas.getBoundingClientRect().left
  const y = event.y - canvas.getBoundingClientRect().top

  console.log(`x: ${x}, y: ${y}`);

  for (let i = 0; i < balls.length; i++) {
    const ball = balls[i]
    ctx.save()
    ctx.beginPath()
    ctx.arc(ball.x, ball.y, ball.r, 0, Math.PI * 2)
    if (ctx.isPointInPath(x, y)) {
      ctx.fillStyle = '#45b787'
      ctx.fill()
    }
    ctx.restore()
  }
})
```

<iframe class="extra-width-center" src="https://lastknightcoder.github.io/canvas-demos/72.html" height="530" style="--width: 1000px;"></iframe>

## 非零环绕原则

当我们绘制的路径交叉在一起，形成了多个区域，这个时候应该填充哪些区域呢

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/Canvas-非零环绕.21db9l9zs8rk.svg"  />

在一个路径中绘制了两个圆，其中一个圆是顺时针绘制的，一个圆是逆时针绘制的，形成了三个区域，那么当我们调用 `fill()` 方法时，哪些区域应该填充呢？

判断哪些区域应该填充需要使用非零环绕原则，从区域中引一条射线，该射线会与路径相交，如果该射线与路径的方向相同(夹角小于 90 度)，则进行加 1，如果相反则进行减 1，最后如果结果不为 0，则对该区域进行填充，否则不填充

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/Canvas-Page-6.1mebk0fsp934.svg"  />

从上图看，区域 `1` 引出一条射线，与一条路径相交，该路径的方向与射线的方向相同，因此加 $1$，最终结果不为 $0$，因此区域 `1` 应该被填充。从区域 `2` 引出一条射线，与路径有两个交点，可以看出射线与相交时的路径一个方向相同一个方向相反，因此最终的结果为 $0$，所以区域 `2` 不会被填充；从区域 `3` 引出一条射线，与路径有三个交点，可以看出射线与这三条路径的方向都相反，因此结果为 $-3$，不为零，因此区域 3 会被填充

```js
ctx.beginPath()
ctx.arc(100, 100, 50, 0, Math.PI * 2);
ctx.arc(150, 100, 50, 0, Math.PI * 2, true);
ctx.fillStyle = '#894e54'
ctx.fill()
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/73.html" height="200" width="100%"></iframe>

利用非零环绕特性，我们可以实现剪纸效果

```js
const drawRectClockwise = (x, y, w, h) => {
  ctx.moveTo(x, y)
  ctx.lineTo(x + w, y)
  ctx.lineTo(x + w, y + h)
  ctx.lineTo(x, y + h)
  ctx.closePath()
}
// 顺时针绘制矩形
drawRectClockwise(50, 50, 320, 300)
// 逆时针绘制的圆
ctx.arc(140, 250, 60, 0, 2 * Math.PI, true)
// 逆时针绘制三角形
ctx.moveTo(280, 200)
ctx.lineTo(220, 300)
ctx.lineTo(340, 300)
ctx.closePath()

ctx.fillStyle = '#2486b9'
ctx.shadowColor = '#d0dfe6'
ctx.shadowOffsetX = 10
ctx.shadowOffsetY = 10
ctx.shadowBlur = 5
ctx.fill()
```

<iframe src="https://lastknightcoder.github.io/canvas-demos/74.html" height="430" width="100%"></iframe>