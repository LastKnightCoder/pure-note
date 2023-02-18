---
tags: [GSAP, 动画, JavaScript]
---

## GSAP 介绍

引用官网的一段话

> Animation ultimately boils down to changing property values many times per second, making something appear to move, fade, spin, etc.

动画的实质就是在一秒内多次改变属性值，GSAP 就是做这个工作的，在给定起始值和终止值时，GSAP 可以在 1s 内做 60 次插值。

GSAP 可以对以下属性进行插值：

- CSS 属性，例如 `width`, `height`, `borderRadius`, `color`
- SVG 属性，例如 `viewBox`, `stroke`, `fill`，可以通过额外的插件对路径进行变形动画
- 任何数值，GSAP 可以对任意对象中的数值属性进行插值

## 引入 GSAP

通过 script 标签引入 CDN，然后就可以在代码中访问 `gsap` 了

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.10.4/gsap.min.js"></script>
<script>
  gsap.to(".box", {
    x: 200,
    duration: 1,
  });
</script>
```

通过 npm 引入

```bash
npm install gsap
```

```js
import gsap from "gsap";
gsap.to(".box", {
  x: 200,
  duration: 1,
});
```

## Tween

```info
`Tween` 我不知道怎么翻译，姑且当作时插值动画。
```

### gsap.to

`gsap.to(target, obj)` 方法可以对 `target` 进行动画，例如

```js
gsap.to(".box", {
  x: 200,
  duration: 1,
});
```

上述代码表示选择含有类名为 `box` 的元素，将其向右平移 `200px`，持续时间为 `1s`

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo01.html" allowFullScreen width="100%" height="130"></iframe>

`target` 表示需要被动画的对象，可以有如下几种取值：

- CSS 选择器，例如 `.box, #box, div p`

  ```tip
  CSS 选择器可以选中多个 DOM 元素，这些元素会被应用相同的动画。
  ```
- DOM 对象
- JavaScript 对象，可以对对象中的数值属性进行插值，通过 `onUpdate` 方法可以拿到当前的数值
- 数组，数组的元素可以是如上三种

`obj` 是一个对象，它包含了你想改变的属性，例如 `width`，`color`，同时也包含一些与动画相关的属性，例如 `duration`，`ease`，`onUpdate` 等。

```note
当我们改变 CSS 属性时，属性名要变为驼峰命名，如 `background-color -> backgroundColor`，`border-radius -> borderRadius`。
```

#### 例子 1：CSS 选择器

```html
<style>
  .box {
    width: 100px;
    height: 100px;
    background-color: #93b5cf;
  }
  .box:nth-child(2) {
    background-color: #74787a;
  }
</style>
<div class="box"></div>
<div class="box"></div>
<script src="./gsap.js"></script>
<script>
  gsap.to(".box", {
    x: 200,
    duration: 1,
  });
</script>
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo02.html" allowFullScreen width="100%" height="230"></iframe>

有两个 DOM，其类名都为 `box`，因此当使用 `gsap('.box', obj)` 时，两个元素都会被选中，然后对其 CSS 属性进行插值，从而产生动画。

#### 例子 2：DOM 对象

```html
<style>
  #box {
    width: 100px;
    height: 100px;
    background-color: #93b5cf;
  }
</style>
<div id="box"></div>
<script src="./gsap.js"></script>
<script>
  const box = document.getElementById("box");
  gsap.to(box, {
    x: 200,
    duration: 1,
  });
</script>
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo03.html" allowFullScreen width="100%" height="130"></iframe>

#### 例子 3：JavaScript 对象

```html
<div id="box">1</div>
<script>
  const obj = {
    num: 1,
  };
  const box = document.getElementById("box");
  gsap.to(obj, {
    num: 100,
    duration: 1,
    onUpdate: function () {
      box.innerHTML = Math.round(obj.num);
    },
  });
</script>
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo04.html" allowFullScreen width="100%" height="60"></iframe>

#### 例子 4：数组

```html
<style>
  .box {
    width: 100px;
    height: 100px;
    background-color: #93b5cf;
  }
  #box {
    width: 100px;
    height: 100px;
    background-color: #74787a;
  }
</style>
<div class="box"></div>
<div class="box"></div>
<div id="box"></div>
<script>
  gsap.to([".box", document.getElementById("box")], {
    x: 200,
    duration: 1,
  });
</script>
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo05.html" allowFullScreen width="100%" height="330"></iframe>

````note
被插值的属性一定要存在于被动画的对象中，否则会报错

```js
const obj = {n: 1}
gsap.to(obj, {
  // obj 上不存在 nn 属性
  nn: 10,
  duration: 1,
})
```

这个时候你会提出疑问，HTML 对象上也没有 `x` 属性，为什么不会报错，这是因为 `gsap` 使用了一个叫 `CSSPlugin` 的插件进行了处理，在 `gsap` 的源码中，存在这么一行

```js
gsap.registerPlugin(CSSPlugin)
```

正是这个插件，将 `x` 简写对应于到了 CSS 中的 `transform` 属性，CSSPlugin 提供如下简写

| 简写 | CSS属性 |
| :--- | :--- |
| `x: 100` | `transform: translateX(100px)` |
| `y: 100` | `transform: translateY(100px)` |
| `rotation: 360` | `transform: rotate(360deg)` |
| `rotationX: 360` | `transform: rotateX(360deg)` |
| `rotationY: 360` | `transform: rotateY(360deg)` |
| `skewX: 45` |	`transform: skewX(45deg)` |
| `skewY: 45` |	`transform: skewY(45deg)` |
| `scale: 2` |	`transform: scale(2, 2)` |
| `scaleX: 2` |	`transform: scaleX(2)` |
| `scaleY: 2` |	`transform: scaleY(2)` |
| `xPercent: -50` |	`transform: translateX(-50%)` |
| `yPercent: -50` |	`transform: translateY(-50%)` |

````

### gsap.from

`gsap.to` 表示从当前状态到目标状态，而 `gsap.from` 表示从目标状态到当前状态，它接收的参数同 `gsap.to`

```js
gsap.from('.box', {
  x: 300,
  opacity: 0,
  duration: 1,
})
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo06.html" allowFullScreen width="100%" height="130"></iframe>

### gsap.fromTo

`gsap.fromTo(target, start, end)` 可以指定初始状态和终止状态

```javascript
gsap.fromTo('.box', {
  opacity: 0,
  x: 100
}, {
  x: 300,
  opacity: 1,
  duration: 1,
})
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo07.html" allowFullScreen width="100%" height="130"></iframe>

### gsap.set

当你不需要动画效果时可以使用 `gsap.set()`，它就相当于动画时长为 0 的"动画"

```js
gsap.set('.box', {
  x: 200
})
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo08.html" allowFullScreen width="100%" height="130"></iframe>

## Timeline

当你需要组织复杂的动画的时候，比如序列动画，动画依次执行，这个时候就需要使用 `Timeline`。`Timeline` 可以看作是**多个 `Tween` 的容器**，这些 `Tweens` 被放置在不同的时间上，因此使用 `Timeline` 很方便的制作出 PPT 动画的效果。

### 序列动画

使用 `gsap.timeline()` 可以创建一个 `Timeline` 对象，通过 `Timeline` 对象的 `add` 方法，可以添加 `Tween` 至 `Timeline` 中，被添加进 `Timeline` 的 `Tween` 会依次执行（前一个动画执行完，执行后一个）。

```javascript
const tl = gsap.timeline();
tl.add(gsap.to('.box1', {
  x: 200,
  duration: 1,
}))
tl.add(gsap.to('.box2', {
  x: 200,
  duration: 1,
}))
```

<iframe src="https://lastknightcoder.github.io/GSAP-demos/demo09.html" allowFullScreen width="100%" height="230"></iframe>

````tip
`tl.add(gsap.to())` 可以简写为 `tl.to()`
```js
tl.to('.box', {
	x: 100,
	duration: 1,
})
```

并且支持链式调用

```js
tl.to('.box1', {
	x: 100,
	duration: 1,
})
.to('.box2', {
	x: 100,
	duration: 1,
})
```
````

### 默认值

在创建 `Timeline` 对象时，可以传入一个对象

## 动画属性

- [ ] ease
- [ ] duration
- [ ] delay
- [ ] repeat

## 动画控制

- [ ] play
- [ ] pasue
- [ ] reverse
- [ ] resume