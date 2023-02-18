---
tags: [踩坑, iframe, 全屏, fullscreen]
---

有一个页面，我给它注册了键盘事件，当按下 `f` 时间可以进入/退出全屏

```js
document.addEventListener('keydown', e => {
  if (e.key == 'f') {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen()
    } else {
      document.exitFullscreen()
    }
  }
})
```

然后希望在另一个页面中通过 `iframe` 嵌入这个页面

```html
<!-- another page -->
<iframe src="page.html"></iframe>
```

但是当我按下 `f` 时，这个 `iframe` 并没有被全屏，后来经过搜索，需要为 `iframe` 添加上 `allowFullScreen` 属性

```html
<iframe src="page.html" allowFullScreen></iframe>
```

但是又有一个问题，当我全屏的时候，屏幕变为黑色了

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204261448272022-04-26-14-48-28.png" style="zoom:50%"/>

经过搜索，可以添加如下样式进行解决

```css
::backdrop {
  background-color: white;
}
```

参考：

- [iframe中的元素如何请求全屏](https://segmentfault.com/q/1010000004532869)
- [Background/element goes black when entering Fullscreen with HTML5](https://stackoverflow.com/questions/16163089/background-element-goes-black-when-entering-fullscreen-with-html5)