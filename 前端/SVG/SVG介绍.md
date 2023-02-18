---
tags: [SVG]
---

SVG 全称为 `Scalable Vector Graphics`（可缩放矢量图），它是一种图像格式，使用形状、路径等来组成图像内容，与一般由像素描述的图像不同，SVG 可以做到与分辨率无关的清晰度，无论放大多少倍，图像还是一样的清晰


| 缩放 | PNG | SVG |
| --- | --- | --- |
| 100% |  <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-12-33.png" /> | <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-11-45.svg" style="zoom:100%" /> |
| 150% | <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-12-33.png" style="zoom: 150%;" /> | <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-11-45.svg" style="zoom:150%" /> |
| 200% | <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-12-33.png" style="zoom: 200%;" /> | <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-11-45.svg" style="zoom:200%" /> |

因为 SVG 与分辨率无关，所以我们经常使用 SVG 制作 icon，可以在各个平台进行使用，不用准备 2x，3x 不同尺寸的图。

除此之外，SVG 还有很多优点：

- SVG 是文本文件，可以直接打开使用编辑器进行修改
- 相比于 PNG、JPEG、GIF，具有更小的尺寸
- SVG 中的文字可以被选中
- 支持动画，可以通过 SMIL 或者 CSS 使得 SVG 中的元素产生动画效果
- ...

SVG 作为一种图片，我们可以直接通过 `img` 或者在 `CSS` 中通过 `background-image` 进行引用

```html
<img src="xxx.svg">
```

```css
background-image: url(xxx.svg)
```

还可以通过 `embed` `object` `iframe` 来引用一个 SVG 图片

```html
<iframe src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-11-45.svg"></iframe>
```

<iframe src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/svg-Page-32022-01-14-10-11-45.svg"></iframe>

也可以直接在网页中内联 `<svg>` 标签来显示一幅图像

```html
<svg width="150" height="100" viewBox="0 0 300 200">
  <rect x="0" y="0" width="200" height="200" fill="#dae8fc" />
  <circle cx="200" cy="100" r="100" fill="#d5e8d4" />
</svg>
```

<svg width="150" height="100" viewBox="0 0 300 200">
  <rect x="0" y="0" width="200" height="200" fill="#dae8fc" />
  <circle cx="200" cy="100" r="100" fill="#d5e8d4" />
</svg>

关于 SVG 的具体语法会稍后介绍。