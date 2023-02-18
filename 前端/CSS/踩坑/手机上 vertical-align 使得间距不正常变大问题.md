## 问题

使用 `vertical-align: middle` 来使得行内元素居中对齐

```html
<div class="container">
  <span class="item1"></span>
  <span class="item2"></span>
</div>
```

```css
.item1 {
  // ...
  vertical-align: middle;
}
.item2 {
  // ...
  vertical-align: middle;
}
```

在浏览器上显示正常，但是在手机上会使得行间距不正常的变大，如下

<img src="https://img.alicdn.com/imgextra/i2/O1CN019PaaxW1Hl4R1xhnLc_!!6000000000797-2-tps-1080-1087.png" style="zoom: 40%;"/>

目前不知道原因。

## 解决办法

对父容器使用 `flex` 布局

```css
.container {
  display: flex;
  align-items: center;
}
```

在浏览器和手机上均显示正常，如下

<img src="https://img.alicdn.com/imgextra/i2/O1CN01PemfI421J6ZspDV1B_!!6000000006963-2-tps-1080-1106.png" style="zoom: 40%" />

