---
tags: [SVG, CSS]
---

之前我们为 `SVG` 元素设置样式是通过属性样式设置的，如

```html
<rect fill="red" />
```

除了通过属性样式（或者称为表示属性），还可以通过 `CSS` 对 SVG 元素进行设置，样式也分为三种

- 内联样式
- 内部样式
- 外部样式

``` danger 注意
这三种样式是针对 SVG 文件而言的，而不是内联 SVG，对于内联 SVG，将它当作正常的 DOM 元素看待即可。
```

## 内联样式

内联样式就是通过 `style` 属性为其指定样式

```html
<rect style="fill: red;" />
```

## 内部样式表

可以在 `<svg>` 标签内部定义 `<style>` 标签，在其中进行样式设置

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <style type="text/css">
    rect {
      fill: red;
    }
  </style>
</svg>
```

## 外部样式表

假设有如下 `svg.css` 文件，我们可以在 `.svg` 中引用它，在 `<svg>` 标签的前一行通过如下语句引用

```html
<?xml-stylesheet href="/path/to/svg.css" type="text/css"?>
<svg xmlns="http://www.w3.org/2000/svg">
  <!-- ... -->
</svg>
```

```` tip
优先级顺序为：

```text
内部样式 > 内部样式表 = 外部样式表 > 属性样式 > 继承样式
```

属性样式的优先级只比继承样式高。

````
