---
title: CSS：优雅的使用CSS处理图片加载处理
tags: [CSS, img, 错误处理]
---

今天看了张鑫旭的一个处理图片加载错误处理方法的视频，我觉得很是优雅。

## 常见做法

监听 `onerror` 事件，当图片加载出错时，触发该事件，然后我们替换为一张兜底图

```js
<img src='xxx.png' onerror='this.src="break.svg"' />
```

```css
img[src$="break.svg"] {
  /* 个人疑惑，为什么是 contain */
  object-fit: contain;
}
```

这样做的缺点是只有一张裂开的图，用户不知道<span class="comments red">这张图表达的含义是什么</span>，对用户而言，内容是比视觉体验重要的。

## 基于 CSS 的方法

使用伪元素 `::before` `::after` 来展示兜底图和提示内容，<span class="comments green">`img` 有一个特性是能正常加载图片时，`::before` 和 `::after` 是不会显示出来的</span>。

```html
<img
  src="zxx.png"
  alt="提示内容"
>
```

```note
一定要写 `alt` 属性。
```

```css
img {
  position: relative;
  /* 下面这行的意义是什么 */
  transform: scale(1); 
}

img::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: url(break.svg) no-repeat center / 50% 50% #f5f5f5;
  color: transparent;
}

img::after {
  content: attr(alt);
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  line-height: 2;
  background-color: rgba(0, 0, 0, .5);
  color: white;
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

既能呈现视觉效果，也能显示内容。

## 参考

- [图片加载出错样式最佳实践](https://www.bilibili.com/video/BV1rG41137tu)